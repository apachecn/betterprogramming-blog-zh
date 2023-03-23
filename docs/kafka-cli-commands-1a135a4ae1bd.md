# 用于日常编程的 15 个 Kafka CLI 命令

> 原文：<https://betterprogramming.pub/kafka-cli-commands-1a135a4ae1bd>

## 演示最常用的 Kafka 命令行界面命令的使用

![](img/19d2a61d963a0ef0b82298d85cc9d54a.png)

布莱克·康纳利在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 介绍

Apache Kafka 是促进均匀流架构的最常用技术之一。尽管经常被视为仅仅是一个消息代理人(比如 RabbitMQ)，Kafka 绝对不仅仅是这样。

Kafka 是 LinkedIn 的一个内部项目，于 2011 年开源，并迅速从一个消息代理演变为一个完整的平台，以高度可扩展、容错的分布式方式实现流媒体。

在今天的文章中，我们将浏览 Kafka 命令行界面中一些最常用的命令。请确保将本教程加入书签，因为在 Kafka 集群上执行某些管理任务时，您可能需要经常引用某些部分。

# 1.如何列出 Kafka 集群中的所有主题

如果您想要列出特定代理中包含的主题，下面的命令将会达到目的:

```
$ kafka-topics \
  --bootstrap-server localhost:9092 \
  --list
```

请注意，在旧版本中，您也可以使用 Zookeeper 端点，如下所示:

```
$ kafka-topics \
  --zookeeper localhost:2181 \
  --list
```

由于最近的 Kafka 版本将删除 Zookeeper 依赖，我建议使用第一个命令。

最后，如果您想列出整个集群的主题，那么请确保将所有代理都包含在`--bootstrap-server`中:

```
$ kafka-topics \
  --bootstrap-server localhost:9092,localhost:9093,localhost:9094 \
  --list
```

# 2.如何删除卡夫卡的一个话题

为了从集群中删除主题，您需要传递`--delete`标志以及代理和要删除的主题名称。

```
$ kafka-topics \
   --bootstrap-server localhost:9092,localhost:9093,localhost:9094 \
   --delete \
   --topic topic_for_deletion
```

# 3.使用正则表达式从 Kafka 中删除多个主题

当从 Kafka brokers 中删除主题时，您甚至可以指定类似 regex 的表达式，以便一次删除多个主题。例如，假设我们想要删除所有以前缀`test-`开头的主题。以下命令应该可以解决问题:

```
$ kafka-topics \
    --bootstrap-server localhost:9092,localhost:9093,localhost:9094 
    --delete \
    --topic 'test-.*'
```

# 4.如何创造一个卡夫卡主题

现在，如果你想创建一个新的主题，你可以简单地使用`kafka-topics` runner 的`--create`选项。

```
$ kafka-topics \
  --bootstrap-server localhost:9092 \
  --create \
  --topic topic-name
```

请注意，在创建主题时，您可以提供一些额外的配置选项，例如分区数量和/或复制因子。例如，为了创建一个包含三个分区、复制因子为 2 的主题，下面的命令就可以完成:

```
$ kafka-topics \
  --bootstrap-server localhost:9092 \
  --create \
  --topic topic-name \
  --partitions 3 \
  --replication-factor 2
```

# 5.获取 Kafka 主题的详细信息(分区计数、副本等。)

如果您想要检索特定主题的配置细节，您可以使用带有`kafka-topics` runner 的`--describe`选项。

```
$ kafka-topics \
  --bootstrap-server localhost:9092 \
  --describe \
  --topic topic-name
```

该命令的输出将为您提供诸如分区数量、复制因子、前导、副本和 ISR 以及附加配置(如保留期，如果缺省值已更改)等信息。

```
Topic:topic-name  PartitionCount:1    ReplicationFactor:1 Configs:
    Topic: topic-name Partition: 0    Leader: 0   Replicas: 0 Isr: 0
```

# 6.如何为一个卡夫卡主题产生数据

