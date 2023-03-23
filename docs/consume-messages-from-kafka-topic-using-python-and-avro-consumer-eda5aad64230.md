# 使用 Python 和 Avro Consumer 消费来自 Kafka 主题的消息

> 原文：<https://betterprogramming.pub/consume-messages-from-kafka-topic-using-python-and-avro-consumer-eda5aad64230>

## 用 Kafka 和 Avro 进行轮询和反序列化

![](img/5f98c9ab952a0e59bff23ec378cfbe69.png)

安内特·lūsiņa 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

# 概观

这篇教程是对我最近写的另一篇关于如何为 Kafka 主题制作 Avro 唱片的教程[的补充。](https://medium.com/@billydharmawan/avro-producer-with-python-and-confluent-kafka-library-4a1a2ed91a24)

在本教程中，我们将学习如何编写一个 Avro 消费者，它能够轮询来自 Kafka 主题的消息，并根据 Avro 模式反序列化它们。

你可以从这个 GitHub [repo](https://github.com/billydh/python-avro-producer/) 下载代码。

# Avro 消费者

由于我们已经为 Avro 生产者在上述[回购](https://github.com/billydh/python-avro-producer/)的初始提交中做了大量工作，编写消费者相当简单。我们的生产者代码已经涵盖了所有的依赖项，所以我们可以马上开始。

当我们为 Kafka 主题生成 Avro 记录时，我们的生成器需要将 Avro 模式编码到其中，并将其序列化为一个字节数组。另一方面，当我们使用 Avro 记录时，我们的使用者需要对字节数组进行反序列化，并使用 Avro 模式将其解码为人眼可以阅读的文本或对象。

# 从 Kafka 主题消费记录的函数

好吧，让我们继续写我们的 Avro 消费者。新建一个名为`consumer_record.py`的 Python 文件，其内容如下:

消费 _ 记录. py

让我们看一下上面的代码，这样我们都明白发生了什么:

*   第 7-14 行:这里，我们基本上为我们的消费者设置了配置值——即引导服务器、模式注册 URL、消费者组 ID 和自动偏移重置属性。auto-offset reset 属性实际上是告诉我们的使用者应该从何时开始轮询记录。我们将它设置为`earliest`，这样它将从卡夫卡主题的开头开始消耗。如果我们将它设置为`latest`，并且在这个消费者初始化并订阅主题之前，主题中已经有一些消息，那么这些消息将被跳过。
*   第 16 行:这是不言自明的。它告诉我们的消费者开始订阅给定的主题，以便稍后可以轮询消息。
*   第 18-31 行:这是我们告诉消费者从订阅的主题中轮询消息的地方。我们还设置了 5 秒的轮询超时(第 19 行)，这意味着如果 5 秒后没有消息，它将停止轮询。我们检查是否有消息，如果有，我们打印消息的键和值以及我们轮询它的分区号和偏移量。最后，我们提交偏移量，以便下一次运行同一个消费者时，它不会从偏移量 0 开始，而是从最后提交的偏移量开始。
*   最后，如果没有消息，我们打印该信息并关闭消费者，因为不再需要它了。

注意:我们可以用不同的方式编写这部分代码，比如使用一个`while`循环，这样消费者就可以一直轮询和打印消息，直到没有消息为止。现在的编写方式意味着我们需要执行这个脚本的次数与主题中的消息总数一样多。同样，这只是为了演示如何编写 Avro 消费者，而不是编写生产级代码。

# 修改函数以解析命令行参数

为了能够使用相同的`util`函数来解析命令行参数，我们需要对它进行一些调整。这是因为当最初编写它时，它需要传递以下参数:`--schema-file`和`--record-value`。我们的消费者代码不需要这两者。

对于我们的消费者，我们不需要`--schema-file`参数的原因是因为 Avro 模式是在模式注册表中注册的。这是我们的消费者获取模式的地方，因此它能够解码和反序列化来自主题的 Avro 记录。

对于`--record-value`，很明显，我们的消费者代码不需要它，因为我们没有为这个主题生产任何东西。

所以，这是我们最后的`parse_command_line_args.py`:

parse_command_line_args.py —已调整

# 调整 Avro 生产商代码

由于`parse_command_line_args`函数的变化，我们需要对现有的生产者代码做一点调整。

本质上，我们只需要在开始时添加检查，以确保提供了`--schema-file`和`--record-value`的值:

send_record.py —已调整

好了，这就是我们需要做的。在下一节中，我们将测试我们的 Avro 消费者。

# 来自卡夫卡主题的消费记录

为了测试我们的消费者，我们需要做的第一件事是启动 Docker 容器，它将托管我们的 Kafka 引导服务器和模式注册表。你可以按照这个[教程](https://medium.com/better-programming/your-local-event-driven-environment-using-dockerised-kafka-cluster-6e84af09cd95)和[这个](https://medium.com/better-programming/adding-schema-registry-to-kafka-in-your-local-docker-environment-49ada28c8a9b)来设置 Docker 容器。

一旦容器开始运行，我们就可以继续使用我们的生产者代码向 Kafka 主题发送一些记录。跟随这个[教程](https://medium.com/@billydharmawan/avro-producer-with-python-and-confluent-kafka-library-4a1a2ed91a24')了解如何做的细节。

让我们执行以下命令，将两条 *x* 记录发送到`create-user-request`主题:

```
~/python-avro-producer ❯ python send_record.py --topic create-user-request --schema-file create-user-request.avsc --record-value '{"email": "[email@email.com](mailto:email@email.com)", "firstName": "Bob", "lastName": "Jones"}'
Successfully producing record value - {'email': '[email@email.com](mailto:email@email.com)', 'firstName': 'Bob', 'lastName': 'Jones'} to topic - create-user-request~/python-avro-producer ❯ python send_record.py --topic create-user-request --schema-file create-user-request.avsc --record-value '{"email": "[email2@email.com](mailto:email2@email.com)", "firstName": "Jane", "lastName": "Smith"}'
Successfully producing record value - {'email': '[email2@email.com](mailto:email2@email.com)', 'firstName': 'Jane', 'lastName': 'Smith'} to topic - create-user-request
```

酷毙了。现在，让我们执行我们的消费者代码，看看我们能否从 Kafka 主题中检索到这两条 *x* 记录:

```
~/python-avro-producer ❯ python consume_record.py --topic create-user-request --schema-file create-user-request.avsc

Successfully poll a record from Kafka topic: create-user-request, partition: 0, offset: 0
message key: e76a0f7e-c6c1-4809-a562-da50b9126ef1 || message value: {'email': '[email@email.com](mailto:email@email.com)', 'firstName': 'Bob', 'lastName': 'Jones'}~/python-avro-producer ❯ python consume_record.py --topic create-user-request --schema-file create-user-request.avscSuccessfully poll a record from Kafka topic: create-user-request, partition: 0, offset: 1
message key: 11142acb-d986-4570-9b16-b28b50accc46 || message value: {'email': '[email2@email.com](mailto:email2@email.com)', 'firstName': 'Jane', 'lastName': 'Smith'}
```

非常好。我们的 Avro 消费者工作正常。

现在，如果我们再次尝试运行它，我们应该看不到任何消息，因为主题中只有两条消息:

```
~/python-avro-producer ❯ python consume_record.py --topic create-user-request --schema-file create-user-request.avscNo messages at this point. Try again later.
```

干得好，各位。

# 总结

通过阅读本教程和[上一篇](https://medium.com/@billydharmawan/avro-producer-with-python-and-confluent-kafka-library-4a1a2ed91a24)，我们将了解卡夫卡生产者和消费者是如何工作的。我希望这能鼓励你去探索更多关于卡夫卡和事件驱动架构的知识。