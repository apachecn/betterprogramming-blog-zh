# 来自 WWDC 2022 的 3 个强有力的快速技巧

> 原文：<https://betterprogramming.pub/3-powerful-swift-tricks-from-wwdc-2022-8369a9a141c6>

## 最容易学的语言变得越来越容易

![](img/aba6c28d7905c243e377a12940c78912.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1207886) 的[马丁·沃雷尔](https://pixabay.com/users/martinvorel_com-2835811/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1207886)

在 Swift 5.7 中有很多新的技巧可以学习，但这些是我最喜欢的苹果 2022 年的 WWDC。随着语言的不断发展，编写以前重复的代码变得越来越容易。处理可选和泛型总是很容易，但是现在变得更加容易了。

看看这些简单的例子，你就会明白我的意思了。

# 使用“if let”而不重复可选的名称

Swift 5.7 之前的可选绑定采取的是`if let myOptional = myOptional`的形式。虽然我想保留可选的名称，因为我将它作为一个常量绑定在闭包中，但是我仍然需要重复这个名称。在 Swift 5.7 中，仍然可以编写类似于`if let myNewName = myOptional`的内容，但保留现有名称而不重复自己更容易。

这个视图有一个可选的`@State`属性，名为`displayedText`，我正在可选地将它与`if let`绑定。当该值等于`nil`时，以透明颜色的形式提供一个空白空间。这样即使没有设置值，在`Form`中也会有一行。

`TextField`设置`inputText`属性，`Button`用这个值更新可选的`displayText`。

# 对于符合具有关联类型的协议的任何类型，使用“any”关键字

我们都非常熟悉`any`关键字，但是它与`some`关键字有什么不同呢？Swift 5.7 中一个很大的不同是`any`现在可以用于关联类型的协议，这在以前是不可能的。为了测试它的局限性，我创建了一个名为`MyProtocol`的协议，它有一个关联的类型。

我创建了两个几乎相同的类型:`MyConformingType`和`MyOtherConformingType`。

我有另一个名为`MyType`的结构，它同时使用了`any`和`some`关键字来创建`MyConformingType`的实例。在 SwiftUI 视图中显示了它们的类型，并且一个`Button`提供了改变它们类型的能力。

这就是两个关键字之间的区别变得明显的地方，因为不可能改变`someExample`属性。

如果您在这个函数中的一个断点处停下来，您可能会看到`someExample`是 MyProtocol 类型。

我们最终得到了一个抽象类型，它只具有协议所要求的属性。

我试着将`someExample`设置为与`MyConformingType`完全相同的实例，即使这样也不被认为是相同的类型！

# 使用时钟 API 来延迟代码的执行

iOS 16 中有一个新的 API，可以更容易地处理和测量时间。

这里有一个简单的`Form`，它使用两种时钟:一个`SuspendingClock`，当应用程序进入后台时停止；一个`ContinuousClock`，它...继续。每个时钟都有一个`Stepper`用于选择您想要等待的时间，还有一个`Button`用于启动等待。

由于`sleep`函数可以抛出，我将它包装在一个`do-catch`中，它在一个`Task`中，因为否则，闭包将是异步的，不适合作为`Button`的动作。

```
**Want Daily Coding Tips like these?**These tips previously appeared in my newsletter [Type Safely](https://typesafely.substack.com/).
```