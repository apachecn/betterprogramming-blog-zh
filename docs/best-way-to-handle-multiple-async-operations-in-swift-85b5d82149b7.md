# 如何在 Swift 中处理多个异步操作

> 原文：<https://betterprogramming.pub/best-way-to-handle-multiple-async-operations-in-swift-85b5d82149b7>

## 提示:不要嵌套它们！

![](img/c2c839365ee9c1bf65324576d4da7dea.png)

由[埃克托尔·j·里瓦斯](https://unsplash.com/@hjrc33?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们遇到过这样的情况，我们需要等待一些 API 完成，然后处理进一步的逻辑或者相应地更新 UI。我们将看到我(可能还有许多其他人)过去是怎么做的，然后是使用 Swift 的最佳方法。

# 问题

正如你在下面看到的，我们调用了两个 API，然后我们想更新 UI。我们将使用 Alamofire 来简化 API 请求。我们还记录了两个 API 完成所花费的时间。

注意:如果你自己尝试这个，那么不要忘记在你的`Info.plist`文件中设置应用传输安全设置➡允许任意负载到`YES`。

## 密码

## 代码解释

*   端点是将我们稍后要调用的 API 的 URL 保存在一起的结构
*   需要一个`IBOutlet`来访问 UI 对象，以便在 API 响应状态改变时更新它们
*   `startTimeInterval`是指我们开始第一次 API 调用的时间
*   `isWaiting`正在跟踪是否有我们正在等待响应的 API 请求。当状态改变时，它更新用户界面。
*   在`viewDidLoad()`中，我们使用 Alamofire 调用两个 API。请记住，我们正在嵌套它们。所以这些 API 请求将一个接一个地执行，而不是同时执行。
*   `updateUI()`实际上就是分别用适当的文本和动画更新标签和`activityIndicatorView`

## 故事板

如果你想知道我们的故事板是什么样子的，它已经非常精简了。它只是一个标签和一个活动指示器。

![](img/9beaa26c7a898f8d1102da5622912473.png)

# 解决方案:“调度组”

现在进入(双关语！)的`[DispatchGroup](https://developer.apple.com/documentation/dispatch/dispatchgroup)`。它是专门为这种情况设计的，将多个任务组合在一起，然后等待它们完成。当所有任务完成时，它可以通过快速关闭来通知，这样我们就可以继续进行。

## 密码

## 代码解释

*   我们已经添加了一个`DispatchGroup`实例
*   我们对组使用`enter()`方法。这是为了告诉`DispatchGroup`我们在等待什么。把`DispatchGroup`想象成它在计算`enter()`被调用了多少次。因此，每次调用`enter()`时，计数器都会加 1。
*   我们已经删除了嵌套的 API 调用，以及我们如何同时调用这两个 API。总的来说，这使得 API 等待时间更短。这里，注意我们使用`leave()`方法告诉`DisaptchGroup`任务已经完成，意味着已经收到 API 响应。通过调用`leave()`方法，我们将把我们的虚计数器减 1。一旦我们的虚计数器达到零，`DispatchGroup`就会调用`notify()`方法。

![](img/ebf16fb2e2606d88a6b9f78d486e9c29.png)

来源:苹果文档

![](img/36dceef1176e1b1651e30fed344be118.png)

来源:苹果文档

**注意:**每个`enter()`呼叫还应伴随一个`leave()`呼叫。否则，根本不会执行`notify()`方法。

您可能只想在 API 调用之前调用`enter()`,如下所示:

但是我建议你避免这种情况，因为在非常非常罕见的情况下，如果 API 响应太快，那么组几乎会立即进入和离开，如果组中没有其他调度的任务，它会调用`notify()`方法，而不会等待你的下一个任务。

因此，我个人认为，如果我们事先知道要开始多少任务，我们应该将`enter()`个电话组合在一起。

# 结论

太好了！今天我们学习了如何使用`DispatchGroup`来等待异步操作。使用`DispatchGroup`有不同的方法，但那是另外一天。

感谢您的阅读。

## 下载代码

[](https://github.com/akshitzaveri/DispatchGroupExamples) [## akshitzaveri/dispatch group 示例

### 通过在 GitHub 上创建一个帐户，为 akshitzaveri/dispatchgroupemples 开发做出贡献。

github.com](https://github.com/akshitzaveri/DispatchGroupExamples) 

# 资源

 [## 调度组

### 声明组允许您聚合一组任务并同步组上的行为。您附加了多个…

developer.apple.com](https://developer.apple.com/documentation/dispatch/dispatchgroup)