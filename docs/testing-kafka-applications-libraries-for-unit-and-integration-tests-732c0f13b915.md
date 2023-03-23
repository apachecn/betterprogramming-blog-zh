# 测试 Kafka 应用程序——用于单元和集成测试的库

> 原文：<https://betterprogramming.pub/testing-kafka-applications-libraries-for-unit-and-integration-tests-732c0f13b915>

## 为了卡夫卡消费者，卡夫卡生产者，溪流和春天——卡夫卡

![](img/7abb4c974e0bf47fa3ba9001c7d18908.png)

照片由 [Siora 摄影](https://unsplash.com/@siora18?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

你被赋予了一项涉及使用 Kafka 的任务，或者你正在设计一个全新的应用程序？即使不尝试使用 TDD(测试驱动开发),牢记测试也是一个很好的实践。我在这里的目的不是给你测试的例子，网上有很多，大多数只是测试的框架，我发现通常非常有用的是在一个地方总结的信息，列表和要点(这是我的难题)。更重要的是，如果你最近才开始研究卡夫卡，你可能甚至不知道那里有什么。

因此，这将是一个类和库的列表，您可以根据您已经决定在您的主实现中使用的库和类来用于您的单元和集成测试。

# 卡夫卡消费者

为了对您的简单消费者进行单元测试，您可以使用由`org.apache.kafka:kafka-clients:X.X.X`库提供的`[MockConsumer](https://javadoc.io/doc/org.apache.kafka/kafka-clients/latest/org/apache/kafka/clients/consumer/MockConsumer.html)`。设置所有的测试非常简单和直观。在设计应用程序(或稍后进行重构)时，您需要记住的唯一一件事是使使用或包装 KafkaConsumer 实例的类是可测试的。这意味着让他们期待一个`Consumer<K, V>`被传入，无论是通过调用工厂方法，直接在构造函数中，还是作为 bean 注入。

要创建 MockConsumer 的实例，您只需匹配记录的键和值的类型，并“告诉”它从哪里开始读取:

```
val mockConsumer = MockConsumer<String, String>(OffsetResetStrategy.LATEST)
```

`KafkaConsumer`和`MockConsumer`都实现了`Consumer<K,V>`接口，所以一旦你把它传递给你正在测试的类，它就会像一个真正的消费者一样与之交互。这里的不同之处在于，您可以使用其他方法来为测试准备条件:

*   本质上是在开始测试之前准备要读取的记录
*   `schedulePollTask()`在这里您可以传递任何 Runnable 以在随后的轮询中执行
*   `setPollException()`检查您的消费者在异常情况下的行为

因为这仅仅意味着单元测试，所以您在这里测试的基本上是您的真正消费者是否:

*   可以反序列化它要读取的记录
*   按预期处理记录——理想情况下是有责任划分的，所以这里的“处理”可能只是指将预期值传递给另一个处理业务逻辑的类，该类是单独进行单元测试的
*   过滤任何记录(如果需要的话)
*   处理反序列化或处理中的错误
*   处理与 Kafka 的连接相关的错误——关于订阅、轮询或提交
*   正确处理偏移量—是否提交、何时提交以及提交的频率

注意:如果您的用户以非标准的方式操作偏移量，那么您可以在所有的单元测试之间继续使用相同的实例，除了`updateBeginningOffsets()`和`updateEndOffsets().`

# 卡夫卡作品

`kafka-clients`库还包括一个`[**MockProducer**](https://javadoc.io/doc/org.apache.kafka/kafka-clients/latest/index.html)` 类，它实现了与`KafkaProducer`相同的接口`Producer<K, V>`。因此，与上面的消费者测试类似，您的生产类应该以允许您传入模拟的方式来设计。

要创建一个`MockProducer`的实例，您需要匹配键的类型和记录的值，并“告诉”它是自动成功完成`send`请求(autocomplete=true ),还是希望通过调用`completeNext()`或`errorNext()`来显式完成它们。

```
val mockProduer = MockProducer(true, StringSerializer(), StringSerializer())
```

为了测试您的生产者的正常工作流程，您将希望为您的生产者安排一些工作，使用 autocomplete，然后使用 MockProducer 上的`history()`方法检查已经发送了什么。这将返回自上次您在模拟上调用`clear()`以来发送的所有 ProducerRecord-s 的列表。

为了测试您如何处理异常，您可以将 autocomplete 设置为 false，并将`errorNext()`设置为，这将在下一个未完成的`send`调用中抛出您传递的任何 RuntimeException。

这里，测试能力也仅限于单元测试，所以你要验证的是你的生产者是否:

*   可以序列化它需要的记录
*   处理序列化错误
*   处理与 Kafka 连接相关的错误-即在`send()`上
*   正确应用任何过滤，即实际发送的记录数量与您预期的数量相匹配
*   以预期的格式发送记录—任何丰富或重新格式化都被正确应用

# 卡夫卡流

对于流来说，测试类和生产类被划分到不同的库中，所以您需要添加`org.apache.kafka:kafka-streams:X.X.X`依赖项来使用流，然后添加`org.apache.kafka:kafka-streams-test-utils:X.X.X`依赖项来使用方便的测试类。

这里，事情反过来进行——不是创建一个模拟并将其传递给测试的类，而是通过传入拓扑和属性来创建一个`[TopologyTestDriver](https://kafka.apache.org/30/javadoc/org/apache/kafka/streams/TopologyTestDriver.html)`的实例。因此，在这里使您的应用程序单元可测试意味着有一种方法来创建您的拓扑并将其传递给测试驱动程序。

```
val driver = TopologyTestDriver(myTopology, myProperties)
```

一旦有了驱动程序实例，就需要为拓扑显式地创建所有主题:

```
val myInputTopic = driver.**createInputTopic**(
       inputTopicName, 
       Serdes.String().serializer(), // key type
       Serdes.String().serializer()  // value type
)val myOutputTopic = driver.**createOutputTopic**(
       outputTopicName, 
       Serdes.String().deserializer(), //  key type
       Serdes.String().deserializer()  //  value type
).... // create as many output topics as your topology hasval myDlqTopic = driver.**createOutputTopic**(
       dlqTopicName, 
       Serdes.String().deserializer(), //  key type
       Serdes.String().deserializer()  //  value type
)
```

将所有的[***TestInputTopic***](https://kafka.apache.org/30/javadoc/org/apache/kafka/streams/TestInputTopic.html)和[***TestOutputTopic***](https://kafka.apache.org/30/javadoc/org/apache/kafka/streams/TestOutputTopic.html)设置好，就可以开始测试了！激动人心！

```
myInputTopic.**pipeInput**(key, validValue)
...
assertTrue(myDlqTopic.isEmpty)
assertFalse(myOutputTopic.isEmpty)val actualRecord = myOutputTopic.**readRecord**()
assertEquals(expectedRecord, actualRecord, "Oh no, records don't match)
```

您也可以同时处理多个输入值:

*   `pipeValueList(List<V>)` —如果您的测试类只关心值
*   `pipeKeyValueList(List<KeyValue<K,V>>` —如果你的测试类只关心键和值
*   `pipeRecordList(List<TestRecord<K,V>>`——如果您的测试类只使用任何标题或时间戳

同样，对于输出:

*   `readValuesToList()` —如果您只需要验证输出值
*   `readKeyValuesToList()`或`readKeyValuesToMap()` —如果您只需要验证输出的键和值
*   `readRecordsToList()` —如果您需要检查输出的标题和时间戳

因为我们仍然在这里讨论单元测试，所以检查是非常基本的——您验证您的流:

*   可以序列化和反序列化它需要的记录
*   以期望的方式处理任何异常
*   按预期处理记录—输出格式与预期格式匹配，将流视为黑盒
*   是否按预期进行过滤—无论输入了多少条记录，输出记录的数量都与预期的数量相匹配

# 流处理器

如果你正在使用一个处理器来处理你可以使用一个[***MockProcessorContext***](https://kafka.apache.org/28/javadoc/org/apache/kafka/streams/processor/MockProcessorContext)来初始化你的那个接口的实现。使用它，您可以检查记录是否被转发到正确的主题，以及偏移量是否被提交。

```
val mockContext = MockProcessorContext<String, String>()
val processor = MyProcessor() // implementing Processor
processor.init(mockContext)...processor.process(record)
val forwardedRecords = mockContext.forwareded()
assertEquals(1, forwardedRecords.size)
assertEquals(expectedRecord, forwardedRecords.map{it.record()}.first())
assertEqual(expectedTopic, forwardedRecords[0].childName().get())// if you have scheduled commit (or other action) manipulate time bymockContext.scheduledPunctuators()[0].punctuator.punctuate(time)// check if scheduled task is doneassertTrue(mockContext.committed())
```

# 集成测试

有多种方法可以做到这一点，因此我将列出一些方法，您可以探索并测试哪些方法适合您:

## KafkaStreams 的嵌入式 KafkaCluster

在`org.apache.kafka:kafka-streams-test-utils`库中，有几个更有帮助的类，其中一个是[***embeddedcafkacluster***](https://github.com/a0x8o/kafka/blob/master/streams/src/test/java/org/apache/kafka/streams/integration/utils/EmbeddedKafkaCluster.java)***。*** 这将启动一个带有 1 个 zookeeper 实例和可配置数量的代理的内存 Kafka 集群。

```
class MyKafkaStreamIntegrationTest{
    @ClassRule
    val cluster = EmbeddedSingleNodeKafkaCluster()

    @BeforeAll
    fun setup() {
        cluster.createTopic(myTopic)
        // ... other setup
    }

    // ... startup app and test
```

## 春天的嵌入式卡夫卡

使用`org.springframework.kafka:spring-kafka-test`库，你可以访问运行在`localhost:9092.`的 kafka 的“嵌入式”内存实例。要使用它，你只需要用[***embedded Kafka***](https://docs.spring.io/spring-kafka/reference/html/#embedded-kafka-annotation)来注释你的测试类

```
@SpringBootTest
@DirtiesContext // if have more than one test class using kafka
@EmbeddedKafka(partitions=1, brokerProperties = {..}
class MySpringIntegrationTest{
    // startup your app and assert 
}
```

## 测试容器

内存中的实例很可能与 Kafka 和 Zookeeper 的实际实现不同，因此也可以选择为您的`[KafkaContainer](https://www.testcontainers.org/modules/kafka/)` ***使用 docker 容器。***

```
KafkaContainer kafka = KafkaContainer(DockerImageName.parse(“confluentinc/cp-kafka:6.2.1”))
// configure
kafka.start()
```

## 查理特的卡夫卡-朱尼特

这是一个不太标准的[库](https://github.com/charithe/kafka-junit)，但是它完成了工作并提供了一些方便的方法，你可能会发现这些方法很有用，因此值得在这里提及。它有一个 junit4 和 5 实现:

```
[@ClassRule](http://twitter.com/ClassRule)
public static KafkaJunitRule kafkaRule = new KafkaJunitRule(EphemeralKafkaBroker.create());@ExtendWith(KafkaJunitExtension.class)
@KafkaJunitExtensionConfig(startupMode = StartupMode.WAIT_FOR_STARTUP)
```

希望你能在这里找到一些有用的信息。

感谢阅读！