# 第 1 部分:面向初学者的 Kotlin 协程

> 原文：<https://betterprogramming.pub/kotlin-coroutines-for-beginners-a54d7fedb206>

![](img/6a9685b6f2f2192e2cf71e956598cfd3.png)

## # 100 日代码的第 27 天

## 管理后台线程的新方法

科特林协程是给开发者的礼物。它们通过减少对回调的需求来简化代码。它们是通过将异步回调从长时间运行的操作(如写入数据库)转换来实现的。

如果你来自其他编程背景，那么你可以联想到类似的东西是`async-await`的模式。

看看下面的代码。我们使用回调，我们的代码在这里异步工作。回调做工作，但它们并不完美。差远了。

*   当你可能在做一些嵌套的异步调用操作时，事情变得混乱，这很容易导致所谓的*回调地狱*。我看过，真的很丑。
*   如果不编写一些额外的代码，它们不会帮助您处理错误。

Kotlin 协程解决了这些问题。

```
*// Async callbacks*
networkRequest { response ->
   *// Successful network request*
   databaseSave(response) { rows ->
     *// Result saved*
   }
}
```

使用协程，您可以将这种异步代码转换成顺序代码。

```
*// The same code with coroutines*
val response = networkRequest()
*// Successful network request*
databaseSave(response)
*// Result saved*
```

# 为什么要担心主线程？

非常重要的是，我们永远不要阻塞 Android 中的主线程，因为主线程负责处理所有与 UI 相关的操作，如更新屏幕上的内容或处理用户交互。

主线程必须在不到 16ms 的时间内完成所有这些操作，以保持 60FPS 的屏幕刷新率，为用户提供流畅的体验。

如果你阻塞主线程，UI 会冻结，如果你阻塞的时间足够长，应用程序甚至会向用户显示应用程序没有响应的消息。你不会想参与的。

这就是 Kotlin 协程的伟大之处，它们使您的代码*是主安全的。*直接从主线程中随意调用任何长时间运行的操作，比如网络请求，而不必担心阻塞主线程。

# 协程在内部是如何工作的？

先来看看大图。

Kotlin 使用挂起函数，可以在不阻塞主线程的情况下挂起。这和封杀有什么区别？

当某个进程由于某种外部原因而无法重新启动时，它就会被阻塞；例如，I/O 设备不可用或信号量文件被锁定。

一个进程被挂起意味着操作系统已经停止执行它，但这可能只是为了时间片(多任务)。这并不意味着这个过程不能立即恢复。

因此，我们使用`suspend`关键字使一个函数成为挂起函数。这里，举以下例子:

```
suspend fun getLatestData() {
    val latestUpdates = networkRequest()
    display(latestUpdates)
}suspend fun networkRequest() {
    ...
}
```

`getLatestData()`和`networkRequest()`都被定义为暂停功能，因为`getLatestData()`必须等待`networkRequest()`返回响应。因此，你可以注意到一个挂起函数只能被另一个挂起函数调用。

当你的主线程调用`networkRequest()`时，它将挂起主线程的`networkRequest()`，因此主线程将继续执行它的任务。每当`networkRequest`结果准备好了，主线程将继续顺序执行函数，而不是发送传统的回调到主线程。

`suspend`和`resume`正在取代回调的需要。因此，这就是挂起函数不阻塞主线程的工作方式。另外，`suspend`关键字没有指定代码应该在哪个线程上运行。它可以是主线程，也可以是后台线程。

让我们回到他们内部是如何工作的。

回到例子:

```
suspend fun getLatestData() {
    val latestUpdates = networkRequest()
    display(latestUpdates)
}suspend fun networkRequest() {
    ...
}
```

我们的主线程点击第`getLatestData()`行，并将其添加到主线程中，就像任何其他顺序函数一样。然后，它按顺序执行函数内部，直到任何其他挂起函数出现。

此时，我们点击了`networkRequest()`暂停功能。现在，`getLatestData()`作为挂起函数被 Kotlin 挂起，直到其结果准备好，并且`getLatestData()`的当前状态的副本被保存在存储器中以供将来参考。现在在主线程上，Kotlin 点击了`networkRequest()`，它也挂起了。我们的主线程总是保持空闲。

科特林知道如何恢复这些暂停的功能。首先，它将通过恢复先前保存的状态来恢复最新的`networkRequest()`。然后，它将通过恢复其状态来恢复`getLatestData()`,从而在不阻塞主线程的情况下继续顺序执行程序。永远不会。

因此，我们得到了更简单、更易读的代码。谢谢你协程。

# 将回调转换成协程

记住回调使你的代码更难阅读和推理，如果不写额外的代码，它们对处理错误没有帮助。

协程允许您按顺序编写所有代码，这使得代码更容易阅读，并且具有异常等功能来处理错误。

让我们举下面的例子:

```
*// Annotation signifies which thread it should execute on*
@UiThread
fun makeNetworkRequest() {

    slowFetch { result ->
        *// When the result is ready, this callback will get it*
        show(result)
    }
    *// makeNetworkRequest() exits after calling slowFetch without waiting for the result*
}
```

因此，这段代码可以写成:

```
*// Annotation signifies which thread it should execute on*
@UiThread
suspend fun makeNetworkRequest() {
    val result : Response = slowFetch() 
    // makeNetworkRequest will be suspended
    // Until result is ready
}suspend fun slowFetch() : Response {
    ...
}
```

因为我们的代码变得有顺序性，所以链接多个长时间运行的操作非常容易。

类似于`async-await`模式，当我们调用 suspend 函数时，不会直接调用`await()`。Kotlin 使用用于等待结果的`Deferred.await()`。

今天到此为止。在第 27 天，我们将继续这方面的更多内容。敬请关注。

编辑:这是第 2 部分的链接，在这里您可以了解到 CoroutineScope、SuspendCoroutines 以及一些代码示例。

[](https://medium.com/better-programming/kotlin-coroutines-for-beginners-4ba32909b509) [## 第 2 部分:面向初学者的 Kotlin 协程

### 通过实践代码学习

medium.com](https://medium.com/better-programming/kotlin-coroutines-for-beginners-4ba32909b509) 

感谢您的阅读，您一如既往地令人惊叹！