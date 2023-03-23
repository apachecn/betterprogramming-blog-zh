# Swift 中的 CurrentValueSubject 概览

> 原文：<https://betterprogramming.pub/currentvaluesubject-in-swift-16dabeed575>

## 在 iOS 中使用联合发布器

![](img/9339896587a12aa9e026028da97cc316.png)

照片由[印尼 UX](https://unsplash.com/@uxindo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/user-experience?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

苹果宣布新的两个发布者采用 Combine 框架分别是`CurrentValueSubject`、`PassthroughSubject`。

这些也称为定制发布者，iOS 开发人员可以创建并使用它们来绑定、订阅原始-非原始类型。

在这个故事中，我将用一个例子来解释什么是`CurrentValueSubject`。

# CurrentValueSubject

它是一个具有初始值的自定义发布者。

让我们设想一个屏幕，它将根据来自 web 服务的即将到来的数据显示一个空视图或非空列表。个人认为，在 iOS 项目中很难管理这些状态。

然而，由于国家驱动的发展**，**这比以前容易多了。让我们看看`CurrentValueSubject` 如何防止可能的国家驱动开发错误。

我们将定义一个名为`Serializable`的协议，它在 SwiftUI 列表视图中既可识别又可解码。

在这个定义之后，让我们定义一个名为`Model`的结构，它符合`Serializable`和`ServiceResponseState`以满足状态驱动的开发。

现在让我们创建一个满足定制发布者及其功能的视图模型。

我们可以看到，在`listDidLoad()` **中，** `send()` 因为`CurrentValueSubject` **的整理订阅事件被调用了两次。**

此外，正如我们在下面的控制台中看到的，虽然在调用`send(.finished)`之后调用了`resetState()` ，但是在 sink closure 中，并没有因为订阅终止而发生任何变化。

这可以防止在这种状态管理过程中出现任何错误。

![](img/a58964c334b2e177e8f831d75049b218.png)

安慰

我们在 Swift 编程语言的组合框架中看到了`CurrentValueSubject`的力量。我猜，使用这种棘手的东西是惊人的。