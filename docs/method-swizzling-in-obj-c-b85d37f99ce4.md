# 理解 Obj-C 中的 Swizzling 方法

> 原文：<https://betterprogramming.pub/method-swizzling-in-obj-c-b85d37f99ce4>

## 什么是“方法切换”,什么时候应该使用该功能

![](img/1e7af0bc520d96d0dcf9babfbce5a918.png)

照片由来自 Unsplash 的 Belinda Fewings 提供

当我开始在遗留的 iOS 应用程序上工作时，我首先遇到的事情之一就是“方法切换”。

Swizzling 允许开发人员改变一个预先存在的方法的底层实现，而不必改变原来的实现。实际上，您可以用一种方法替换另一种方法。如果 JavaScript 开发人员曾经做过猴子补丁，他们可能对这个概念很熟悉。

如果您从基类继承，大多数现代面向对象和静态类型的语言都有重写现有方法的方法。这有时被称为多态性。但是如果你想改变原来的实现而不改变基类中原来的底层代码呢？

你可以在大多数苹果语言中使用 swizzling 方法来做到这一点。您可能会问自己，如果您有需要更改的方法的原始代码，为什么要这样做？主要原因是，如果你正在使用一个你不能改变或者没有权限改变的库，你至少可以在运行时使用 swizzling 来改变一个方法的行为。

# Objective-C 运行时

Objective-C 的运行时为在应用程序运行时执行操作提供了一些很好的工具。您可以使用选择器来创建对 Objective-C 中方法签名的引用，然后使用这些选择器来操作运行时。

在下面的例子中，我们有一个类，它有一个我们需要改变的方法。我们将调用这个类`TaxCalculator`和我们想要调用的方法`whatAreMyTaxes`:

现在我们将创建一个 Objective-C 类别，它类似于 Swift 中的一个扩展，为我们的`TaxCalculator`类添加一个新方法:

这个类别增加了一个叫做`swizzle_whatAreMyTaxes`的新方法。这将是我们用来调酒的原始方法`whatAreMyTaxes`的方法。

为了让我们的应用程序使用 swizzled 方法，我们需要在首次加载应用程序时交换方法。我们将使用 Objective-C 的`load`方法来交换我们的方法。类的`load`方法总是在应用程序初始化时执行。我们还需要确保它只被加载一次。

我们将使用 Grand Central Dispatch (GCD)来确保该方法只被加载一次。实现将类似于下面的示例:

看看上面的方法，让我们来分解一下我们为每一行做了什么。我们在`load`方法中做的第一件事是检查`self`是`TaxCalculator`类的一个实例。

一旦我们验证了在运行时加载了正确的类，我们就创建一个将在`dispatch_once`方法中使用的`dispatch_once_t`令牌。这个方法有两个参数，第一个是令牌的地址，第二个是闭包或块。

在 out `dispatch_once`闭包中，我们通过使用`Class`类型并调用`[self class]`来创建对当前类的引用。然后，我们可以为原始方法和 swizzled 方法创建两个选择器。在 Objective-C 中，我们使用`@selector`方法来获取对我们的方法签名的引用。

在定义了我们的选择器之后，我们将需要获得实际的方法引用。这是我们开始使用 Objective-C 运行时的地方。我们将使用`class_getInstanceMethod`方法为这两种方法创建引用。如果你正在重组类方法，有一个不同的运行时方法可以使用，叫做`class_getClassMethod`。这两种方法都将类引用和选择器引用作为参数。

现在我们有了两个方法引用，我们使用`method_exchangeImplementations`方法来混合它们。这种方法也可以用来将它们交换回来。

# Swizzle 实用程序

我创建了一个实用程序类，您可以使用它轻松地交换实例或类方法。下面是我的`SimpleSwizzleHelper`课:

使用这个助手类，我们现在可以重构我们的 load 方法来支持 swizzling。注意`SimpleSwizzleHelper`可以处理实例和类方法。

[](https://github.com/davidfekke/SwizzleExample) [## GitHub-davidfekke/Swizzle Example:一个 Swizzle 示例

### 这是我编写的一个示例项目，展示了如何使用 Objective-C 运行时简单地混合 Obj-C 方法。的…

github.com](https://github.com/davidfekke/SwizzleExample) 

# 结论

方法重组是 Objective-C 运行时的一个非常强大的特性。使用此功能时要小心。只有当您不能直接更改底层实现时，才应该使用这种方法。

任何使用该特性的开发人员都应该小心地剔除那些不知道原始实现是什么的方法，也就是说，您有头文件和静态库，但无法访问原始代码。我也会避免从苹果的操作系统中删除任何系统代码。如果使用不当，这可能会造成伤害。

既然你已经读了这篇文章，祝你喝得开心！

## 想联系作者？

本文原载于 [https://fek.io](https://fek.io/blog/method-swizzling-in-obj-c-and-swift/) 。你也可以在 YouTube 上查看[的视频教程。](https://www.youtube.com/watch?v=EubqhkP1ggw&t=1s)