现在让我们来看看数据生成。为了生成特定主题的一些数据，您所需要的就是`kafka-console-consumer`跑步者:

```
$ kafka-console-producer \ 
  --bootstrap-server localhost:9092 \
  --topic topic-name> One event
> Oh, another event!
```

如果您还想为生成的每条消息设置键，那么您可以使用一些附加选项(即，启用键和键分隔符)，如下所示:

```
$ kafka-console-producer \ 
  --bootstrap-server localhost:9092 \
  --topic topic-name \
  --property "parse.key=true" \
  --property "key.separator=:"> key1:One event
> key2:Oh, another event!
```

# 7.如何从 Kafka 主题中消费数据

另一方面，如果您想在终端中消费来自特定 Kafka 主题的消息，那么您所需要的就是`kafka-console-consumer`:

```
$ kafka-console-consumer \
  --bootstrap-server localhost:9092 \
  --topic topic-name \
  --from-beginning*One event
Oh, another event!*
```

如果您还想打印出键，那么您可能还想指定键分隔符、启用键和值打印的标志，以及键和值反序列化程序。

举个例子，

```
$ kafka-console-consumer \
  --bootstrap-server localhost:9092 \
  --topic topic-name \
  --from-beginning \
  --property key.separator="-" \
  --property print.key=true \
  --property print.value=true \
  --property  key.deserialzer=org.apache.kafka.common.serialization.StringDeserializer \
  --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
```

# 8.如何净化卡夫卡主题

现在，为了清除 Kafka 主题并删除所有消息，您可以简单地将保留期临时更改为一秒，然后再改回来。

要将保留时间更改为一秒，您只需将`--add-config`旗与`kafka-configs`赛跑运动员配合使用:

```
$ kafka-configs \
  --bootstrap-server localhost:9092 \
  --alter \
  --entity-type topics \
  --entity-name topic-name \
  --add-config retention.ms=1000
```

要恢复它，只需删除此配置(以便使用默认保留):

```
$ kafka-configs \
  --bootstrap-server localhost:9092 \
  --alter \
  --entity-type topics \
  --entity-name topic-name \
  --delete-config retention.ms
```

# 9.如何列出所有主题的所有消费者群体

每一个卡夫卡的话题都可能有无数的消费群体。为了列出 Kafka 集群中所有主题的所有消费者群体，您可以简单地使用带有`kafka-consumer-groups` runner 的`--list`标志。

```
$ kafka-consumer-groups \
  --bootstrap-server localhost:9092 \
  --list
```

输出将包含所有使用者组的名称:

```
test-consumer-group-1
test-consumer-group-2
test-consumer-group-3
...
```

# 10.获取特定消费者群体的详细信息(补偿、滞后等)。)

现在，为了获得关于特定使用者组的更多细节，您可以使用`--describe`标志和使用者组名称(可以使用我们在上一节中演示的命令推断出来)。

```
$ kafka-consumer-groups \
  --bootstrap-server localhost:9092 \
  --describe \
  --group my-group
```

输出将包含以下信息:

```
TOPIC 
PARTITION 
CURRENT-OFFSET 
LOG-END-OFFSET 
LAG 
CONSUMER-ID 
HOST 
CLIENT-ID
```

# 11.获取使用者组中所有活动成员的列表

`--describe`选项还可以与其他选项结合使用，以获得特定消费者群体的更多详细信息。

为了列出特定使用者组中的所有活动成员，您还可以指定`--members`标志:

```
$ kafka-consumer-groups \
  --bootstrap-server localhost:9092 \
  --describe \
  --group my-group \
  --members
```

以下是上述命令的输出示例:

```
**CONSUMER-ID          HOST           CLIENT-ID       #PARTITIONS**
consumer1-3fc8d..   /127.0.0.1      consumer1       2
consumer4-117fe..   /127.0.0.1      consumer4       1
consumer2-e76ea..   /127.0.0.1      consumer2       3
consumer3-ecea4..   /127.0.0.1
```

要获得更多细节，您还可以使用`--verbose`选项，该选项还将报告分配给每个成员的分区。

