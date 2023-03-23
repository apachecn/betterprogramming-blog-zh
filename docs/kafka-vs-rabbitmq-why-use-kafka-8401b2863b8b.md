# 卡夫卡 vs. RabbitMQ:为什么要用卡夫卡？

> 原文：<https://betterprogramming.pub/kafka-vs-rabbitmq-why-use-kafka-8401b2863b8b>

## 所有的数据流服务都是平等的吗？

![](img/30aa73c034aeb453295ef2cfebc89ca8.png)

利瓦伊·琼斯在 [Unsplash](https://unsplash.com/s/photos/data?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

成功完成任何项目的一个重要部分是选择正确的工具来执行重要的基本功能。对于开发人员来说，有几个消息服务可供选择总是一个挑战。

一个悬而未决的关键问题是使用 [Apache Kafka](https://kafka.apache.org/) 或 [RabbitMQ](https://www.rabbitmq.com/) 服务。这两个平台都有几个功能和用例，可以帮助用户做出明智的决策。

Apache Kafka 和 RabbitMQ 是消息服务领域的两个顶级平台。尽管两个平台处理消息的方式不同，但不同之处在于它们所选择的架构、设计和交付方法。

# 但是什么是阿帕奇卡夫卡和 RabbitMQ 呢？

Apache Kafka 和 RabbitMQ 是开源平台，用于流数据，并配备了商业支持的 [pub/sub](https://www.rabbitmq.com/tutorials/tutorial-three-ruby.html) (我们将在后面描述)系统，由几家企业使用。

## 什么是阿帕奇卡夫卡？

简单地说，Apache Kafka 是一个为高访问数据重放和流而优化的消息总线。健壮的消息代理允许应用程序不断处理和重新处理流数据。

开源平台使用一种简单易用的路由方法，使用路由键向主题发送消息。Kafka 工具于 2011 年推出，是为流媒体设置而创建的。

## 什么是 RabbitMQ？

RabbitMQ 是一个全面的消息传递代理，支持高级消息队列协议(AMQP)、MQ 遥测传输(MQTT)和简单(或流)面向文本的消息传递协议(STOMP)等协议。

RabbitMQ 可以处理寻求高效率的用例，比如处理在线支付。RabbitMQ 还可以作为微服务之间的消息代理。

RabbitMQ 于 2007 年推出，最初是消息传递和 SOA 系统中的主要元素。如今，它的扩展角色还包括流用例。

因此，如果您正在考虑是使用 Apache Kafka 还是 RabbitMQ，请继续阅读，了解架构、方法的差异以及它们的性能优缺点。

# 架构差异

## 阿帕奇卡夫卡建筑

Apache Kafka 架构使用大量的发布-订阅消息和快速可伸缩的流平台。与日志一样，健壮的消息存储库利用服务器集群来保存主题(类别)中的若干记录。

所有 Kafka 消息都有一个密钥、值和时间戳。智能消费者或哑代理模型不尝试跟踪消费者消息，只保留未读消息。Apache Kafka 保存了定义时间范围内的所有消息。

## RabbitMQ 架构

RabbitMQ 体系结构利用了一个全方位的消息代理，它包含了点对点、请求/回复和发布/订阅通信设计的变化。

使用哑消费者和智能代理方法允许以与监视消费者状态的代理相似的速度向消费者可靠地传递消息。

通过使用同步或异步通信方法，该平台为几个插件提供了足够的支持，包括。NET、客户端库、Java、Node.js、Ruby 等等。

它还提供了分布式部署场景以及多节点集群到集群的联合，完全不依赖外部服务。

通过 [RabbitMQ](http://kth.diva-portal.org/smash/get/diva2:813137/FULLTEXT01.pdf) ，发布者可以将消息传送到交易所，并由消费者从队列中检索消息。通过交换将生产者从线路中分离出来，保证了生产者不会被硬编码的路由选择所困扰。

# 发布/订阅(发布/订阅)

发布/订阅是异步消息传递的主要消息传递模式之一，异步消息传递是一种消息传递方案，在这种方案中，消费者将消息的生成与处理分离开来。

## 阿帕奇卡夫卡

在 Apache Kafka 中，该平台是为高容量发布-订阅消息和流创建的，旨在持久、快速和可伸缩。本质上，Kafka 提供了一个可持续的消息存储和一个服务器集群。

## 兔子 q

在 RabbitMQ 中，设计需要一个全面的消息代理，使用点对点、请求/回复和发布/订阅通信风格模式的几种变体。

# 推/拉模式

## 阿帕奇·卡夫卡:基于拉的方法

Kafka 使用拉模型，在该模型中，消费者从指定的偏移量开始批量发出消息请求。Apache Kafka 还允许长池，当没有消息通过偏移量时，它会停止循环。

由于 Apache Kafka 的分区，拉模型对于它来说仍然是合理的。它的平台可以在没有竞争消费者的情况下在一个障碍内提供消息顺序。

这种方法允许用户利用消息批处理来实现高效的消息传递和更高的吞吐量。

## RabbitMQ:基于推送的方法

RabbitMQ 将消息推送给消费者，其中包括一个预取极限配置，对于防止消费者被多个消息淹没至关重要。

它们对于低延迟消息传递也很有用。push 方法的目的是快速分发单个消息，同时保证所有消息均匀地并行化，并在有序队列中处理消息，通常是在消息到达时。

# 用例

## 阿帕奇卡夫卡

Apache Kafka 本身提供了一个额外的代理，这是它最著名的地方，也是平台的一个流行元素。额外的代理是有预谋的，并在流处理设置的方向上进行营销。

此外，Kafka Streams 的加入可以作为 Apache Flink、Apache Spark、Google Cloud 数据流和 Spring Cloud 数据流等流媒体平台的替代方案。

出色的[用例](https://kafka.apache.org/uses)文档提供了 Apache Kafka 的详细用例，包括提交日志、事件源、日志聚合、度量、web 活动跟踪以及更多任务。

## 兔子 q

RabbitMQ 提供了一个全面的消息传递解决方案，在 web 服务器对请求的及时响应中广泛使用，以取代在用户等待结果时执行资源密集型措施的强制响应。

RabbitMQ 也非常适合将消息分发给多个接收者，因为它提供了许多用于可靠交付的[特性](http://www.rabbitmq.com/confirms.html)、联合、管理工具、路由、安全和[其他功能](http://www.rabbitmq.com/features.html)。

在附加软件的帮助下，RabbitMQ 还可以有效地处理几个重要的用例。与 Apache Cassandra 结合可以提供对流历史的访问，或者与用于访问“无限”队列的 [LevelDB](https://github.com/google/leveldb) 插件结合。

# 结论

Apache Kafka 和 RabbitMQ 这两个平台都提供了各种旨在满足大量需求的关键服务。

RabbitMQ 对于需要低数据流量的简单用例已经足够了。此外，使用 RabbitMQ，其他额外的好处包括灵活的路由前景和优先级队列选项。

另一方面，如果所提出的用例具有对海量数据和高流量的需求，那么 Apache Kafka 是值得考虑的。