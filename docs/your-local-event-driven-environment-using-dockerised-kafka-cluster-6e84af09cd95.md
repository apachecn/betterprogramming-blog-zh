# 使用 Kafka Docker 设置您的本地事件驱动环境

> 原文：<https://betterprogramming.pub/your-local-event-driven-environment-using-dockerised-kafka-cluster-6e84af09cd95>

## 在 Docker 容器中构建 Kafka 集群，学习如何创建主题、生成和使用消息

![](img/114df5d92c54fb5df9ad7a52dbc3cae8.png)

Jelleke Vanooteghem 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 介绍

事件驱动架构是当今许多应用程序中实现的现代架构之一。过去几年开发了许多工具来支持这种架构，例如，AWS SNS、SQS、RabbitMQ 和 Apache Kafka。

事件驱动架构的主要目的是通过一个或多个消息队列来分离您的服务，服务将向这些队列发布消息，并从这些队列中进行轮询或消费。这样，我们可以很容易地替换生产者或消费者，因为它们是相互分离的。

在这一部分中，我们将设置一个本地 Kafka Docker，并使用它的 CLI 工具来执行基本操作，包括创建主题、发布消息和使用它们。

我们将使用的 Docker 图像如下:

*   `confluentinc/cp-zookeeper:5.4.0`
*   `confluentinc/cp-server:5.4.0`
*   `confluentinc/cp-kafka:5.4.0`

# 先决条件

