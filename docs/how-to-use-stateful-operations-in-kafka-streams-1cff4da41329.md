# 如何在 Kafka 流中使用有状态操作

> 原文：<https://betterprogramming.pub/how-to-use-stateful-operations-in-kafka-streams-1cff4da41329>

## 探索 Kafka Streams DSL API 中的有状态操作

![](img/81dfe9813db29c321ece36b6e9eabd2f.png)

内森·杜姆劳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

[](https://medium.com/better-programming/learn-stream-processing-with-kafka-streams-stateless-operations-2111080e6c53)[Kafka Streams](https://kafka.apache.org/documentation/streams/)API 博客系列的第一部分涵盖了无状态函数，如`filter`、`map`等。

[](https://medium.com/better-programming/learn-stream-processing-with-kafka-streams-stateless-operations-2111080e6c53) [## 使用 Kafka 流学习流处理

### 无状态操作

medium.com](https://medium.com/better-programming/learn-stream-processing-with-kafka-streams-stateless-operations-2111080e6c53) 

在这一部分中，我们将探索 Kafka Streams DSL API 中有状态的操作。我们将重点关注聚合操作，如`aggregate`、`count`和`reduce`，并讨论相关的概念。

# 聚合

聚合操作应用于相同关键字的记录。Kafka Streams 支持以下聚合:`aggregate`、`count`和`reduce`。正如上一篇文章中提到的，分组是聚合的先决条件。您可以在`KStream`或`KTable`上运行`groupBy`(或其变体)，这将分别产生`KGroupedStream`和`KGroupedTable`。

注意:`KTable`分组没有在无状态操作博客中讨论。

## 总计

`aggregate`功能有两个关键组件:`Initializer`和`Aggregator`。当接收到第一条记录时，调用`Initializer`并将其用作`Aggregator`的起点。对于随后的记录，`Aggregator`使用当前记录和计算的合计(到目前为止)进行计算。从概念上讲，这是在无限数据集上执行的有状态计算。它是有状态的，因为计算当前状态时考虑了当前状态(键值记录)和最新状态(当前聚合)。这可用于移动平均、求和、计数等情况。

下面是一个如何计算计数的示例(即特定密钥被接收的次数)。GitHub 上的[代码示例:](https://github.com/abhirockzz/kafka-streams-apis/tree/master/stateful-ops)

## 数数

`count`是一种常用的聚合形式，以至于它被作为一流的操作来提供。一旦您通过键(`KGroupedStream`)对流记录进行了分组，您就可以通过使用这个操作来计算特定键的记录数。

做事情的`aggregate`方式可以被一个单独的方法调用代替！

```
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> stream = builder.stream(INPUT_TOPIC);stream.groupByKey().count();
```

## 减少

您可以使用`reduce`来组合值流。前面提到的`aggregate`操作是`reduce`的推广形式。可以实现`sum`、`min`、`max`等功能。下面是一个`max`的例子:

注意:所有的聚合操作都忽略带有`null`键的记录，这是显而易见的，因为这些函数集的目标就是对特定键的记录进行操作。

## 聚合和状态存储

在上面的例子中，聚合值被推送到输出主题。不过，这不是强制性的。可以将聚合结果存储在本地状态存储中。这里有一个例子:

```
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> stream = builder.stream(INPUT_TOPIC);stream.groupByKey().count(Materialized.as("count-store"));
```

在这个例子中，对`count`的调用还创建了一个名为`count-store`的本地状态存储，然后可以使用交互式查询对其进行自省。

这些状态存储可以在内存中，也可以使用`RocksDB`存储在磁盘上。这考虑到了`scalability`,因为每个状态存储都本地存在于特定的 Kafka Streams 应用程序中，该应用程序处理来自主题的不同分区的输入。因此，整体状态分布在(潜在的)应用程序的多个实例中(除了`GlobalKTables`的情况)。另一个关键属性是`high availability`，因为这些状态存储的内容被备份到 Kafka 中作为`changelog`或`compacted`主题(尽管这可以被禁用)，这提供了高可用性。如果一个应用程序实例崩溃，它的状态存储内容可以从 Kafka 本身恢复。

## `KGroupedTable`

当在一个`KTable`上调用`groupBy` *操作时，获得一个`KGroupedTable`。就像`KGroupedStream`一样，拥有一个`KGroupedTable`是在一个`KTable`上应用聚合的先决条件。`aggregate`、`count`和`reduce`在`KGroupedTable`中的工作方式与它们在`KGroupedStream`中的工作方式相同。

但是有一个重要的区别需要强调。从概念上来说，`KTable`与`KStream`不同，因为它表示某个时间点的数据快照(非常像数据库表)。它是一个可变实体，与代表不可变+无限记录序列的`KStream`相反。为了考虑这种差异，`KGroupedTable`中的`aggregate`和`reduce`函数还添加了一个额外的`Aggregator`(通常称为减法器)，当更新一个键或获得一个`null`值时会调用该函数。

# 开窗术

有状态的 Kafka 流操作也支持`Windowing`。这允许您将流处理管道的范围限定在特定的时间窗口/范围内(例如，跟踪每分钟的链接点击量或每小时的唯一页面浏览量)。

要对一组记录执行`Windowed`聚合，您必须创建一个`KGroupedStream`(如上所述)，在`KStream`上使用`groupBy`，然后使用`windowedBy`操作(有两种重载形式)。您可以在传统窗口(翻滚、跳跃或滑动)或基于会话的时间窗口之间进行选择。

在一个`KGroupedStream`上使用`windowedBy(Windows<W> windows)`会返回一个`TimeWindowedKStream`，在此基础上您可以调用上述聚合操作。例如，如果您想要特定时间范围(比如五分钟)内的点击次数，请选择滚动时间窗口。这将确保记录在给定的时间范围内清楚地分开。换句话说，来自用户 1 的从上午 10:05 到 10:05 的点击将被单独聚集(计数),并且新的时间段(窗口)从上午 10:06 开始，在此期间点击计数器被重置为零并再次计数。

其他窗口类型包括:

*   `Tumbling`从不重叠的时间窗口。一条记录只能是一个窗口的一部分。
*   `Hopping`时间窗口，其中记录可以出现在一个或多个时间范围/窗口中。
*   `Sliding`时间窗口用于`Joining`操作。

还有另一种叫做`Joining`的有状态操作。这是一个广泛的主题，本身就值得写一整篇文章(或者另一个系列)。

如果您想考虑“会话”(由定义的非活动间隙分隔的活动周期)，请使用`windowedBy(SessionWindows windows)`，它返回一个`SessionWindowedKStream`:

Kafka Streams 博客系列的这一部分到此结束。请继续关注下一部分，它将演示如何使用内置的测试工具来测试 Kafka Streams 应用程序。

# 参考

请不要忘记查看以下卡夫卡流资源

*   [卡夫卡文献](https://kafka.apache.org/documentation/streams/)
*   [卡夫卡流 Javadocs](https://kafka.apache.org/24/javadoc/index.html?org/apache/kafka/streams/)