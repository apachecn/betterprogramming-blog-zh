# 开闭原理

> 原文：<https://betterprogramming.pub/solid-swift-by-examples-part-two-82ac3c457e4e>

## Swift 实例中的坚实原则

## 坚固的雨燕

![](img/b935d60ff4ccbe47114d0b1f9baf4c99.png)

芬恩·哈克肖在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在第一部分中，我们描述了什么是可靠原则以及它们试图解决什么，解释了第一个原则，*单一责任*。

[](https://medium.com/better-programming/solid-swift-by-examples-part-one-35018d53d3e6) [## 稳固和单一责任原则

### 坚固的雨燕

medium.com](https://medium.com/better-programming/solid-swift-by-examples-part-one-35018d53d3e6) 

在这一部分，我们将探讨以下原则:

开闭原理是由 Bertrand Meyer 在 1988 年定义的。他说:

> "软件实体应该对扩展开放，但对修改关闭."

这个概念应该扩展并应用于任何其他模块、类或函数。这并不像听起来那么简单。

作为开发人员，我们总是有新的需求，其中一些需要更改或扩展现有的功能和业务逻辑，因此我们需要找到一种方法，在添加或更改代码时保持代码的良好状态。

因此，每当我们需要修改现有代码时，开闭原则就会发挥作用。

Bertrand Meyer 提议使用继承来实现这个原则，但是 Robert C. Martin 和其他人重新定义了它，使用多态形式。

我更喜欢使用现代的方法，包括组合、接口(协议)、依赖注入和设计模式(作为装饰者)。

让我们看看下面的例子:

一个简单的投资管理器类

我们有一个投资计算器在生产与以前的代码，遵循以前的做法(单一责任)。我们将投资回报抽象成一个名为`InvestmentManager`的类。

但是，我们需要增加一种新的投资。我们必须改变`InvestmentManager`:

高级投资实施

这段代码已经开始有味道了。如果我们有一种新的投资方式，我们会一遍又一遍地改变`InvestmentManager`。

我们违反了开闭原则，因为它不是封闭的，不能修改，也不能扩展。每次出现新的投资类型，我们都必须修改这个类。我们必须避免这种情况。

为了解决这个问题，我们将从一个接口(协议)`Investment`继承，并且我们将使用*装饰模式*添加一个新的投资类(我们也可以用继承来处理这个问题)。

每个新的投资都将实现这个协议，`InvestmentManager`将依靠这个抽象遵循开闭原则。

由协议驱动的投资和高级投资

我们已经对`InvestmentManager`进行了修改，允许它在修改时关闭，在扩展时打开。

如果我们需要增加一个新的`Investment` `Class`，那么`InvestmentManager`就不需要改变，因为它通过`Investment`接口开放扩展。

一个有趣的事情是`AdvancedInvestment`。通过使用装饰模式，我们可以使用来自`GeneralInvestment`的基本属性和实现`Investment`协议来处理一种新的投资方式。

# 结论

我们使用了开闭原则，使我们的代码更容易维护和扩展。有时候，当我们写代码时，我们看不到创建抽象的需要，但是我们可能会需要它。

我希望你喜欢这篇文章，它对你有帮助。感谢阅读。

# 下一章

[](https://medium.com/@piero9212/solid-swift-by-examples-part-three-675672c1ec20) [## 利斯科夫替代原理

### 这是关于坚实原理的出版物的第三部分。在以前的文章中，我们讨论了什么是固体…

medium.com](https://medium.com/@piero9212/solid-swift-by-examples-part-three-675672c1ec20) 

# 参考

[](https://cactussoft.biz/blog/2018/01/16/ios-best-practices-s-o-l-i-d/) [## iOS 最佳实践。第四部分:神盾局

### 作为第 3 部分:架构的延续，我们来谈谈 S.O.L.I.D .原则以及如何在 Swift 中应用它们…

cactussoft.biz](https://cactussoft.biz/blog/2018/01/16/ios-best-practices-s-o-l-i-d/) [](https://codeburst.io/understanding-solid-principles-open-closed-principle-e2b588b6491f) [## 理解固体原理:开放封闭原理

### 这是理解坚实原理系列的第三部分，我们将探讨什么是开闭原理和…

codeburst.io](https://codeburst.io/understanding-solid-principles-open-closed-principle-e2b588b6491f) 

*   [https://web . archive . org/web/20150905081105/http://www . object mentor . com/resources/articles/OCP . pdf](https://web.archive.org/web/20150905081105/http://www.objectmentor.com/resources/articles/ocp.pdf)