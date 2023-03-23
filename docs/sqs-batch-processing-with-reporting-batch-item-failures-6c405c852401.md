# 报告批项目失败的 SQS 批处理

> 原文：<https://betterprogramming.pub/sqs-batch-processing-with-reporting-batch-item-failures-6c405c852401>

## 看看 AWS 的部分批处理

![](img/992bbd0ca88aefb2d2c7a331ba69b042.png)

乔纳森·法伯在 Unsplash[上的照片](https://unsplash.com?utm_source=medium&utm_medium=referral)

在深入细节之前，我们先打好基础。我们可以使用 Lambda 函数来处理 Amazon 简单队列服务中的消息。

Lambda 轮询队列，并使用包含队列消息的事件同步调用您的 Lambda 函数；它成批地读取消息，并为每一批调用一次您的函数。成功处理后，它从队列中删除消息。现在，这里有几个参数可以根据以下要求帮助微调您的 Lambda:

批大小:每批中发送给函数的记录数。对于标准队列，这可能高达 10，000。默认值为 10。超过 10 秒时，`MaximumBatchingWindowInSeconds`参数必须设置为至少 1 秒。[调用有效负载大小配额](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html)会导致批量大小可能低于配置的大小。

批处理窗口:调用前收集记录的最长时间。如果批量大于默认值，则必须进行设置。

可见性超时:为了防止其他消费者再次处理该消息，亚马逊 SQS 设置了一个可见性超时，在此期间 SQS 阻止其他消费者接收和处理该消息。

推荐:可见性超时= 6 次函数超时+MaximumBatchingWindowInSeconds

# 问题陈述

假设我们将 SQS 的批处理大小设置为 100，可见性超时设置为 15 分钟。因此，在 15 分钟内，来自 SQS 的这 100 条消息将由 Lambda 函数处理，这些消息将从队列中隐藏，但如果在第 81 条消息后，它抛出一个错误，那么这 100 条消息将再次可见并可用于处理，尽管我们知道 80%的工作已经完成。

因此，正常配置要么完全成功，记录将从队列中删除，要么完全失败，将在可见性超时后保留以供重新处理。

# 解决办法

AWS 提出了[部分批处理](https://aws.amazon.com/about-aws/whats-new/2021/11/aws-lambda-partial-batch-response-sqs-event-source/)的概念。现在，我们可以只告诉 SQS 那些失败的记录，然后需要再次处理，它也可以删除所有成功的记录。

> 有了这个特性，当 SQS 队列中的消息处理失败时，Lambda 会将消息队列中的一批记录标记为部分成功，只允许重新处理失败的记录。

# 履行

为了展示这个特性的演示，我在 GitHub 中创建了一个资源库，可以帮助您理解我们如何实现这个特性。

```
git clone [https://github.com/surya5954/sqs-batch.git](https://github.com/surya5954/sqs-batch.git)
npm install 
sls offline start
```

这个项目有两个主要的 Lambda 函数:

1.  生产者:处理 POST API 调用，为 SQS 和批量发布准备主体
2.  消费者:使用报告批项目失败的逻辑处理来自 SQS 的批消息

# 生产者

这部分只是简单地从 aws-sdk 为 Node.js 使用 [sendMessageBatch](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SQS.html#sendMessageBatch-property) 方法向 SQS 发布消息。这个批处理版本的`SendMessage`一次最多只能发送十条消息。因此，如果我们想要发送比这更多的消息，我们需要将消息分成十个一组的数组。

我已经解决了这个问题，所以这就是生产者的 POST API 调用体的外观:

```
{
 "numberOfBatch": 5, // Default 10 messages per batch
 "delaySeconds": 0
}
```

现在，这将向 SQS 发送(`numberOfBatch` * 10)条消息，这些消息将进一步被消费者 Lambda 批量消费。

下面是为 SQS 准备消息的简单逻辑:

# 消费者

以下是面向 SQS 消费者的活动配置:

`BatchSize`和`maximumBatchingWindow`上面已经解释过了。现在，来到`ReportBatchItemFailure`，几个月前 AWS 引入的事件主角，有责任批量报告故障项目。

这个消费者的处理函数需要完成这个特性的所有繁重工作，尽管它并不那么繁重，如下所示:

这里需要注意两点:

首先，我使用了`Promise.allSettled`,它返回一个承诺，在所有给定的承诺都被实现或拒绝后，该承诺将被解析。我们不需要担心它们是否满足，它们会在可见性超时后自动从队列中删除。

第二，对于所有失败的消息(拒绝的承诺)，我们的处理函数需要有逻辑来捕捉所有的异常，并将它们推到一个数组中，看起来应该是这样的:

```
{ 
  "batchItemFailures": [ 
        {
            "itemIdentifier": "id2" // messageIds
        },
        {
            "itemIdentifier": "id4" // messageIds
        }
    ]
}
```

就是这样！

现在，只有失败的消息将重新出现在队列中进行处理，而成功的消息将从队列中删除。根据 AWS，这里的文档是基于你的处理函数的返回的一些成功和失败的条件，

空的和 Null 的`batchItemFailures`列表将被 Lambda 视为成功。

无效的 JSON 或`itemIdentifier`值(空字符串|| null ||错误的键名||无效的`messageId`)将被视为批处理完全失败。因此，在构建这个 JSON 时要小心。

我很感谢你花时间阅读这篇文章。关于这个主题有很多东西需要学习，我已经尝试包含有用资源的链接。

我希望你觉得这是有用的。

快乐学习！