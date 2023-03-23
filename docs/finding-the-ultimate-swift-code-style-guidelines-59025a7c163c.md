# 寻找最终的 Swift 代码风格指南

> 原文：<https://betterprogramming.pub/finding-the-ultimate-swift-code-style-guidelines-59025a7c163c>

## 存在许多代码风格的指南。哪个适合你？

![](img/3428f911f55cd9500edcacc2c9a6403b.png)

Clark Van Der Beken 在 [Unsplash](https://unsplash.com/s/photos/patterns?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

T4:我一直是一个注重细节的人。当我阅读别人的代码时，我总是注意到它的格式低效、不一致，有时甚至是丑陋的。

好的代码风格的优点已经说过很多次了。简而言之，程序员读别人的代码比写自己的代码花的时间还多。这就是为什么它必须是可读的。

这是 [Bertrand Meyer](https://en.wikipedia.org/wiki/Bertrand_Meyer) 在他的 [*面向对象构造*](https://www.amazon.com/Object-Oriented-Software-Construction-Book-CD-ROM/dp/0136291554) 中写的:

> 您应该从开始编写类的时候就应用样式规则。例如，你不应该写一个没有直接包含头文件注释的例程。这不会花很长时间，也不是浪费时间:事实上，这是为你或其他人在课堂上的所有未来工作节省的时间，无论是半小时后还是五年后。使用规则的缩进、注释和标识符的正确拼写、适当的词汇约定——在每个左括号前留一个空格而不是在其后，等等——并不会使您的任务比忽略这些规则更长，但是经过几个月的工作和大量软件的计算会产生巨大的差异。对这样的细节的关注，虽然不充分，但却是优质软件的必要条件。

我有自己的 [Swift](https://swift.org) 代码风格指南，自从我用 Swift 写了第一行代码以来，它一直在发展。当我转换项目时，它一直伴随着我，从未让我失望过。但首先，让我告诉你一个隐藏在这些准则背后的故事。

# 起源

自从我看到 [Java 代码约定](https://www.oracle.com/technetwork/java/codeconventions-150003.pdf)以来，我一直在寻找完美的书面的，或者说是刻在石头上的 Swift 指南。我找不到合适的词来形容我有多感动！

我越读越糊涂。如果 Java 开发者有这个指南，为什么会有这么多丑陋的 [Java](https://www.java.com/) 代码存在？我仍然想知道这样的丑陋从何而来:

```
// Don't.**void** layoutScreen() {
    layoutHeader() layoutFooter()
}
```

*注:在本文中，当你在代码中看到一个* `*// Do*` *或一个* `*// Don’t*` *时，这些是我的建议。*

为什么我们在中间有那奇怪的令人困惑的空行？为什么不能是这样呢？

```
// Do.**void** layoutScreen() {
    layoutHeader()
    layoutFooter()
}
```

当然，Java 代码约定有许多过时的代码风格建议，这些在当今被认为是糟糕的风格:

```
// Don't.
function(longExpression1, longExpression2, longExpression3,
         longExpression4, longExpression5);
```

但是最终，这些约定对我来说是一个真正的进步，就像[代码完成](https://en.wikipedia.org/wiki/Code_Complete)。和巨大的灵感。

# 现有准则

有几个现有的 Swift 代码风格指南，但它们中的任何一个都足够可靠或详尽，足以涵盖我想要涵盖的所有内容。

首先有官方的 [API 设计指南](https://swift.org/documentation/api-design-guidelines/)。不要误解我，他们是完美的！但是你可以从它的名字中猜到，它主要关注于 [API](https://en.wikipedia.org/wiki/Application_programming_interface) s(命名、方法签名等等。)

## [谷歌的 Swift 风格指南](https://google.github.io/swift/)

另一方面，也有少数来自大公司的导游。我见过的最详尽的是谷歌的。不幸的是，我不能使用它，它有很多规则似乎是错误的！

例如，注释—尽管它们位于行首，但看起来仍然是隐藏的:

```
// Don't.@testable **import** MyModuleUnderTest@objc **private** **func** criticReviewWasTapped(_ sender: UIButton?) {
    // ...
}// Do.@testable
**import** MyModuleUnderTest@objc
**private** **func** criticReviewWasTapped(_ sender: UIButton?) {
    // ...
}
```

我总是希望看到注释分开。但是现在，当 Swift 拥有基于它们的丰富 API([属性包装器](https://docs.swift.org/swift-book/LanguageGuide/Properties.html#ID617))时，它就变得必要了。我不希望出现不一致的情况，当一个规则在一种情况下适用，而在另一种情况下不适用。

另一个例子是控制流语句。一个[布尔](https://en.wikipedia.org/wiki/Boolean_algebra)运算符在一行的末尾？加油！这已经被认为是坏的风格了，看看完整的代码！

```
// Don't.
**if** aBooleanValueReturnedByAVeryLongOptionalThing() &&
    aDifferentBooleanValueReturnedByAVeryLongOptionalThing() &&
    yetAnotherBooleanValueThatContributesToTheWrapping() {
    doSomething()
}// Do
**if** aBooleanValueReturnedByAVeryLongOptionalThing()
    && aDifferentBooleanValueReturnedByAVeryLongOptionalThing()
    && yetAnotherBooleanValueThatContributesToTheWrapping() {
    doSomething()
}
```

然而，我对谷歌的工作感激不尽！我通读了他们的指南，他们提示了我很多必须强调的东西！

## [raywenderlich.com 官方斯威夫特风格指南](https://github.com/raywenderlich/swift-style-guide)

这是一个非常受欢迎的，当我在寻找合适的使用指南时，我经常被它绊倒。

他们绝对没有试图做到面面俱到——这是第一件让我反感的事情。另一个原因是本指南是临时编写的。 [Ray Wenderlich](https://www.raywenderlich.com) 是一个著名的学习平台，它的指导方针与之一致:两个空格缩进以节省更多空间，等等。

另一个很容易把我吓跑的是前后矛盾。虽然指南建议使用类型推断，但它们也建议显式指定集合类型，并通过空文字初始化它(因此，不一致):

```
// Don't.
**var** lookup: [String: Int] = [:]// Do.
**var** lookup = [String: Int]()
```

## 其余的

还有 [LinkedIn 的 Swift 风格指南](https://github.com/linkedin/swift-style-guide)和 [Airbnb Swift 风格指南](https://github.com/airbnb/swift)都有同样的缺陷。

所以，这是一个关于我如何编写最终 Swift 代码指南的故事。

# 终极 Swift 代码风格指南

它有一个很好的目录，让我们很容易地导航。在我看来，这些指导方针是详尽的。我包括了所有我认为应该在那里的主题，排除了所有不属于那里的主题。

其中一个主要原则是，你不能忽视任何规则，但你可以根据你的需要改变它。例如，如果您喜欢另一种大括号样式，那很好，只要坚持您的样式并替换相应的规则。

所以，事不宜迟，[来了](https://github.com/lazarevzubov/Ultimate-Swift-Code-Style-Guidelines)！随便用，评论，叉！如果您有任何重要的建议，请使用拉式请求系统—让我们一起讨论！

如果你喜欢阅读我(和其他作者)在 Medium 上的博客，你可以**成为正式的 Medium 成员**(如果还没有) [**这里**](https://lazarevzubov.medium.com/membership) 。这样做，你就支持了所有的媒体作者。🙏

…或者你可以给我买杯咖啡！☕