遵循本教程的唯一先决条件是 Docker。你可能想按照[官方安装说明](https://docs.docker.com/install/)进行操作。

# 编写 Docker 编写文件

我们要做的第一件事是创建一个`docker-compose.yml`文件。使用组合文件的优点是，我们可以将组成应用程序本身的服务组合在一起。然后，我们可以使用一个命令:`docker-compose up`启动该文件中定义的所有容器。

另一种方法是使用`docker run`命令，但这意味着我们需要执行三次，每次执行一个 Docker 映像，我们希望从这个映像开始旋转容器。

正如介绍中提到的，我们将有三个 Docker 容器运行。因此，我们的组合文件将由三个服务组成，名称如下:`zookeeper`、`broker`和`kafka-tools`。

docker-compose.yml

我们的本地 Kafka 集群是由`zookeeper`和`broker`容器构建的。`broker`是 Apache Kafka，它依赖于 [Apache Zookeeper](https://zookeeper.apache.org/) ，这就是为什么我们在 compose 文件中指定了依赖关系。

```
services:
  ...
  ...
  broker:
    ...
    ...
    depends_on:
      - zookeeper
```

最后，我们有`kafka-tools`容器，它包含我们可以用来与`broker`交互的命令行工具。

注意，对于`kafka-tools`容器，我们添加了`network_mode: "host"`。这使得从容器内部，`localhost`被解释为我们机器上的 Docker 主机。

这样我们可以很容易地连接到`broker`，因为它在`localhost:9092`上暴露了自己。稍后我们会看到更多相关内容。

# 打开集装箱

我们将启动我们在`docker-compose.yml`文件中定义的三个容器。转到您的终端，将目录切换到您创建`docker-compose.yml`文件的位置，并执行以下命令。

```
~/demo/kafka-local ❯ ls -l
total 8
-rw-r--r--  1 billyde  staff  1347 12 Feb 23:06 docker-compose.yml~/demo/kafka-local ❯ docker-compose up -d
Creating network "kafka-local_default" with the default driver
Creating kafka-tools ... done
Creating zookeeper   ... done
Creating broker      ... done
```

最后，要检查所有容器是否都在运行，运行以下命令。

```
~/demo/kafka-local ❯ docker ps
CONTAINER ID        IMAGE                             COMMAND                  CREATED             STATUS              PORTS                                        NAMES
748c5da81038        confluentinc/cp-server:5.4.0      "/etc/confluent/dock…"   5 seconds ago       Up 4 seconds        0.0.0.0:9092->9092/tcp                       broker
5044ae334235        confluentinc/cp-zookeeper:5.4.0   "/etc/confluent/dock…"   5 seconds ago       Up 4 seconds        2888/tcp, 0.0.0.0:2181->2181/tcp, 3888/tcp   zookeeper
ff1f9070dc42        confluentinc/cp-kafka:5.4.0       "tail -f /dev/null"      5 seconds ago       Up 4 seconds        9092/tcp                                     kafka-tools
```

如果您看到所有的容器都列在那里，并且它们的`STATUS`是`Up xx seconds`，那么我们是好的。快乐的日子！

# 创造一个卡夫卡主题

就像任何消息代理一样，Kafka 通过向主题发送消息来运行。每个主题通常表示一个特定的事件，例如，用户活动主题，其中的消息将与用户活动相关。

让我们看看如何使用 cli 工具创建主题。

首先，我们需要进入`kafka-tools`容器，因为那是我们的 Kafka cli 工具所在的地方。为此，从终端执行以下命令。

```
~/demo/kafka-local ❯ docker exec -ti kafka-tools bash
root@kafka-tools:/#
```

如果你看到`root@kafka-tools:/#`，你就中了！让我们创建一个主题，我们称之为`to-do-list`，因为它将包含我们需要做的事情的列表。

```
root@kafka-tools:/# kafka-topics --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 2 --topic to-do-list
root@kafka-tools:/#
```

该命令实际上不返回任何内容，任何内容都不表示好。要检查主题是否实际创建，我们可以运行下面的命令。

```
root@kafka-tools:/# kafka-topics --list --bootstrap-server localhost:9092
__confluent.support.metrics
_confluent-license
_confluent-metrics
to-do-list
```

很棒的东西，我们可以看到我们的主题`to-do-list`确实被创造出来了。有关该主题的更多详细信息，我们可以运行以下命令。

```
root@kafka-tools:/#  kafka-topics --describe --bootstrap-server localhost:9092 --topic to-do-list
Topic: to-do-list PartitionCount: 2 ReplicationFactor: 1 Configs:
 Topic: to-do-list Partition: 0 Leader: 1 Replicas: 1 Isr: 1
 Topic: to-do-list Partition: 1 Leader: 1 Replicas: 1 Isr: 1
```

好了，在进入下一部分之前，我们先来讨论一些事情。

*   还记得上一节的内容吗？如果我们不将`network_mode: "host"`添加到`kafka-tools`服务中，它将无法连接到`broker`。相反，这是我们将会看到的。

```
[2020-02-12 12:40:27,990] WARN [AdminClient clientId=adminclient-1] Connection to node -1 (localhost/127.0.0.1:9092) could not be established. Broker may not be available. (org.apache.kafka.clients.NetworkClient)
```

*   `--replication-factor 1`:这指定了我们想要创建多少主题分区的副本。复制是为了容错。例如，如果我们指定因子为 3，这意味着将有三台服务器包含相同的数据(主题、它的分区和它的消息)。三台服务器中的一台将成为领导者，其余的将成为追随者。
*   `--partitions 2`:这个名字是自我描述的，但是这基本上允许我们为我们的主题设置我们想要多少个分区。每当我们的 Kafka 生产者向该主题发送消息或记录时，该记录将只存在于一个可用的分区中。记录在分区中的位置基于记录的键。
*   `--topic to-do-list`:您可能已经猜到了，这个参数指定了我们想要创建的主题的名称。

# 向主题发送消息

我们的主题已经准备好了，这意味着我们可以将一些消息放入其中。为此，我们将使用`kafka-console-producer`。让我们看看如何发送键值消息。

```
root@kafka-tools:/# kafka-console-producer --broker-list localhost:9092 --topic to-do-list --property "parse.key=true" --property "key.separator=:"
>1:Wash dishes
>2:Clean bathroom
>3:Mop living room
```

选项`--property parse.key=true`告诉生产者我们想要发送一个带有密钥的消息。此外，我们还需要告诉生产者将使用什么分隔符来分隔键和值(或消息本身)，这由选项`--property "key.separator=:"`指定。

我们现在可以在发送完消息后关闭生成器。只需从键盘上按下`ctrl + c`即可终止。

# 消费来自主题的消息

我们在之前的话题中已经发表了一些消息，我们来看看是否可以消费。为此，我们将使用`kafka-console-consumer`。

```
root@kafka-tools:/# kafka-console-consumer --bootstrap-server localhost:9092 --from-beginning --topic to-do-list --property "print.key=true"
1 Wash dishes
3 Mop living room
2 Clean bathroom
```

完美！我们可以看到上一节中生成的所有三条消息。

需要注意一些事情:

*   `--from-beginning` : Kafka 消费者通过偏移跟踪其消息消费。该选项告诉消费者，如果还没有确定的偏移量(在本例中没有),就从最早的可用偏移量开始消费。
*   `--property "print.key=true"`:该选项告诉消费者将消息的密钥打印到控制台。如果没有指定，使用者将只输出消息。

与生产者相同，要终止消费者，只需按`ctrl + c`。

# 包裹

在本教程中，我们学习了如何在 Docker 容器中启动本地 Kafka 集群。此外，我们还做了一些基本的操作，包括创建主题、生成和消费消息——所有这些都是通过 Kafka 命令行工具完成的。

到现在，我们都应该对卡夫卡的作品有了基本的了解。我真的希望这能启发你去探索更多，并用 Kafka 构建事件驱动的应用程序。

对于 Kafka 应用程序来说，使用 DynamoDB 并不罕见。因此，您可能也对设置 DynamoDB 的本地实例感兴趣。你可以看看这个[教程](https://medium.com/better-programming/how-to-set-up-a-local-dynamodb-in-a-docker-container-and-perform-the-basic-putitem-getitem-38958237b968)。

此外，检查 Avro 和模式注册表。它们是非常棒的工具，允许您定义模式，告诉您的生产者和消费者如何序列化和反序列化(分别)来自和去往 POJOs 或数据类的 Kafka 消息。

# 参考

Docker 合成文件[https://github . com/confluentinc/examples/blob/5 . 4 . 0-post/CP-all-in-one/docker-compose . yml](https://github.com/confluentinc/examples/blob/5.4.0-post/cp-all-in-one/docker-compose.yml)

官方卡夫卡 doc[https://kafka.apache.org/quickstart](https://kafka.apache.org/quickstart)