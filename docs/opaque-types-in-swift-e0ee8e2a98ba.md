# 在 Swift 中探索不透明类型

> 原文：<https://betterprogramming.pub/opaque-types-in-swift-e0ee8e2a98ba>

## 一个间谍任务的例子

![](img/d0631e27af33fe5d7f364749b1bbc5ce.png)

由 [Sergiu Nista](https://unsplash.com/@sergiunista?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了理解不透明类型，我们将举一个例子。

想象一下，你是一个秘密服务组织的头，你想把你的间谍派到一个敌国。下面是开始使用的代码示例:

当你派出你的间谍时，你不想让你的敌国知道真实身份。你只是想让他们知道有人已经成为他们国家的新移民。

为此，我们创建了一个名为`Person` 的协议，现在我们将使用这个协议来隐藏间谍的真实身份。

现在，敌国再也看不到我们发送给他们的对象的原始类型。所以，我们现在成功地隐藏了我们的间谍。

其次，由于我们的间谍在敌国时将使用假名，我们不能再用他的名字来识别他。所以，我们需要某种类型的`ID`，不管他现在在哪里，它都会保持不变。

`[Identifiable](https://developer.apple.com/documentation/swift/identifiable)`协议是一个很好的选择，它要求我们提供某种数据类型的`id`变量。

`associatedtype ID`意味着符合协议的对象决定 ID 的数据类型。它可以是字符串、整型、浮点型等。

我们现在更新`Person`协议和我们的`Spy`对象以符合`Identifiable`协议。

不幸的是，我们现在在我们的`sendPersonToEnemyCountry()`函数中有一个可怕的错误:

```
“Protocol **Person** can only be used as a generic constraint because it has Self or associated type requirements
```

![](img/1572d982656d87a9e2e464d088dc68da.png)

# 那是什么意思？我们应该取消我们的间谍任务吗？

如果你读完这篇文章的其余部分，你就不需要这么做了。让我们看看如何挽救这个任务。

首先，让我们试着理解这里的问题。

Swift 说我们不能再使用`Person`协议作为我们函数的返回类型，因为它*不能计算出我们关联类型“ID”的数据类型*。因为关联类型本质上是一个泛型参数，所以它可以属于任何数据类型。

Swift 是一种类型安全语言，当它不能理解数据类型时会报错。

我们可以通过更新我们的`sendPersonToEnemyCountry()` 函数来返回一个数据类型`Spy`来解决这个问题，但是这会破坏我们的任务，因为我们的敌国会看到我们在给他们派间谍。

因此，我们需要的是一个解决方案，它允许我们从我们的`sendPersonToEnemyCountry()`函数返回我们的 Spy hidden by person 协议，但同时克服我们以前的错误:*协议 person 只能用作一般约束，因为它有 Self 或关联类型需求* ***。***

## 幻影侠来救援了。！

现在，让我们更新我们的函数，使用一个不透明的返回类型。

要编写一个不透明的返回类型，从关键字`some` 开始，然后是实际的返回类型。

现在编译没有任何错误。我们完成了任务，我们的间谍已经渗透到了敌国。但是，在我们庆祝胜利之前，让我们试着理解不透明类型是如何扭转局面的。

我们之前的错误是 Swift 无法确定我们关联的类型 ID 的数据类型。当我们切换到不透明的返回类型时，Swift 开始查看我们的函数声明，以找出我们实际返回的是什么。在这种情况下，我们返回一个`Spy`对象，它有一个关联的 ID 类型 String。所以，错误现在消失了。

尽管 Swift 现在确切地知道我们实际上从我们的函数返回了什么，但接收方(`Enemy` country)并不知道发送的实际数据类型(`Spy`)，因为它仍然是由`Person`协议抽象的。

协议和不透明返回类型之间仍有一些差异，如下所示:

上述代码会导致错误。Swift 不高兴，因为现在它不知道从我们的`sendPersonToEnemyCountry()`函数中实际返回的是什么数据类型。

> *对待具有不透明返回类型的函数，就像对待具有具体返回类型的函数一样。总是从函数返回单一数据类型。*

总之，不透明的返回类型有助于我们保留协议(抽象)的好处，同时允许我们仍然将它用作返回类型，即使协议有自身或相关的类型需求。

感谢阅读！

```
**Want to Connect?**Contact me on [LinkedIn](https://www.linkedin.com/in/karthik-shiva-ab5904124/)
```