```
$ kafka-consumer-groups \
  --bootstrap-server localhost:9092 \
  --describe \
  --group my-group \
  --members \
  --verbose
```

输出应该像下面这样。

```
CONSUMER-ID HOST       CLIENT-ID  #PARTITIONS ASSIGNMENT
..          /127.0.0.1 consumer1  2           topic1(0), topic2(0)
..          /127.0.0.1 consumer4  1           topic3(2)
..          /127.0.0.1 consumer2  3           topic2(1), topic3(0,1)
..          /127.0.0.1  consumer3 0           -
```

# 12.增加卡夫卡主题分区的数量

如果出于某种原因，您需要增加特定主题的分区数量，那么您可以使用`--alter`标志来指定新的——增加的——分区数量。

```
$ kafka-topics \
  --bootstrap-server localhost:9092 \
  --alter \
  --topic topic-name \
  --partitions 40
```

# 13.在 Kafka 代理中添加配置

您可能需要做的另一件常见事情是向特定(甚至所有)代理添加更多配置选项。

例如，假设我们想要指定用于日志清理的后台线程的数量。这种行为可以通过`log.cleaner.threads`配置选项进行调整。

为此，您可以运行`kafka-configs` runner 以及代理细节和`--add-config`，如下所示。

```
$ kafka-configs \
  --bootstrap-server localhost:9092 \
  --entity-type brokers \
  --entity-name 0
  --alter \
  --add-config log.cleaner.threads=2
```

如果您想为集群中的所有代理添加一个配置，那么您可以使用`--entity-default`标志(而不是通过(`--entity-name broker-id`)指定一个特定的代理)，如下所示。

```
$ kafka-configs \
  --bootstrap-server localhost:9092 \
  --entity-type brokers \
  --entity-default \
  --alter \
  --add-config log.cleaner.threads=2
```

这里需要注意的是，一些有效的配置可能需要重新启动代理。您可以在官方文档的[相关章节中查看每个单独代理配置的更新模式。](https://kafka.apache.org/documentation/#brokerconfigs)

# 14.从 Kafka 代理中删除配置

另一方面，您可能还想从特定的代理中删除一些配置。您只需要指定代理 id，(`--entity-name`)和`--delete-config`标志来指定您希望删除的选项。

```
$ kafka-configs \
  --bootstrap-server localhost:9092 \
  --entity-type brokers \
  --entity-name 0 \
  --alter \
  --delete-config log.cleaner.threads
```

同样，如果您希望删除对集群中的每个代理都有效，您可以提供`--entity-default`标志，而不是通过`--entity-name`指定代理 id。

# 15.描述 Kafka 代理的当前动态代理配置

现在，为了获得特定代理的当前配置，您可以在执行`kafka-configs` runner 时指定代理 id(即`--entity-name`)和`--describe`标志。

```
$ kafka-configs \
  --bootstrap-server localhost:9092 \
  --entity-type brokers \
  --entity-name 0 
  --describe
```

您可能也会喜欢的相关文章:

[](https://towardsdatascience.com/kafka-no-longer-requires-zookeeper-ebfbf3862104) [## 卡夫卡不再需要动物园管理员了

### 2.8.0 版本让你提前接触到没有动物园管理员的卡夫卡

towardsdatascience.com](https://towardsdatascience.com/kafka-no-longer-requires-zookeeper-ebfbf3862104) [](https://towardsdatascience.com/kafka-monitoring-tools-704de5878030) [## Kafka UI 监控工具(2021 年更新)

### 探索 Apache Kafka 集群的一些最强大的 UI 监控工具

towardsdatascience.com](https://towardsdatascience.com/kafka-monitoring-tools-704de5878030) [](/how-to-fetch-specific-messages-in-apache-kafka-4133dad0b4b8) [## 如何在阿帕奇卡夫卡中获取特定信息

### 掌握 Kafka 控制台消费者和 kafkacat

better 编程. pub](/how-to-fetch-specific-messages-in-apache-kafka-4133dad0b4b8)