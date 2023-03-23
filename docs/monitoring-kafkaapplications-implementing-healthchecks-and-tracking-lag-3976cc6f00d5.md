# 监控 Kafka 应用程序—实施健康检查和跟踪延迟

> 原文：<https://betterprogramming.pub/monitoring-kafkaapplications-implementing-healthchecks-and-tracking-lag-3976cc6f00d5>

## 对于卡夫卡消费者，溪流，春天-卡夫卡，卡夫卡-连接

![](img/268f0b06cdea3c7a8dcd4ab65c59425a.png)

[甄虎](https://unsplash.com/@zhenhu2424?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

微服务通常使用发送或回复心跳/健康检查的模型，作为向报告、调度或编排服务提供状态信息的一种方式。这些不仅在应用程序的正常生命周期中很重要，在新版本推出时也很重要。

我们将看看如何使用不同的库和抽象来实现健康检查，但首先，让我们就检查应用程序的健康时要考虑的问题达成一致。

# 健康对于 Kafka 应用意味着什么？

对于 Kafka 应用程序，健康检查应该在两种情况下故意失败:

*   与 Kafka 的通信有一个问题，这可能是临时的网络故障，异常长的处理时间导致心跳失败，消费者被踢出或无法提交。这些类型的错误导致重启是有意义的(每当一个实例被认为不健康时，编排系统就会这么做)，因为它们最有可能在重启时解决
*   该应用存在一个不容忽视的关键问题，需要在进一步处理之前解决。我的意思是，如果应用程序正在处理流数据，它不能跳过或错过单个记录。在这种情况下，如果应用程序或它与之交互和/或依赖的另一个应用程序中存在错误，即导致所有未完成的记录无法处理，则应用程序不应提交任何偏移并继续运行。唯一的解决办法是停止，一个简单的方法是报告不健康，重新启动，希望有人能快速修复这个错误。

在运行状况检查中，不应该考虑由于记录本身的问题(无论是格式、结构还是其他问题)而导致的处理单个记录的失败。这些小问题应该通过将这些记录发送到 DLQ(死信队列)、记录问题并发出警报来处理。如何处理 DLQ 的记录超出了本文的范围。

# 简单的卡夫卡式消费者

`KafkaConsumer`的实现并不真正提供开箱即用的“状态”,因此应用程序需要在整个生命周期中跟踪它——从订阅/分配主题和分区，到轮询、可能的暂停、恢复和关闭。在这些阶段中的每一个阶段以及它们之间的转换都会引发异常。这种情况的好处和坏处是，作为开发人员，您可以完全控制这些关键时刻，因此正确处理它取决于您——捕捉任何与 Kafka 相关的异常，记录它，发出警报，并更改缓存状态，用作健康指标。

这里要注意的关键是`KafkaConsumer`将在一个线程中运行，而检查健康的请求将在另一个线程中处理，所以要确保正确处理缓存的状态。

在我之前的一篇文章中，我详细描述了一个在这样的应用中处理多线程和状态的解决方案。本质上，应用程序需要保持一个状态，其中一个是错误。

```
enum State {
   CONSUMING,
   PAUSED,
   ...
   ERROR; fun isHealthy(): Boolean {
     return this != ERROR
   }
}
```

然后在你的健康检查终点，你需要做的就是检查`state.isHealthy()`。对于 Spring，它看起来会像这样:

```
class HealthCheckIndicator: HealthCheckIndicator {
 override fun health(): Health{
    return if(state.isHealthy()) { 
       Health.up() 
    } else {
       Health.down()
    }.build()
 }
```

# 卡夫卡溪流

在内部，`KafkaStreams`使用了一个普通的`KafkaProducer` 和`KafkaConsumer`，但是这个抽象增加了几个简洁的特性(在这个上下文中不仅仅是几个，而是几个有用的特性)——它提供了一个获取当前状态的方法，而且，调用它是线程安全的。

`KafkaStreams.State`有七种可能的值:

*   `CREATED` —在生命周期开始时
*   `RUNNING` —准备消费或正在消费
*   `REBALANCING` —消费群体正在重新平衡
*   `PENDING_SHUTDOWN` —从上述任一状态转换到`NOT_RUNNING`
*   `NOT_RUNNING` —通过调用`close()`作为正常生命周期的结果而停止
*   `PENDING_ERROR` —转换状态至`ERROR`
*   `ERROR` —水流无法自行恢复

最后两个可以报告为“关闭”或“不健康”

# 春天的卡夫卡溪流

Spring 实现甚至进一步脱离了底层概念，并提供了一种更好的跟踪状态的方式。成功创建流后，您可以注册一个`StateListener`。该接口实际上是一个消费者，在每次转换时接收流的新旧状态。

如果您的应用程序需要回复另一个轮询状态的服务，那么您不能直接使用侦听器，您仍然需要缓存健康并使用该变量来回复健康检查请求。

```
// create the 'stream'
stream.setStateListener{ newState, oldState -> 
   if (newState == ERROR || newState == PENDING_ERROR)
     healthy = false
}
```

但是，如果您应该在预定的时间间隔推送状态，那么您可以直接使用监听器实现。

正如您可能已经猜到的，状态的枚举与上一节提到的`KafkaStreams.State`相同。

# 卡夫卡-连接

Kafka 在一家数据驱动的公司中发挥着巨大的作用，但仅靠它是不够的。通常，有各种各样的内部和外部的“非卡夫卡”流需要与卡夫卡整合，Kafka-Connect 就出现了。它提供了一个非常丰富的生态系统，包括来自 S3、雪花、Mongo 等数据汇的现成连接器，甚至包括来自 SQL DB 的跟踪 CDC(变更数据捕获)。

每个连接器都有一组从源复制数据的任务，如果这些任务并行运行，可能会出现一个或多个失败。

目前没有提供现成的运行状况检查端点，但有一种方法可以扩展您的部署并自行添加。为此，您需要:

*   创建一个小的 Java 项目并添加`org.apache.kafka:connect-api:XXX`依赖项。
*   延长`ConnectRestExtension`并执行`register`方法:

```
@Override
public void register(ConnectRestExtensionContext restPluginContext){
  restPluginContext
    .configurable()
    .register(new HealthcheckController(new HealthcheckService(restPluginContext.clusterState())));
}
```

*   在控制器中定义一个端点:

*   在服务中实现实际运行状况检查的逻辑:

`ConnectorState`非常简单:

```
enum ConnectorState {
    HEALTHY,
    UNHEALTHY;

    public boolean isHealthy() {
        return this == HEALTHY;
    }
}
```

*   构建并把 jar 放到插件文件夹中(特定于部署)
*   将此添加到您的配置中以激活扩展:

```
rest.extension.classes=my.kafkaconnect.extension.HealthcheckRestExtension
```

这是最极端、最简单、坦率地说也是最不理想的实现，即使单个任务失败了，您也会报告`kafka-connect`不健康。当您开始开发和试验连接器时，这是一个很好的起点。然而，更好的处理方法是，一旦您有多个连接器，每个连接器都有多个任务，那么首先尝试恢复单个任务和连接器。

`Kafka-connect`公开端点以重新启动

*   名为`/connectors/<name>/restart`的连接器
*   通过连接器名称和任务 id 的任务`/connectors/<name>/tasks/<id>/restart`

一种方法是在`ConnectorState`枚举中添加第三个值，类似于`UNHEALTHY_TASKS`，访问所有这些信息的服务类可以返回一个包装类，包含连接器的名称和不正常任务的 id:

然后，扩展本身或另一个服务(不仅仅是通常会使用的 orchestrator 或 scheduler)可以使用健康检查来触发重启和警报。如果一个连接器或一个任务不健康，您可以剥离扩展中的一个线程来命中重新启动的端点，并在发出警报之前重试 N 次。

在这种情况下，只有当所有连接器都关闭并且发生了可怕的事情时，才会响应“不健康”。如果所有 N 次自我修复的尝试都失败了，并且您收到了一个警告，那么您可能需要以某种方式进行干预。

# 春天到了

如果你使用的是 Spring 的`HealthIndicator`,确保你要报告的健康状况可以“读取”响应，因为你可以在那里添加世界上所有的细节并报告任何状态，响应代码将是 200。如果业务流程或其他系统只依赖您回复 OK 来表示正常，回复 5XX 来表示不正常，那么请确保添加以下配置:

```
management:
  endpoint:
    health:
       status:
         http-mapping:
           UP:  200
           DOWN: 503 // or whatever fits
```

# 为什么要跟踪消费者滞后

消费者滞后实际上是来自消费者组的最后提交的偏移和最后可用于读取的偏移之间的差异。如果数据的生产速度远远超过其消费速度，或者消费者在处理传入消息时遇到问题，那么消费者群体就会滞后。

这可以用作性能指标。如果您已经就数据从源到达目的地的速度达成了一个 SLO(服务水平目标),但没有达到，那么快速查看一下消费者延迟警报或仪表板就会告诉您管道中的哪个应用程序是有罪的参与者。

另一个潜在的用例是，如果突然有大量数据涌入，而应用程序无法及时处理这些数据。这可能与季节性有关，或者某项业务是季节性的，或者它受到一次性事件的影响。在这些情况下，如果没有计划，您会注意到滞后图形中的尖峰，您可能需要手动更改配置，并在受影响的组中扩展您的使用者。

此外，延迟可能是处理过程中的一个错误的症状，只有在应用程序受到更大压力时才能发现。这将是非常危险的，因为应用程序正在积极处理，无论多慢，但根据错误的类型，它可能不会这样做。

此外，该应用程序甚至可以是“死的”，但虚假报告是好的，因此我才谈论健康检查。这不是最令人兴奋的工作，但没有人希望僵尸应用程序投入生产。

# 跟踪消费者滞后—第三方云提供商

如果您使用云提供商，他们很可能会为您提供一个端点来获取该指标，并将其绑定到您选择的可视化和警报工具。

例如，汇合通过 REST API 提供了各种度量。您可以在不同的粒度级别获得关于消费者滞后的信息——仅针对每个组、每个组和主题，甚至每个组、主题和分区。这是他们网站上的查询示例:

你可以在这里查找其他特定于汇合的方法来跟踪延迟[https://docs . Confluent . io/cloud/current/monitoring/monitor-lag . html](https://docs.confluent.io/cloud/current/monitoring/monitor-lag.html)

# 跟踪消费者滞后— **程序化**

如果您有 Kafka 的内部部署，并且您需要开发您自己的度量报告服务，那么您可以通过`AdminClient` API 以编程的方式完成这项工作。

下面是 Kotlin 中最压缩的例子，不考虑异常处理和返回格式，只是为了演示这个想法:

# 跟踪消费者延迟— CLI

这更像是一种局部的、间歇使用的解决方案，但值得一提的是:

```
kafka-consumer-groups \
  --bootstrap-server localhost:9092 \
  --group my_group \
  --describe
```

这将返回以下格式的信息:

```
GROUP TOPIC PARTITION CURRENT-OFFSET LOG-END-OFFSET LAG CONSUMER-ID HOST CLIENT-ID
```

还有更多与数据管道整体健康状况相关的指标，但这两个指标可能是您设置的前两个。

希望你能在这里找到有用的东西。感谢阅读！