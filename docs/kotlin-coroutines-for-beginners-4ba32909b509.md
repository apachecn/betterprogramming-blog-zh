# 第 2 部分:面向初学者的 Kotlin 协程

> 原文：<https://betterprogramming.pub/kotlin-coroutines-for-beginners-4ba32909b509>

## # 100 日代码的第 28 天

## 通过实践代码学习

![](img/6a9685b6f2f2192e2cf71e956598cfd3.png)

请务必查看之前关于下面的 [Kotlin](https://kotlinlang.org/) 协程的故事，我们将在这个故事中继续学习。

[](https://medium.com/@devDeeJay/kotlin-coroutines-for-beginners-a54d7fedb206) [## 面向初学者的 Kotlin 协同程序

### 管理后台线程的新方法|第 1 部分

medium.com](https://medium.com/@devDeeJay/kotlin-coroutines-for-beginners-a54d7fedb206) 

在 Kotlin 中，所有的协程都在我们称之为协程的作用域内运行。

*   协程作用域通过它的[作业](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/)控制协程的生命周期。当您取消一个作用域的作业时，它会取消在该作用域中运行的所有协程。这在用户离开当前屏幕的情况下会很有帮助。
*   它还允许您指定默认的调度程序。Dispatcher 控制哪个线程运行协程。

我们来谈谈代码。我们将在我们的`ViewModel`中定义协程，因为它处理所有耗时的操作，如联网或读写数据库。

```
private val viewModelJob = *Job*()
private val uiScope = *CoroutineScope*(Dispatchers.Main + viewModelJob
```

这里，我们首先创建一个`viewModelJob`,这是我们想要完成的一些工作。工作是可取消的。

然后我们定义一个名为`uiScope`的协程上下文。

# 验光仪

协程上下文是配置协程的一组属性。我们可以定义线程策略、异常处理程序等。

这里，我们提到添加了两个作用域，`Dispatcher.Main`是主线程，而`job`是我们刚刚定义的。因此，最终的协程上下文是两者的组合。

由于我们已经在`viewModel`中定义了作业，如果视图模型被破坏，那么取消作业是一个好的做法。

```
override fun onCleared() {
    super.onCleared()
    viewModelJob.cancel()
}
```

由于`viewModelJob`是我们定义的协程作用域`uiScope`的一部分，当`viewModelJob`被取消时，每个由`uiScope`启动的协程也将被取消。

一个好的做法是取消任何不再需要的协程，以避免不必要的工作或内存泄漏。

如果我们不通过作业和`Dispatchers.Main`协程作用域:

*   该范围将隐式创建一个作业，您可以使用`uiScope.coroutineContext.cancel()`取消该作业。
*   作用域将一直运行到应用程序终止，这是我们作为负责任的开发人员不希望看到的。

# ViewModel 中的协同作用域

定义范围的一种方法是通过上面的样板代码。我说的是样板代码，所以你可以预期接下来会发生什么。

你可以使用 [AndroidX](https://developer.android.com/jetpack/androidx) 依赖`lifecycle-viewmodel-ktx` 库，其中有一个`viewModelScope`作为扩展函数。该库将负责为您设置和清除范围。

因此，在所有的视图模型类中，这就少了一件你需要担心的事情。

# 代码示例 1

让我们将简单的线程代码转换成协程。假设我们的代码在 3000 毫秒后更新了一个`LiveData`变量。

将此转换为协程:

所以，有什么区别吗，达南杰？

*   如果用户离开活动，前一段代码中的任务仍然会完成，从而导致内存泄漏。然而，在协程中，一切都将被取消，并由您的`ViewModel`来清理。
*   我们的协程运行在主线程上，而不是其他线程上。
*   由于我们的代码运行在主线程上，`delay()`不会阻塞我们的主线程，调度程序会在 3000 毫秒后恢复它。`delay()`是一个挂起函数，我们不能用`Thread.sleep()`来强制主线程休眠。

# SuspendCouroutine

`SuspendCoroutine`还有延续。Kotlin 为您提供了一个名为`suspendCoroutine`的东西，它可以立即挂起当前的协程，并为您提供一个 continuation 对象，您可以使用它在将来恢复挂起的协程。

该延续对象保存延续/恢复挂起的协同例程所需的上下文。

`suspendCoroutine`的延续提供了两个功能，resume 和`resumeWithException`。调用其中任何一个都将导致挂起的协程立即恢复。

# SuspendCancellableCoroutine

`SuspendCoroutine`当你不需要取消支持的时候是一个很好的选择。如果您需要取消，您可以使用`SuspendCancellableCoroutine`将取消传播到您的库，这些库支持基于回调的 API 的取消。

# 代码示例 2

首先，尝试自己阅读和理解代码。

在这个例子中，我们从互联网上获取一些数据。我们使用`onStateChangeCallback`来管理三种状态之一:`Loading`、`NetworkSuccess`、`NetworkError`。

如果我们得到一个`NetworkSuccess`，那么我们将响应保存到数据库，这是在`BACKGROUND`线程中完成的另一个异步操作。

# **让我们转换成协程**

我们将使用`suspendCoroutines` 把回调转换成协程。我们将调用`suspendCoroutine`来暂停该功能，同时等待回调。

如果我们有一个`SUCCESS`回调，我们将调用 resume。如果我们得到一个`ERROR`回调，我们将调用`resumeWithException`。

我们为名为`await()`的`NetworkResponse`类定义了一个扩展函数。这个函数实际上负责挂起和恢复协程。

所以，现在，你可以简单地从 call 对象中调用`await()`函数，就像我们在上面的回调中所做的那样。但这一次，没有回调。

我们将一个名为`await()`的`suspend`函数添加到一个最初不是为协程构建的类中。(开闭原则的完美应用。)

这种方法可以用来更新基于回调的 API 以支持协程，而无需更改实现。

最后，我们有了更干净的代码。

今天到此为止，协程中还有很多内容需要探索，敬请关注。

感谢阅读，保持敬畏！