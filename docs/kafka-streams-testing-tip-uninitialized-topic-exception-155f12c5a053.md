# 如何处理 Kafka 流中的“未初始化主题”异常

> 原文：<https://betterprogramming.pub/kafka-streams-testing-tip-uninitialized-topic-exception-155f12c5a053>

## 如何修复这个常见的异常

![](img/fa039b43e9ac68aec5754f58e4c8d0ea.png)

Florian Olivo 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

我之前的一篇[文章](https://medium.com/@abhishek1987/testing-kafka-streams-applications-1c5cb14c5376)讨论了测试 Kafka Streams 应用程序。这里有一个相关的提示，可能会派上用场！

在测试时，您可能会遇到`java.util.NoSuchElementException: Uninitialized topic`异常。最有可能的是，这是由于您调用了`TestOutputTopic.readValue`(例如，作为`assert`检查的一部分)。问题不在于异常本身，而在于错误消息`Uninitialized topic`，这可能会令人困惑。如果您的处理`Topology`没有像预期的那样工作，并且在您试图读取的主题(`TestOutputTopic`)中没有记录，则可能会弹出异常。

考虑这个例子:

```
TestInputTopic<String, String> inputTopic = ...;
TestOutputTopic<String, String> outputTopic = ...;inputTopic.pipeInput("foo", "bar");
assertThat(outputTopic.readValue(), equalTo("foobar"));
```

为了避免混淆，您应该在检查值之前和之后(可选)确认输出主题是否为空:

在断言`outputTopic.readValue()`之前使用`assertThat`(带有适当的错误消息)可以确保您得到一个可以理解的失败响应。像这样的东西:

现在`output topic was empty`有道理了...至少对我来说是这样！

如果你对卡夫卡感兴趣，请看看我以前的一些文章！