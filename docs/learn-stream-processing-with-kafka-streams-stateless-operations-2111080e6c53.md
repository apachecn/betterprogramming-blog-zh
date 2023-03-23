# 使用 Kafka 流学习流处理

> 原文：<https://betterprogramming.pub/learn-stream-processing-with-kafka-streams-stateless-operations-2111080e6c53>

## 无状态操作

![](img/21477c881b0ce47506d56c9e1111eed8.png)

Joao Branco 在 [Unsplash](https://unsplash.com/s/photos/stream?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

[Kafka Streams](https://kafka.apache.org/documentation/streams/) 是一个 Java 库，用于在 Apache Kafka 之上开发流处理应用程序。这是 Kafka Streams 及其 API 系列文章的第一篇。

这不是一个关于卡夫卡流的理论指南(尽管我在过去已经涉及了其中的一些方面)。

在这一部分中，我们将介绍 Kafka Streams DSL API 中的无状态操作——特别是在`KStream`中可用的函数，如`filter`、`map`、`groupBy`等。Kafka Streams 中的 DSL API 提供了一个强大的函数式编程模型来定义流处理拓扑。

原料药(`KStream`等)。)在这篇文章中引用的可以在 [Kafka Streams Javadocs](https://kafka.apache.org/24/javadoc/index.html?org/apache/kafka/streams/) 中找到

# 设置

首先，您需要创建一个`KafkaStreams`实例。它需要一个`topology`和相关的配置(以`java.util.Properties`的形式)。

为 Kafka streams 应用程序设置所需的配置:

```
Properties config = new Properties();config.setProperty(StreamsConfig.APPLICATION_ID_CONFIG, App.APP_ID);
config.setProperty(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
config.setProperty(StreamsConfig.DEFAULT_KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
config.setProperty(StreamsConfig.DEFAULT_VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
```

然后我们可以构建一个定义处理管道的`topology`(本文的其余部分将关注拓扑的无状态部分)。

您可以创建`KafkaStreams`实例并开始处理:

```
KafkaStreams app = new KafkaStreams(topology, config);
app.start();
new CountdownLatch(1).await(); // wait forever
```

# 使用 KStream 的无状态操作

我通常喜欢把东西分成不同的类别——这有助于我分而治之。在这种情况下，我尝试了同样的方法，将各种`KStream`操作分成`filter`、`map`等。

让我们开始吧！

## 过滤器

您可以使用`filter`根据您的标准省略或包含记录。

例如，如果发送到主题的值包含一个单词，并且您希望包含大于指定长度的单词，那么您可以使用一个`Predicate`来定义这个标准，并将其传递给`filter`方法——这将创建一个新的带有过滤记录的`KStream`实例。

```
KStream<String, String> stream = builder.stream("words");
stream.filter(new Predicate<String, String>() {
    @Override
    public boolean test(String k, String v) {
            return v.length() > 5;
        }
    })
```

如果您想根据某些标准来`exclude`记录，也可以使用`filterNot`。下面是一个 lambda 风格的例子:

```
KStream<String, String> stream = builder.stream("words");
stream.filterNot((key,value) -> value.startsWith("foo"));
```

## `map`

一个常用的无状态操作是`map`。在 Kafka 流的情况下，可以通过应用映射器函数来转换输入`KStream`中的每个记录。

这个有多种口味，比如`map`、`mapValues`、`flatMap`、`flatMapValues`。

如果您想改变键和值，只需使用`map`方法。例如，将键和值转换为大写。

```
stream.map(new KeyValueMapper<String, String, KeyValue<String, String>>() {
    @Override
    public KeyValue<String, String> apply(String k, String v) {
            return new KeyValue<>(k.toUpperCase(), v.toUpperCase());
        }
    });
```

如果您只想改变数值，请使用`mapValues`:

```
stream.mapValues(value -> value.toUpperCase());
```

`flatMap`与`map`类似，但允许返回多条记录(`KeyValue` s):

```
stream.flatMap(new KeyValueMapper<String, String, Iterable<? extends KeyValue<? extends String, ? extends String>>>() {
    @Override
    public Iterable<? extends KeyValue<? extends String, ? extends String>> apply(String k, String csv) {
        String[] values = csv.split(",");
        return Arrays.asList(values)
                    .stream()
                    .map(value -> new KeyValue<>(k, value))
                    .collect(Collectors.toList());
            }
    })
```

在上面的例子中，流中的每个记录都得到`flatMap` ped，这样每个逗号分隔的值(CSV)首先被分成它的组成部分，并且为 CSV 字符串的每个部分创建一个`KeyValue`对。例如，如果您有记录`(foo <-> a,b,c)`和`(bar <-> d,e)`(其中`foo`和`bar`是键)，那么结果流将有五个条目:`(foo,a)`、`(foo,b)`、`(foo,c)`、`(bar,d)`和`(bar,e)`。

如果您只想从流中接受一个值并返回一个值集合，请使用`flatMapValues`

## `group`

如果您想对一个`KStream`的内容执行有状态聚合，那么您首先需要按照键对它的记录进行分组，以创建一个`KGroupedStream`。

我们将在本系列的后续文章中讨论`KGroupedStream`上的有状态操作。

这里有一个使用`groupByKey`的例子:

```
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> stream = builder.stream(INPUT_TOPIC); 

KGroupedStream<String,String> kgs = stream.groupByKey();
```

`groupByKey`的一个通用版本是`groupBy`，它让您能够使用`KeyValueMapper`基于不同的键进行分组。

```
stream.groupBy(new KeyValueMapper<String, String, String>() {
    @Override
    public String apply(String k, String v) {
        return k.toUpperCase();
    }
});
```

在这两种情况下(`groupByKey`和`groupBy`，如果您需要使用不同的`Serde` ( `Serializer`和`Deserializer`)而不是默认的，请使用重载版本(它接受一个`Grouped`对象)。

```
stream.groupByKey(Grouped.with(Serdes.Bytes(), Serdes.Long()));
```

# 终端操作

Kafka Streams 中的终端操作是返回`void`而不是中间物的方法，比如另一个`KStream`或`KTable`。

您可以使用`to`方法将`KStream`的记录存储到 Kafka 中的一个主题。

```
KStream<String, String> stream = builder.stream("words");stream.mapValues(value -> value.toUpperCase())
      .to("uppercase-words");
```

一个重载版本的`to`允许你指定一个`Produced`对象来定制`Serdes`和分割器。

```
stream.mapValues(value -> value.toUpperCase())
      .to("output-topic",Produced.with(Serdes.Bytes(), Serdes.Long()));
```

除了指定一个静态的主题名称，您还可以利用一个`TopicNameExtractor`并包含任何定制的逻辑来动态地选择一个特定的主题

```
stream.mapValues(value -> value.toUpperCase())
    .to(new TopicNameExtractor<String, String>() {
        @Override
        public String extract(String k, String v, RecordContext rc) {
            return rc.topic()+"_uppercase";
        }
    });
```

在这个例子中，我们利用包含记录元数据的`RecordContext`来获取主题，然后将`_uppercase`追加到主题中。

在上述所有情况下，水槽主题应该在卡夫卡中预先存在。

如果您想要记录`KStream`记录(用于调试目的)，请使用`print`方法。它接受一个`Printed`实例来配置行为。

```
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> stream = builder.stream(INPUT_TOPIC);
stream.mapValues(v -> v.toUpperCase()).print(Printed.toSysOut());
```

这将打印出记录——例如，如果您将`(foo, BAR)`和`(john, DOE)`传递给输入主题，它们将被转换为大写并被记录如下:

```
[KSTREAM-MAPVALUES-0000000001]: foo, BAR
[KSTREAM-MAPVALUES-0000000001]: john, DOE
```

您还可以使用`Printed.toFile`(而不是`toSysOut`)来定位特定的文件。

`foreach`方法与`print`和`peek`相似:

*   也是终端操作(像`print`)
*   它接受一个`ForeachAction`(就像`peek`)

# 多方面的

由于`print`方法是一个终端操作，您可以选择使用`peek`，它返回同一个`KStream` 实例。它接受一个`ForeachAction`，可以用来指定您想要对每条记录做什么——例如，记录键和值。

```
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> stream = builder.stream(INPUT_TOPIC);stream.mapValues(v -> v.toUpperCase())
      .peek((k,v) -> System.out.println("key="+k+", value="+v))
      .to(OUTPUT_TOPIC);
```

在上面的例子中，您将能够看到被记录的键和值，它们也将被具体化为输出主题(不像`print`操作)。

`branch`是一个我没用过的方法(说实话)，但是看起来挺有意思的。它使您能够根据多个标准(由一个`Predicate`表示)评估一个`KStream`中的每条记录，并输出多个`KStream`的数组。这里的关键是您可以使用多个谓词而不是一个——就像`filter`和`filterNot`的情况一样。

你可以将两个`merge`合二为一。

```
StreamsBuilder builder = new StreamsBuilder(); KStream<String, String> stream1 = builder.stream("topic1");
KStream<String, String> stream2 = builder.stream("topic2");stream1.merge(stream2).to("output-topic");
```

**注意:**产生的流可能没有按顺序排列所有记录。

如果您想为您的`KStream`中的每条记录派生一个新的键(它也可以有不同的类型)，使用`selectKey`方法，它接受一个`KeyValueMapper`。`selectKey`与`map`相似，但区别在于`map`将返回类型限制为一个`KeyValue`对象。

```
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> stream = builder.stream(INPUT_TOPIC);stream.selectKey(new KeyValueMapper<String, String, String>() {
            @Override
            public String apply(String k, String v) {
                return k.toUpperCase();
            }
        })
```

在使用 Kafka Streams DSL 开发处理管道时，您会发现自己使用`to`将结果流记录推送到输出主题，然后从该(输出)主题创建一个新的流:

```
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> stream1 = builder.stream(INPUT_TOPIC);
stream1.mapValues(v -> v.toUpperCase()).to(OUTPUT_TOPIC);//output topic now becomes the input source
KStream<String, String> stream2 = builder.stream(OUTPUT_TOPIC);//continue processing with stream2
stream2.filter((k,v) -> v.length > 5).to(LENGTHY_WORDS_TOPIC);
```

这可以通过使用`through`方法来简化。因此，您可以将上面的内容重写如下:

```
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> stream = builder.stream(INPUT_TOPIC);stream.mapValues(v -> v.toUpperCase())
      .through(OUTPUT_TOPIC)
      .filter((k,v) -> v.length > 5)
      .to(LENGTHY_WORDS_TOPIC);
```

在这里，我们将记录(具有大写值)具体化为一个中间主题，并继续处理(在本例中使用`filter`，最后将后置过滤结果存储在另一个主题中。

暂时就这样了。请继续关注本系列即将发布的文章！

# 参考

请不要忘记查看以下 Kafka Streams 资源。

*   [卡夫卡文献](https://kafka.apache.org/documentation/streams/)
*   [汇合单据](https://docs.confluent.io/current/streams/index.html)
*   [卡夫卡流 Javadocs](https://kafka.apache.org/24/javadoc/index.html?org/apache/kafka/streams/)