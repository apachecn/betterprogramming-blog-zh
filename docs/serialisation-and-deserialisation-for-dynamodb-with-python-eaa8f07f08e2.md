# 用 Python 实现 DynamoDB 的序列化和反序列化

> 原文：<https://betterprogramming.pub/serialisation-and-deserialisation-for-dynamodb-with-python-eaa8f07f08e2>

## 使用 Python 3 和 Boto 3 库的 DynamoDB 教程

![](img/08efe8aeb52c9b3a8bbf353a996706ed.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Dmitry Ratushny](https://unsplash.com/@ratushny?utm_source=medium&utm_medium=referral) 拍摄

# 介绍

什么是序列化和反序列化？通俗地说，这就像一种语言翻译，这样我们的观众就能理解我们在说什么。

例如，假设你不是说英语的人，你被要求向说各种不同语言的观众做一个演示，但是他们都懂英语。你会有一个翻译，不是吗？

所以，当你用你的母语说话时，你的翻译会把它翻译成英语，这样听众就能理解了。这是序列化部分。

相反，当你的听众问你一个问题时，你的翻译会把它翻译成你的母语，这样你就能明白问题是什么。这是反序列化部分。

我希望这个类比有意义，并帮助您理解序列化和反序列化的概念，尤其是如果您是编程新手的话。(我刚开始做程序员的时候很难理解它，所以我支持你！)

# 你将学到什么

我们将学习如何以手动方式和`Boto3`包方式序列化 Python 对象和反序列化 [DynamoDB](https://aws.amazon.com/dynamodb/) 项。

当我们使用 [Boto 3](https://boto3.readthedocs.io/) 的`TypeSerializer`和`TypeDeserializer`时，将 Python 对象序列化为 DynamoDB 理解的类型并将 DynamoDB 项反序列化为 Python 对象是轻而易举的事情。

所以，让我们看看如何在我们的代码中使用它们。

# 用类型序列化器序列化

在这个上下文中，序列化是将 Python 对象转换成与 DynamoDB 兼容的格式的过程。

当我们想要做`PutItem`和`UpdateItem`时，这是必要的，因为我们需要告诉 DynamoDB 创建和/或更新什么。

下面是一个简单的 Python 脚本，它执行`PutItem`。

[serialise_dynamo.py](https://gist.github.com/billydh/31d2bded5cb0e49b21d69f0a1b6adc99)

你能看到我们必须显式地写每个字段吗，例如`"lastSeenAt": {"S": item.lastSeenAt}`？这意味着每当我们有一个新的字段时，我们都需要添加到字典中，当我们有很多字段时，这可能是很痛苦的。

那么，有什么更简单的方法呢？

嗯，Boto 3 的`TypeSerializer`来了。

这是我们实现了`TypeSerializer`后代码的样子。

[串行 _ 发电机 _v2.py](https://gist.github.com/billydh/18db5b2cd303b26abc84fbab741e0510)

简洁多了，不是吗？更不用说，我们不必担心新的领域，只要我们保持我们的`dataclass`，这是我们应该的。

注意，我们在字典理解中添加了一个条件`if v != ""`。

原因是我们可以处理空字符串输入，这让 DynamoDB 很高兴，否则 DynamoDB 会向我们抛出一个异常。大概是这样的`AttributeValue may not contain an empty string`。

酷！缩短我们的代码感觉很好，不是吗？

太好了！让我们继续讨论反序列化。

# 使用 TypeDeserializer 进行反序列化

在这个上下文中，反序列化意味着将 DynamoDB 项转换为 Python 对象。当我们做`GetItem`和/或`QueryItem`时，这是必要的。

当我们调用`GetItem`或`Query`时，DynamoDB 会返回类似这样的结果。

```
{
    "Item": {
        "postedBy": {"S": "someguy@example.com"},
        "postedAt": {"S": "1580528217"},
        "forum": {"S": "PythonDynamo"},
        "subject": {"S": "How to serialise and deserialise for DynamoDB?"},
        "message": {"S": "This is how you can do serialisation and deserialisation for DynamoDB."}
    }
}
```

因此，我们需要处理这种类型的对象，并相应地将其反序列化到我们的`ForumUser`类中。

下面是我们如何编写代码来处理反序列化。

[deserialise_dynamo.py](https://gist.github.com/billydh/936d2f91a72acb869969b0c2c10ae8fb)

上面的代码运行良好，但是让我们看看如何用`Boto3`包中的`TypeDeserializer`来简化我们的代码。

这是我们使用`TypeDeserializer`时代码的样子。

[deserialise _ dynamo _ v2 . py](https://gist.github.com/billydh/8cd2cd335cd705804825fcdfc7893440)

这里使用`TypeDeserializer`的好处是我们可以避免像手动反序列化那样使用嵌套的`dict`理解。

注意，我们将`unknown=EXCLUDE`作为参数添加到`ForumUser.Schema().load()`函数调用中。这个参数告诉`Schema`当有一个字段没有被识别，或者换句话说，没有在`dataclass`定义中定义，它将忽略它。

如果没有这个参数，`marshmallow`将抛出一个类似于`marshmallow.exceptions.ValidationError: {‘<undefined_field_name>: [‘Unknown field.’]}`的异常。

不管怎样，很努力！

# 包裹

在本文中，我们已经介绍了如何为 DynamoDB 进行序列化和反序列化，既使用了手动方式，也使用了`TypeSerializer`和`TypeDeserializer`。

如果你想在一个真正的 DynamoDB 表上使用上面的函数，你可以设置一个本地 DynamoDB Docker。

转到 [*如何在 Docker 容器*](https://medium.com/better-programming/how-to-set-up-a-local-dynamodb-in-a-docker-container-and-perform-the-basic-putitem-getitem-38958237b968) 中设置本地 DynamoDB 以获得完整的演练。

我希望这能让您对如何编写处理 DynamoDB 的代码有所了解。

感谢阅读！