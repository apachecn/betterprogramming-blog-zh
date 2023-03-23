# 如何测试 Kafka 流应用程序

> 原文：<https://betterprogramming.pub/testing-kafka-streams-applications-1c5cb14c5376>

## 通过测试验证拓扑

![](img/04a3e550b0733a1c8798c1c1b67c5766.png)

[JR Korpa](https://unsplash.com/@korpa?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/collections/1136512/%E2%98%85-%E2%80%94-textures-%2F-colors?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

“Kafka Streams”系列的前几篇博文介绍了 DSL API 中的`[Stateless](https://medium.com/better-programming/learn-stream-processing-with-kafka-streams-stateless-operations-2111080e6c53)`和`[Stateful](https://medium.com/@abhishek1987/how-to-use-stateful-operations-in-kafka-streams-1cff4da41329)`操作。在这篇博客中，我们将探索几个例子来演示如何使用测试工具来验证基于 Kafka Streams DSL API 的拓扑。

Kafka Streams 提供测试实用程序来为您的流处理管道执行单元测试，而无需依赖外部或嵌入式 Kafka 集群。除了测试之外，这些实用程序也是研究各种 API 特性的很好的学习工具。

让我们从测试相关 API 的高级概述开始

*代码是* [*可在 GitHub*](https://github.com/abhirockzz/kafka-streams-apis/tree/master/testing) *上获得，测试可以通过克隆回购后跟* `*mvn test*` *来执行。*

# 关键概念

最初，`[org.apache.kafka.streams.test](https://kafka.apache.org/24/javadoc/org/apache/kafka/streams/test/package-frame.html)`包中有几个类。它们现在已被弃用，取而代之的是以下类:

## `TestInputTopic`

`TestInputTopic`的一个实例表示一个输入主题，您可以使用`pipeInput`方法(及其重载版本)向它发送记录。使用`TopologyTestDriver`创建`TestInputTopic`实例(如下所述),并在需要时使用定制的序列化器。然后，您可以发送键值对，一次只发送一个值，或者批量发送(使用`List`)。

## `TestOutputTopic`

`TestOutputTopic`是发送-接收等式的另一半，与 a `TestInputTopic`互补。您可以使用它从拓扑操作写入的输出主题中读取记录。它的方法包括读取记录(键-值对)、只读取值、查询大小(尚未使用的当前记录的数量)等。

## `TopologyTestDriver`

`TopologyTestDriver`包含对`Topology`的引用以及与您的 Kafka Streams 应用程序相关的配置。如前所述，它用于创建`TestInputTopic`、`TestOutputTopic`的实例，提供对状态存储的访问等。

# 高层流动

如果您使用的是`Maven`，您可以将测试工具作为一个依赖项包含进来。

```
<dependency>
  <groupId>org.apache.kafka</groupId>
  <artifactId>kafka-streams-test-utils</artifactId>
  <version>2.4.0</version>
  <scope>test</scope>
</dependency>
```

您将(很可能)使用`JUnit`和`hamcrest`来编写匹配规则。

```
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.12</version>
  <scope>test</scope>
 </dependency>
 <dependency>
  <groupId>org.hamcrest</groupId>
  <artifactId>hamcrest-core</artifactId>
  <version>1.3</version>
  <scope>test</scope>
</dependency>
```

下面是一个测试用例可能的样子(类似于你如何用`JUnit`等对任何 Java 代码进行单元测试)。):

*   使用`@BeforeClass`注释方法设置全局状态(如果有)。
*   使用`@Before`带注释的方法为每个测试运行设置状态——这是您通常创建`TopologyTestDriver`等的地方。
*   `@Test`验证`Topology`的方法。
*   `@After`(和/或`@AfterClass`)拆除任何状态(无论是全局状态还是其他状态)的方法。

请确保调用`TopologyTestDriver.close()`来清理拓扑中的处理器及其相关状态。否则，由于状态不一致，可能会导致测试失败

现在您已经了解了概念和基本设置，让我们看几个具体的例子。我们将从无状态操作开始。

# 测试无状态操作

## `Filter`

这里是`Topology`,它使用过滤方法只允许长度大于 5 的值。

```
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> stream = builder.stream(INPUT_TOPIC);

stream.filter((k, v) -> v.length() > 5).to(OUTPUT_TOPIC);
```

下面是相应的测试:

```
 @Test
    public void shouldIncludeValueWithLengthGreaterThanFive() { topology = App.retainWordsLongerThan5Letters();
        td = new TopologyTestDriver(topology, config); inputTopic = td.createInputTopic(App.INPUT_TOPIC, Serdes.String().serializer(), Serdes.String().serializer());
        outputTopic = td.createOutputTopic(App.OUTPUT_TOPIC, Serdes.String().deserializer(), Serdes.String().deserializer()); assertThat(outputTopic.isEmpty(), is(true)); inputTopic.pipeInput("key1", "barrrrr");
        assertThat(outputTopic.readValue(), equalTo("barrrrr"));
        assertThat(outputTopic.isEmpty(), is(true)); inputTopic.pipeInput("key2", "bar");
        assertThat(outputTopic.isEmpty(), is(true));
    }
```

我们首先选择我们想要测试的`Topology`，创建`TopologyTestDriver`实例以及`TestInputTopic`和`TestOutputTopic`对象。

接下来，我们在发送任何数据之前确认输出主题是否为空— `assertThat(outputTopic.isEmpty(), is(true));`

现在可以使用`inputTopic.pipeInput("key1", "barrrrr");`将数据/记录发送到输入主题。这是一个同步过程，触发`Topology`，在这种情况下，T1 执行`filter`操作，并将其推送到输出主题，因为值长度超过 5。我们使用`assertThat(outputTopic.readValue(), equalTo("barrrrr"));`进行确认，并再次检查输出主题是否为空。

最后，我们发送值`bar`，并确认它没有被发送到输出主题，因为它的长度小于 5。

## 平面地图

正如本系列的第一部分(无状态操作)所解释的，这里有一个`flatMap`操作:

```
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> stream = builder.stream(INPUT_TOPIC);stream.flatMap(new KeyValueMapper<String, String, Iterable<? extends KeyValue<? extends String, ? extends String>>>() {
            @Override
            public Iterable<? extends KeyValue<? extends String, ? extends String>> apply(String k, String csv) {
                String[] values = csv.split(",");
                return Arrays.asList(values)
                        .stream()
                        .map(value -> new KeyValue<>(k, value))
                        .collect(Collectors.toList());
            }
        }).to(OUTPUT_TOPIC);
```

在上面的例子中，流中的每个记录都得到`flatMap` ped，这样每个 CSV(逗号分隔)值首先被拆分成它的组成部分，并且为 CSV 字符串的每个部分创建一个`KeyValue`对。

要测试这一点:

```
topology = App.flatMap();
        td = new TopologyTestDriver(topology, config); inputTopic = td.createInputTopic(App.INPUT_TOPIC, Serdes.String().serializer(), Serdes.String().serializer());
        outputTopic = td.createOutputTopic(App.OUTPUT_TOPIC, Serdes.String().deserializer(), Serdes.String().deserializer()); inputTopic.pipeInput("random", "foo,bar,baz");
        inputTopic.pipeInput("hello", "world,universe");
        inputTopic.pipeInput("hi", "there"); assertThat(outputTopic.getQueueSize(), equalTo(6L)); assertThat(outputTopic.readKeyValue(), equalTo(new KeyValue<>("random", "foo")));
        assertThat(outputTopic.readKeyValue(), equalTo(new KeyValue<>("random", "bar")));
        assertThat(outputTopic.readKeyValue(), equalTo(new KeyValue<>("random", "baz"))); assertThat(outputTopic.readKeyValue(), equalTo(new KeyValue<>("hello", "world")));
        assertThat(outputTopic.readKeyValue(), equalTo(new KeyValue<>("hello", "universe"))); assertThat(outputTopic.readKeyValue(), equalTo(new KeyValue<>("hi", "there")));assertThat(outputTopic.isEmpty(), is(true));
```

像往常一样，我们设置所需的测试工具类，并将输入记录推送到输入主题。例如，键`random`及其逗号分隔值`foo,bar,baz`将被拆分成单独的键值对，即它们将导致三个记录被推送到输出表。这同样适用于其他输入记录。

我们确认输出主题`assertThat(outputTopic.getQueueSize(), equalTo(6L));`中的记录数量，并验证每个键值对以确认`flatMap`行为。

# 有状态操作呢？

## 没有状态存储的有状态操作

下面是一个拓扑示例，它使用`groupByKey`后跟`count`，并将结果存储在输出主题中:

```
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> stream = builder.stream(INPUT_TOPIC);stream.groupByKey()
      .count()
      .toStream()
      .to(OUTPUT_TOPIC);
```

测试有状态操作和无状态操作没有太大的不同。

```
topology = App.count();
        td = new TopologyTestDriver(topology, config); inputTopic = td.createInputTopic(App.INPUT_TOPIC, Serdes.String().serializer(), Serdes.String().serializer());
        TestOutputTopic<String, Long> ot = td.createOutputTopic(App.OUTPUT_TOPIC, Serdes.String().deserializer(), Serdes.Long().deserializer()); inputTopic.pipeInput("key1", "value1");
        inputTopic.pipeInput("key1", "value2");
        inputTopic.pipeInput("key2", "value3");
        inputTopic.pipeInput("key3", "value4");
        inputTopic.pipeInput("key2", "value5"); assertThat(ot.readKeyValue(), equalTo(new KeyValue<String, Long>("key1", 1L)));
        assertThat(ot.readKeyValue(), equalTo(new KeyValue<String, Long>("key1", 2L)));
        assertThat(ot.readKeyValue(), equalTo(new KeyValue<String, Long>("key2", 1L)));
        assertThat(ot.readKeyValue(), equalTo(new KeyValue<String, Long>("key3", 1L)));
        assertThat(ot.readKeyValue(), equalTo(new KeyValue<String, Long>("key2", 2L)));
```

单个记录被发送到输入主题和输出主题，然后计数被验证。不出所料，按键`key1`、`key2`和`key3`的计数分别为 2、2、1。

## 具有状态存储的有状态操作

当拓扑由状态存储组成时，事情变得有趣起来。在本例中，不是将计数发送到输出主题，而是使用中间状态存储(这可以通过交互式查询来查询):

```
StreamsBuilder builder = new StreamsBuilder();
        KStream<String, String> stream = builder.stream(INPUT_TOPIC);stream.groupByKey().count(Materialized.as("count-store"));
```

TopologyTestDriver 通过`getKeyValueStore`提供对状态存储(`KeyValueStore`)的访问。在每个记录发送到输入主题后，验证状态存储计数，例如`assertThat(countStore.get("key1"), equalTo(1L));`:

```
topology = App.countWithStateStore();
td = new TopologyTestDriver(topology, config);inputTopic = td.createInputTopic(App.INPUT_TOPIC, Serdes.String().serializer(), Serdes.String().serializer());

KeyValueStore<String, Long> countStore = td.getKeyValueStore("count-store");inputTopic.pipeInput("key1", "value1");        assertThat(countStore.get("key1"), equalTo(1L));inputTopic.pipeInput("key1", "value2");
assertThat(countStore.get("key1"), equalTo(2L));inputTopic.pipeInput("key2", "value3");
assertThat(countStore.get("key2"), equalTo(1L));inputTopic.pipeInput("key3", "value4");
assertThat(countStore.get("key3"), equalTo(1L));inputTopic.pipeInput("key2", "value5");
assertThat(countStore.get("key2"), equalTo(2L));
```

注意，在我们的测试中，我们已经在每个测试方法中创建了`Topology`、`TopologyTestDriver`、`TestInputTopic`和`TestOutputTopic`。这只是因为我们在测试不同的拓扑结构。如果您使用一堆测试用例作为单个 JUnit 类的一部分来测试单个的`Topology`，那么您可以非常容易地将它转移到用`@Before`注释的 setup 方法，这样它就可以在每个测试用例开始之前自动运行。

暂时就这样吧！这是一个简短但有希望对测试基于 Kafka Streams 的处理管道有用的介绍。

感谢阅读！