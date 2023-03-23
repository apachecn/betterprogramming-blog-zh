# SwiftUI 中的警报

> 原文：<https://betterprogramming.pub/alerts-in-swiftui-a714a19a547e>

## 如何在 SwiftUI 中高效呈现警报

![](img/9373855d1deda1b51623833598acaae5.png)

照片由[路易斯·佩尔迪戈](https://unsplash.com/@scalabis?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我最近在 app store 上发布了我的第一个用 SwiftUI 编写的应用。在开发过程中，发现如何创建一个单一的警报是非常容易的，有大量的在线资源。然而，当我的应用程序规模扩大时，我发现当显示警报的情况增加时，这种通用方法是不可靠的。

这促使我写了这篇文章来介绍我使用过的不同方法，从非常简单的警报到处理不同警报类型、连续警报和呈现警报的子视图的更有效的方法。

# 创建简单的警报

在 SwiftUI 中创建警报可以通过一个布尔类型的单个 *@State* 变量和。 *alert(isPresented)* 修改器。每当这个变量被设置为 true 时，视图将被重新加载并显示警报。一旦警报解除，Swift 会将变量设置回 false。

## **定制警报器**

警报的内容结束必须是类型`alert`。这些在复杂性上可以有所不同。默认情况下，提醒有一个标题和一个解除按钮，但可以定制为有一条消息和最多两个按钮，可以绑定到任何功能。

以下是一些例子:

使用自定义消息和消除按钮进行提醒。

使用自定义消息和主次按钮进行提醒。

## 为什么这种方法不可扩展

上面的方法工作得很好，但是在实践中，一个视图可能需要根据用户的动作显示不同的警告。在 SwiftUI 中，一个视图只能附加一个`.alert`修改器。

一个可能的解决方案是对每个单独的视图元素应用一个 alert 修饰符，但是这带来了重复代码和多个`@State` 变量的低效，这变得很难跟踪——特别是如果子视图也存在警报的话。

# AlertItem 简介

一个更好的解决方案是使用`.alert(item)`修饰符，它绑定到一个可识别的对象，每当可识别的项目不为 0 或者被更新时就会出现。

我喜欢使用自定义结构，它类似于 SwiftUI alert 结构，并且符合`Identifiable`协议。

这允许在需要时动态创建不同复杂性的警报，并且不需要多个`.alert`修饰符或`@State`变量。

然后，如果您的子视图也显示警告，您可以将`AlertItem`作为绑定进行传递，并确信行为应该没有什么不同。

# 在警报中显示警报

如果你想在另一个`AlertItem`的动作中改变一个`AlertItem`的值，希望它会消除第一个警告并显示第二个警告…它不会。

这似乎是一个需要解决的小问题；但是，在应用程序中，可能会出现以下形式的警报:

*你确定要<一些动作>？*

如果用户随后进行确认，但操作失败，即失去网络连接，那么显示另一个警告来说明发生了错误将会很有帮助。

解决方案是异步更新`AlertItem`的值。一旦第一个警报的操作完成，UI 将被更新。

如果你有任何问题，请在下面发表评论，如果你想在我下次写文章时得到通知，请加入我的时事通讯！👇

[](https://swiftlythinking.substack.com) [## SwiftlyThinking 的简讯

### 来自一位对 SwiftUI 和更多东西充满热情的 iOS 开发人员的博客帖子。

swiftlythinking.substack.com](https://swiftlythinking.substack.com)