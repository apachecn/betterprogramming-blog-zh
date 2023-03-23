# 使用 Swift 了解 5 大原则

> 原文：<https://betterprogramming.pub/swift-s-o-l-i-d-21203ba3a226>

## 使用面向对象的类设计增强您的 Swift 代码库

![](img/f61fdd2d32ddd3550db02629cf8e7235.png)

照片由[威森王](https://unsplash.com/@wesson?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

一段时间以来，我一直在努力提高我的代码质量，编写更具可读性和可开发性的代码。这个过程中最重要的一步是在代码中应用坚实的原则。让我们看看这个固体是什么？

固体是面向对象类设计的五个原则。它是设计结构时要遵循的一组规则和最佳实践。

# **(S)单一责任原则**

基本上，这个原则强调每个开发的模块只承担一个责任。对象和/或类从一开始就只负责一项任务。

让我们先来看看不符合 SRP 的代码，在正常情况下工作。

我们有一个`DataHandler`类，它创建数据，解析数据，最后保存它创建的数据。如果您获取这段代码并尝试运行它，它将毫无问题地编译。那么这里的问题是什么呢？

当我们查看代码时，`DataHandler`类有多种职责，比如解析、保存和创建数据。

作为解决方案，可以将当前职责转移到不同的类别:

# **(O)开/关原理**

简单地说，实体应该对扩展开放，但对修改关闭。

解释有时会令人困惑。让我们回顾一下代码:

让我们想象我们有一个`PaymentManager`。让这个经理在第一阶段支持现金和 Visa 支付方式。到目前为止一切都很好。过了一段时间，我们不得不更新经理，我们预计将万事达卡功能作为一个新的功能。

让我们像前面的函数一样创建一个名为`makeMasterCardPayment`的函数。太好了，我们的代码将继续工作。我们遵守了要求，但是我们打破了一个规则，即类必须关闭才能修改。对于做类似工作的任务，我们不应该在类中添加任何新的东西。

让我们看看如何解决这个问题:

让我们在抽象结构(协议)中定义`PaymentManager`中的主要任务，这个结构将回答`PaymentManager`期望的需求。

我们还为每种支付方式创建了一个单独的类，这些类将采用`PaymentMamager`所期望的抽象结构。因此，我们可以添加尽可能多的新支付方式，而无需对管理器进行任何更改。

所以我们保持`PaymentManager`类对扩展开放，但对修改关闭。

# **(L)利斯科夫替代原理**

对象应该在不改变行为的情况下被其子类的实例替换。在这个简短的解释之后，我们来谈谈代码。

假设我们有一类矩形，矩形有一个宽度和一个高度，它们的乘积等于面积。

我们有没有 square 类，理论上正方形是矩形，所以我们可以从类 rectangle 继承类 square。到目前为止一切都很好。

下面的`setSizeAndPrint`函数需要一个 rectangle 类型的变量，并默认分配矩形的宽度和高度。为 rectangle 类调用这个函数没问题，因为`width = 4`、`height = 5`、`area = 20`。

但是对于从 rectangle 类继承的正方形来说，情况就不一样了，因为正方形的两边是相等的。我们不能只是默认分配 4 和 5，并期望它像它继承的类一样工作。

在这一点上，不能作为继承类并且需要特定情况开发的类破坏了 LSP。

作为一种解决方案，它旨在通过将类之间的共同任务保持在某个抽象结构(协议)中，让每个类在其内部执行自己的任务。

如上例所示，`Rectangle`和`Square`类之间的共同任务是计算对象的面积。rectangle 和 square 类都继承了 Polygon 抽象结构，这是在一个公共协议中定义的任务。因此，每个类都在自己内部完成必要的任务，不需要进行任何特殊的开发。类的行为就像它们继承的结构一样。

# **(一)界面分离原理**

总之，不应该强迫客户依赖他们不使用的接口。不应该强迫任何代码依赖它不使用的方法。

让我们直接进入代码，实际地看看问题。

让我们有一个名为`Worker`的抽象结构，一般来说，我们期望那些从 Worker 类继承的人能够完成吃饭和工作任务。

首先，让我们有一个名为`Human`的类，这个类继承自抽象结构工人。理论上，我们期望一个人既吃饭又工作。然后，我们需要一个机器人结构，我们从工人结构继承了它，因为机器人可以工作。

问题从这里开始，因为工人协议有两个功能，一个是工作，一个是吃饭，工作功能没有问题，因为机器人可以运行，但因为我们从工人结构继承，我们必须添加吃饭功能，这导致了不必要的责任被传递给类。现在是 ISP 休息时间。

为了解决这个问题，我们必须将具有抽象结构的职责划分为基本部分。

我们正在为 eat 函数创建一个名为`Feedable`的新抽象结构，为 work 函数创建一个名为`Workable`的抽象结构。因此，我们分工负责。

现在`Human`类将从`Feeble`和`Workable`继承，而`Robot`类仅从`Workable`继承。

因此，我们不会给任何职业强加任何不必要的责任，我们创建了一个适合 ISP 的结构。

# **(D)依存倒置原则**

DIP 理论上高层模块不应该从低层模块导入任何东西。两者都应该依赖于抽象，而抽象不应该依赖于细节。细节应该依赖于抽象。

让我们看看下面的例子和理论信息。

我们有一个`employee`结构，这个结构有一个功函数。我们还有一个`Employer`结构，这个结构使现有员工能够工作。
run 函数中创建了一个`employer`对象，默认情况下，它接受数组`Employee`。同样，到目前为止一切都很好，可能我们的项目会成功，但我们在这里错过了一些东西。`Employer`结构直接链接到非抽象`Employee`结构。这就是我们需要蘸酱的地方。

使用 DIP 的理论知识，我们知道结构应该依赖于抽象模型。

因此，我们创建了一个抽象的`Workable`结构，并将`Employee`类依赖于`Workable`，这样`Employee`结构就保留了它原来的功能。

关键是`Employer`类现在期望抽象结构`Workable`的数组，而不是数组`Employee`。因此，我们已经将`Employer`结构的依赖关系链接到一个抽象模块。这意味着`Employer`结构已经到了可以依赖`Workable`模块运行任何结构的地步。

我希望你喜欢这篇文章。你可以在我的 GitHub 上找到所有代码:

[](https://github.com/MCTOK1903/SwiftSolid) [## GitHub - MCTOK1903/SwiftSolid

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/MCTOK1903/SwiftSolid) 

# 参考链接

[**1** 。https://en . Wikipedia . org/wiki/Dependency _ inversion _ principle](https://en.wikipedia.org/wiki/Dependency_inversion_principle)

[**2** 。https://www.youtube.com/watch?v = rndi Yu 8 if-I&list = PL _ CsA ao 9 pq 8 zih 89 p 2 re 5 fzix 9 kai 8 yhx&index = 1](https://www.youtube.com/watch?v=rndiYu8If-I&list=PL_csAAO9PQ8ZIh89P2re5fziX9kaI8Yhx&index=1)

**3** 。[https://en.wikipedia.org/wiki/SOLID](https://en.wikipedia.org/wiki/SOLID)

**4** 。[http://www . principles-wiki . net/principles:open-closed _ principle](http://www.principles-wiki.net/principles:open-closed_principle)

**5** 。[https://en . Wikipedia . org/wiki/Dependency _ inversion _ principal](https://en.wikipedia.org/wiki/Dependency_inversion_principle)