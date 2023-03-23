# 用 AWS Lambda 监控 Kafka 主题消费者滞后

> 原文：<https://betterprogramming.pub/monitoring-kafka-topic-consumer-lag-with-aws-lambda-b6638c7da135>

## Kafka 消费者滞后监测与合流计量 API

![](img/aef27c0b2aef69b20139fb88204a9c37.png)

照片由[纳温·丰塞卡](https://unsplash.com/@cnf271?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/@cnf271?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

最近在我的工作中，我们遇到了几个服务级别的问题，导致 Kafka 的消息不能被 Kafka 的消费者服务和适当地处理。为了识别这种情况，我们决定提出一种机制，在这种机制中，我们可以获得关于 Kafka 主题消费者滞后的及时更新。我们正在使用由 [Confluent](https://www.confluent.io/) 提供的 Kafka 管理的集群。

在我们最初的研究中，我们发现 Confluent 提供了几个 API 端点来使用它们的[度量 API](https://docs.confluent.io/cloud/current/monitoring/metrics-api.html)检索集群级信息。

# **融合度量 API**

`consumer_lag_offset`指标可以使用公开可用的指标 API 从每个 Kafka 主题中检索消费者滞后信息。要使用 Confluent 的 Metric APIs，了解他们自己的“表达性查询语言”是必须的。使用自定义查询，我们可以灵活地从提供的时间范围过滤数据。

在我解释如何在 Lambda 函数中使用`consumer_lag_offset`指标之前，最好先了解一下如何使用一个示例查询来检索消费者滞后补偿。

从汇合中检索消费者滞后的示例查询

在接下来的步骤中，我将简要解释每个查询参数。让我们从聚合开始。

*   `aggregations`:该字段用于指定要查询的指标。在撰写本文时，每个请求只允许一个指标。
*   `filter`:该字段定义过滤参数。`filter`字段带有几个子参数。如下:
    –`op`:定义操作员参数。例如:EQ，FT，GTE，AND，OR，NOT
    –`field`:定义要过滤的字段。例如:卡夫卡集群(`resource.kafka.id`)。过滤字段的完整列表可在[这里](https://api.telemetry.confluent.cloud/docs#section/Object-Model/Resources)找到。
    –`filter`:定义单个指标过滤器
    –`filters`:当你需要使用一组过滤器时，这个参数就派上用场了
    –`value`:要过滤的值。例如:Kafka 集群 Id ( lkc-1qwe3)
*   `group_by`:定义如何标记响应数据。在上面的示例查询中，我决定使用`consumer_group_id`和`topic`对响应进行分组。group_by 标签的完整列表可以在[这里](https://api.telemetry.confluent.cloud/docs#section/Object-Model/Labels)找到。
*   `granularity`:定义执行指标查询的时间范围。时间范围是预定义的，我在上面的示例查询中使用了 PT5M 粒度。其他一些粒度是 PT1M、PT15M、PT30M、PT1H 等。粒度与区间参数紧密相关。因此，粒度受到查询间隔长度的限制。例如，如果我使用 PT5M 粒度，我的查询的最大间隔长度应该小于 24 小时(1 天)。
*   `intervals`:定义我们计划运行查询的时间范围。ISO-8601 标准中应给出时间范围。
*   `limit`:定义查询应该返回的最大结果数。

一旦您构建了定制查询(请求体)，我们就可以向 Metric API 端点发出 API 请求。然而，在发出 API 请求之前，我们还需要做一步就是 API 认证。为了验证我们的 API 请求，我们必须创建一个 API 密钥对。访问密钥可以使用汇合器的[控制台](https://confluent.cloud/)或通过[汇合器命令行界面](https://docs.confluent.io/confluent-cli/current/install.html)创建。

在汇合中创建云 API 密钥的步骤。

*   登录合流控制台。
*   导航到管理部分。
*   选择云 API 密钥
*   选择添加密钥
*   最好对我们将要创建的这些键进行粒度访问。
*   给你的钥匙贴上标签。
*   将您的 API 密钥和秘密密钥保存在安全的地方，因为在初始创建之后您将无法检索秘密密钥。

生成的 API Key 需要作为请求头添加到 API 请求中，请确保对 API Key 进行 [base64 编码](https://www.base64decode.org/)。

为 Kafka 集群“lkc-1qwe3”检索`consumer_lag`的样本 CURL 请求如下所示:

```
curl --location --request POST '[https://api.telemetry.confluent.cloud/v2/metrics/cloud/query'](https://api.telemetry.confluent.cloud/v2/metrics/cloud/query') \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic QVBJX0tFWTpBUElfU0VDUkVUX0tFWQ==' \
--data-raw '{
    "aggregations": [
        {
            "metric": "io.confluent.kafka.server/consumer_lag_offsets"
        }
    ],
    "filter": {
        "op": "OR",
        "filters": [
            {
                "field": "resource.kafka.id",
                "op": "EQ",
                "value": "lkc-1qwe3"
            }
        ]
    },
    "group_by": [
        "metric.consumer_group_id",
        "metric.topic"
    ],
    "granularity": "PT5M",
    "intervals": [
        "2022-05-23T18:05:22.65+05:30/2022-05-23T18:10:22.65+05:30"
    ],
    "limit": 100
}'
```

您可以通过更改认证和其他特定参数(如`resource.kafka.id`、`intervals`)来尝试上述请求。在成功执行请求后，您应该会得到与我收到的以下内容类似的响应:

```
{
    "data": [
        {
            "timestamp": "2022-05-23T12:40:00Z",
            "value": 42.0,
            "metric.topic": "TEST_TOPIC_1",
            "metric.consumer_group_id": ".test-consumer-1"
        },
        {
            "timestamp": "2022-05-23T12:40:00Z",
            "value": 8.0,
            "metric.topic": "TEST_TOPIC_2",
            "metric.consumer_group_id": ".test-consumer-2"
        }
    ]
}
```

# 让我们开始吃吧

既然我们对消费者滞后指标 API 请求和响应有了清晰的概念，让我们继续实际的实现，在这里我使用 AWS Lambda 和 AWS Eventbridge 向 Slack 发送通知。下图应该会让你清楚地了解我们从现在开始努力实现的目标。

![](img/103dfd813becf16bc1632883b9b09ebf.png)

Kafka 消费者滞后通知—高级图片

如上图所示，我将使用无服务器框架创建一个 lambda 函数，并将其与 Eventbridge 规则一起部署到 AWS 中。预定的 Eventbridge 规则将定期调用 lambda 函数。Lambda 函数将调用 Confluent 的 Metric API 来收集消费者滞后数据。

如果滞后数据超过预定义的阈值，Lambda 功能将向预配置的松弛信道发送通知。

## 带有 Eventbridge 的 AWS Lambda

首先，我从这里的[创建了一个简单的 java spring boot 应用程序。完整的代码可以在我的](https://start.spring.io/) [GitHub](https://github.com/cnf271/serverless-kafka-consumer-lag-monitor) 资源库中找到。

在下面的外观中，一旦我们部署到 AWS Lambda 中，`evaluateTopicConsumerLag()`就会在预定义的时间间隔内被调用。`evaluateTopicConsumerLag()`方法最初使用提供的凭证从汇合度量 API 中检索 Kafka 主题消费者滞后数据。随后，根据预定义的阈值评估消费者滞后数据。之后，被标记的消费者被一个接一个地送到空闲通道。

## 使用 Slack webhook APIs 发送通知

正如介绍中提到的，我将向一个松弛通道发送通知警报。您可以决定如何接收通知。因此，在下面的步骤中，我将解释如何创建一个 Slack webhook 端点来向 Slack 通道发送警报。

您可以自由使用现有的松弛信道或创建新的信道来接收警报。我创建了一个专门的频道#kafka-consumer-lag-alerts 来接收消费者滞后提醒。

创建一个 Slack 应用程序和一个 Webhook 端点。

导航到 Slack Web [应用控制台](https://api.slack.com/apps)，如果没有应用，创建一个新的应用。我在我的个人 Slack 工作空间中创建了一个名为 alerts 的新应用。

![](img/fe6960f1e350bd590440c229d324fc16.png)

现在让我们给这个应用程序添加一个频道。导航到 Incoming Webhooks 功能，并按照所需步骤添加频道。

![](img/2d3ec0263d51a40cdaebdc3ea36132fc.png)

我在应用程序中使用了 Webhook URL 作为环境变量`SLACK_WEBHOOK_URL`。请参考 [GitHub](https://github.com/cnf271/serverless-kafka-consumer-lag-monitor) 库来检查 Slack 实现。

## 在 AWS 中部署 Lambda 函数

在我们可以将 Lambda 函数部署到云中的许多方法中，我大部分时间使用[无服务器框架](https://www.serverless.com/)，因为它在配置上很简单。

如果您尚未安装无服务器框架，请参考本[指南](https://www.serverless.com/framework/docs/getting-started)在您的本地机器上安装无服务器框架。安装框架后，您需要做的就是在我们的应用程序的根目录中添加 serverless.yml 类。我们可以在`serverless.yml`文件中包含与应用程序相关的配置级别信息，比如应用程序运行时、部署函数的区域和环境变量。

参考上面的文件，我已经使用了无服务器框架中的环境特性来将变量传递给我的应用程序。yml 类从。env 文件，并将它们传递给应用程序。一旦我们部署了应用程序，我们就可以在 lambda 控制台中检查这些值。

请注意，为了创建本教程，我将敏感信息如`KAFKA_MONITOR_API_KEY`和`KAFKA_MONITOR_SECRET_KEY`作为环境变量传递。我不建议你在实际实现的时候这么做，我强烈建议使用 [AWS KMS 管理器](https://docs.aws.amazon.com/lambda/latest/dg/configuration-envvars.html)来保护你的环境变量。此外，您可以使用 [AWS 参数存储库](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)或 [AWS 秘密管理器](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html)来存储和检索敏感信息。

下面的代码片段将创建一个 AWS Eventbridge 规则，它每五分钟调用一次 Lambda 函数。

```
events:
  - schedule: rate(5 minutes)
```

现在一切就绪，让我们将我们的功能部署到 AWS 中。在通过 CLI 将此功能部署到 AWS 之前，请确保您已经在本地配置了[AWS 凭证。](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)

让我们使用下面的命令构建我们的 Java maven 项目。下面的命令将在目标目录中创建一个可执行的 jar，它已经在 serverless.yml 中作为打包的工件使用。

```
mvn clean install
```

让我们在根目录中执行以下命令，将我们的功能部署到 AWS 中:

```
serverless deploy
OR
sls deploy
```

如果我们在应用程序的实现或配置过程中没有搞砸任何事情，上面的命令应该将我们的功能部署到 Lambda 控制台中。

![](img/df6579576ffb94aa8bcb2c073a4da414.png)

SLS 部署的执行结果

让我们回到 AWS 来检查我们的应用程序在 Lambda 控制台上是否可见。

![](img/cea0e716a1fe802cd139a119d815cc76.png)![](img/b837b1513604f36fb2dd5a898ad860af.png)

带有 EventBridge 规则的 Lambda 函数

上图显示，lambda 函数已经成功部署了五分钟间隔 Eventbridge 规则。

# 让我们测试 Lambda 函数

根据我们添加的 Eventbridge 规则，我们的函数每五分钟调用一次。然而，出于测试目的，我们可以使用 Lambda 控制台提供的测试功能来调用 Lambda 函数。

在一次成功的测试调用之后，我收到了下面的 slack 消息。您可以自由地将[提醒](https://emoji.slack-edge.com/TL6QD3196/alert/940122083239eb24.gif)图标添加到您的 Slack 表情集合中。

![](img/622baea41c4baca8ea315909c51aaec5.png)

本教程的完整代码可以通过 [GitHub](https://github.com/cnf271/serverless-kafka-consumer-lag-monitor) 访问。

# **参考文献**

1.  融合云指标-[https://docs . confluent . io/Cloud/current/monitoring/Metrics-API . html](https://docs.confluent.io/cloud/current/monitoring/metrics-api.html)
2.  slack Web API-【https://api.slack.com/web 
3.  无服务器框架文档-【https://www.serverless.com/framework/docs 