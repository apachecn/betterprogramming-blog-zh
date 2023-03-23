# 如何使用“DispatchWorkItem”和“DispatchGroup”查询多个 API

> 原文：<https://betterprogramming.pub/how-to-query-multiple-apis-with-dispatchworkitem-and-dispatchgroup-41a757692367>

## 使用“DispatchWorkItem”和“DispatchGroup”构建一个简单的搜索屏幕，以便对多个源进行多个异步服务调用

![](img/572bddd9d41d0412be731cfa9d6a4706.png)

照片由[在](https://unsplash.com/@thisisengineering?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) [Unsplash](https://unsplash.com/collections/9641853/i-am-a-creator%3A-chaitra-radhakrishna?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

你可以在这里找到这篇文章的组合框架版本

[https://medium . com/better-programming/how-to-query-multiple-API-with-the-combine-framework-1c4e 0298418 e](https://medium.com/better-programming/how-to-query-multiple-apis-with-the-combine-framework-1c4e0298418e)

有时，您希望在一个动作被触发时完成一项任务，但您无法区分该动作和完成事件。

换句话说，有些动作会被触发，但是你不知道这些动作是否是最后一个。

在这种情况下，您要做的就是延迟启动任务。如果事件继续发生，您可以取消之前的任务并开始新的任务。

![](img/8955c338b5ee99909d9f6c3448afb551.png)

自动完成是这种情况的一个很好的例子。每次按键时，自动完成功能都会提供一些快捷方式选项。很有用。这样的功能让用户体验更好。

那么我们能造出这样的东西吗？嗯，你每敲一次键就打一个服务电话，取消前一个电话。但是，根据网络速度或打字速度，您可能无法按时取消您的请求。

如果您的请求在您取消之前就到达了您的服务器，这将为您的服务器创建太多的请求来处理，最终可能导致您的服务器过载。你不想那样。

你能做的就是使用延迟机制。我们将建立一个搜索屏幕，用户可以写一个主题，并看到与该主题相关的图像。我们将查询两个不同的源(用于 [Unsplash](https://unsplash.com/documentation#example-image-use) 和 [Pexels](https://www.pexels.com/api/documentation/) 的 API)。让我们从基础开始。

# ' DispatchWorkItem '

使用`[DispatchWorkItem](https://developer.apple.com/documentation/dispatch/dispatchworkitem)`，您可以在`DispatchQueue`中运行您的代码块，并随时取消它(在任务完成之前)。

## *样本代码*

你可以在你的操场上试试这段代码，然后观察结果。

假设您在每次击键时都调用了`search`函数。它可能在一个`UITextFieldDelegate`或`UISearchController`方法中。`search`功能将完成这项工作。

这段代码已经足够好了。然而，这不是完美的解决方案。用户键入的速度可能太慢，以至于一秒钟的操作延迟可能不够。

记住，这里有两个异步操作。一个是延迟，一个是网络运营。我们总是取消第一个。如果我们能继承`DispatchWorkItem` 和来覆盖取消方法就好了。

如果这样做，我们也将取消我们的网络运营。但是我们不能这样做，因为出于某种原因`DispatchWorkItem`不是一个公开课。如果你尝试，你会看到这样的错误信息。

```
Cannot inherit from non-open class 'DispatchWorkItem' outside of its defining module
```

别担心。您仍然可以使用操作 API 通过[取消您的网络操作并延迟您的操作。是苹果提供的另一个 API。在这篇文章中我不打算详细讨论操作。如果那是你正在寻找的，你可能想要检查其他文章或博客。](https://developer.apple.com/documentation/foundation/operation)

# '调度组'

 [## 调度组

### 声明组允许您聚合一组任务并同步组上的行为。您附加了多个…

developer.apple.com](https://developer.apple.com/documentation/dispatch/dispatchgroup) 

使用`DispatchGroup`，您可以观察多个异步操作作为单个任务的结果。

让我们假设您需要发出两个网络请求来收集关于您的用户的信息。当然，您可以用一个 bool 变量来处理这种情况。但是不要这样做！如果你必须发出两个以上的网络请求，那就很难管理了。有一种更简单、更优雅的方法可以做到这一点。

输出:

```
**getImages starting...
getFollowers starting...
Notify starting...
getImages finishing...
getFollowers finishing...
Notify finishing...**
```

# 让我们把它们放在一起

我们将使用`DispatchWorkItem`和`DispatchGroup` 对不同的 API 进行多个异步服务调用。我已经使用了 [Unsplash](https://medium.com/u/2053395ac335?source=post_page-----41a757692367--------------------------------) 和[Pexels](https://medium.com/u/5c7773c5a002?source=post_page-----41a757692367--------------------------------)API 来构建下面的示例。当然，我们可以改进 UX，让它看起来更好。但不是今天的话题，我就不说了。

![](img/363fba9636e86bd01371e0c1b139386f.png)

回到代码:

我们一直等到查询字符串超过三个字符。然后，我们取消任何等待的请求，并创建一个新的请求。之后，我们在`services`数组中搜索来自每个 API 的查询字符串。我们使它异步，这意味着所有的请求是同时(几乎)发出的。

在第 22 行，我们等待每个 API 的结果，并用 URL 的组合数组调用完成回调。这个过程不会立即运行。相反，它封装在`DispatchWorkItem`中。每次调用第 28 行的`search`函数时，这个`DispatchWorkItem`的执行都会延迟。

最终，`DispatchWorkItem`将被执行，完成将被调用。正如您所看到的，您可以使用本机 API 轻松地将来自多个数据源的多个网络调用结合起来。你可以在底部找到完整的源代码。

# 结论

对于这类任务，您不需要使用第三方库。本机库可以很容易地做到这一点。我建议您首先学习本机方法，然后决定是否使用第三方库。感谢您的阅读。

[](https://github.com/ibrahimoktay/AsyncProgramming) [## Ibrahim oktay/异步编程

### 演示应用程序-使用 DispatchWorkItem 和 DispatchGroup。通过创建……为 ibrahimoktay/async 编程开发做出贡献

github.com](https://github.com/ibrahimoktay/AsyncProgramming)