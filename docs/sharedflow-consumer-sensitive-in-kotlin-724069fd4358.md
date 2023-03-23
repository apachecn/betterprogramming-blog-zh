# 科特林的共享流量消费者敏感度

> 原文：<https://betterprogramming.pub/sharedflow-consumer-sensitive-in-kotlin-724069fd4358>

## 使用 FileObserver 示例

![](img/45ef63b2048468cf1dbdaa7891d97437.png)

照片由[安迪·里格尔](https://unsplash.com/@andirieger?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

有时，我们需要将资源建模为一个`SharedFlow`,以便在良好的 Kotlin 支持下消费变更。

例如，一个使用`[FileObserver](https://developer.android.com/reference/android/os/FileObserver.html)`获取文件中所有更改的流，或者订阅一个 web 提要来获取新闻和更新 UI 的流。在所有这些情况下，我们需要一种方法来打开和关闭这种资源，以避免不必要的电池或网络消耗(移动设备用户讨厌这样)。

现在想象一下，如果有可能知道有多少流量收集器处于活动状态，以及它们何时发生变化。`MutableSharedFlow`的`[subscriptionCount](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-shared-flow/subscription-count.html)`正是我们所需要的！

我创建了一个助手函数来收集`subscriptionCount`，并触发与有无收集相关的回调。

注意`onActive`仅在第一次运行或者`onInactive`在[启动或](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/)完成时运行。在另一种情况下，只有在已经调用了`onActive`的情况下，才会调用`onInactive`。他们的测试确保同一事件不会被连续调用两次。

另外，请注意，在事件启动之前，最后一个事件的作业会被取消。这是一种性能优化，但需要仔细观察。如果你需要执行一些不应该被取消的重要事情，请在一个单独的[(不可取消)协程上下文](https://kotlinlang.org/docs/cancellation-and-timeouts.html#run-non-cancellable-block)中运行。

我们可以使用一个名为`FlowBinder`的接口来概括`listenerSubscribers`方法，以监听这些事件并做一些有趣的事情。

回到上面提到的`FileObserver`。每次没有检测到消费者，就调用方法`onInactive`，需要停止观察文件的变化。在另一种情况下，当检测到第一个消费者时，方法`onActive`被调用，我们需要开始监视文件事件。让我们看看代码:

例如，这可以用在如下的`ViewModel`中，它可以通过这些文件事件执行一些业务逻辑:

# 结论

`FileObserver`只是`FlowBinder`所能做的一个例子。

请随意分享它的其他用例。