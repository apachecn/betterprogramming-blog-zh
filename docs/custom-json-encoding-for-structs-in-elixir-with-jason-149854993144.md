# Jason 为 Elixir 中的结构定制 JSON 编码

> 原文：<https://betterprogramming.pub/custom-json-encoding-for-structs-in-elixir-with-jason-149854993144>

## 酏剂开发者简要指南

![](img/625a90b98fad60f07809ee9913811563.png)

Artem Maltsev 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 杰森。

[Jason](https://github.com/michalmuskala/jason) 是 Elixir 中一个主动维护的 JSON 解析器和生成器。如果你想处理 JSON 结构以发送给你的客户，或者只是围绕你的 Elixir 应用程序管理数据，你可能会想到这个或者[毒药](https://github.com/devinus/poison)——这篇文章的内容也与此相关。

# 目的和使用案例

在 Multiverse，我们正在开发现有第三方集成的新版本，并将其作为我们核心平台的一部分。正在讨论的特性并不重要。重要的是要知道有大量的数据——想想数百万条记录。

一个问题是我们不能直接访问现有系统的数据库——我们只能将数据导出到 CSV 文件中！

谢天谢地，每个记录只有 8 个左右的字段，所以我们可以借助一个良好的老式 CSV 导入程序来导入数据。

简单吧？不对。

在 PSQL 中导入一百万甚至更多的数据行并不太糟糕，但是在我们进行导入时，它会降低速度并可能锁定应用程序的其余部分——这很糟糕！我们还需要为我们的新实现将数据转换成更合理的数据结构——旧版本有一些不一致和类型问题，我们希望消除这些问题。例如，日期用纪元时间和非必需数据字段表示。

所以现在我们需要转换和导入每条记录，这需要时间。为了减轻这一点，我们将使用 [RabbitMQ，一个开源消息代理](https://www.rabbitmq.com/)。通过将 CSV 中的每一行打包成一条 JSON 消息，将它们作为消息异步发送，并让 RabbitMQ 消费者在它们并发到达时处理它们。

如果你有兴趣了解更多关于兔子的知识，请留下你的评论。

那么，Jason 编码从何而来？

请记住，我们希望转换我们的数据。将所有内容导出到 CSV 文件的问题是，当我们将它读入我们的应用程序时，我们将把所有内容作为字符串读取。现在，转换日期字段需要我们手动调用`String.to_integer/2`，这是我们不希望手动完成的事情。想象我们有 100 个字段要导入！

好的程序员是懒惰的程序员。

# 定义我们的结构

```
defmodule LegacyLog do
  defstruct [:id, :user_id, :legacy_log_id, :time, :notes, :date, :inserted_at, :updated_at]
end
```

这里我们所拥有的只是一个简单的 Elixir struct，其中包含了我们希望从 CSV 导入中处理和解析的每个记录的字段。

在 CSV 导入中使用它:

是的，当我们导入数据时，我们可以在这里转换数据，但是良好的 RabbitMQ 实践包括假装我们的消息来自我们无法直接访问的外部某个地方——即使在这种情况下我们自己在生产和消费它们。

# 定制编码 JSON

你真正想要的东西。

我们需要利用`Jason`定义`Protocols`的方式来定义如何处理我们的编码的自定义实现。

在我们之前定义的结构的基础上，我们将为结构本身添加一个`defimpl`,并指定我们想要做的事情:

在上面的代码中，我为类型`LegacyLog`定义了一个实现，这意味着当`Jason.Encode/3`调用到来时，它知道调用我的自定义代码。任何其他编码函数调用都将使用默认实现(这对于大多数用例来说是合适的)。

自定义代码本身是:

*   将我希望保留为字符串的字段从结构中取出，并保留其余的字段
*   将剩余的`Struct`转换成`Map`
*   遍历每个字段并将它们转换为整数值
*   把笔记放回去
*   使用保证有效 JSON 的`Jason.Encode`进行`iodata`生成。

我们完了！

```
**Want to Connect?**Subscribe to my [Substack](https://chrisgregori.substack.com/) for similar content and [head over to my Twitter](https://www.twitter.com/codestirring) for more Elixir (and general programming) tips.
```