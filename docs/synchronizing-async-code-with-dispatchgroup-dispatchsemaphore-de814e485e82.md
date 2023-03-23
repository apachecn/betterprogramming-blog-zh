# 同步异步代码

> 原文：<https://betterprogramming.pub/synchronizing-async-code-with-dispatchgroup-dispatchsemaphore-de814e485e82>

## 使用 DispatchGroup 和 DispatchSemaphore

![](img/974b5b4a38a43f409084aa30e80aa967.png)

由 [oakie](https://unsplash.com/@jetcityninja?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/synchronized?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

假设您需要执行多个网络调用，并且需要等待它们完成来聚合结果。

我认为最简单的解决方案是使用`**DispatchGroup**` (如果执行顺序不重要)或`**DispatchSemaphore**`。

我将通过使用一个将结合两个异步调用的函数，然后使用一个包含异步调用的循环来演示这个问题及其解决方案。

让我们创建一个命令行项目。

创建一个函数，在预定义的延迟后将 Int 转换为 String。

# 第一个没有循环的场景

创建一个将结合两个异步调用的函数:

当我们运行它时，输出将是空的，因为在执行`fetchData`完成处理程序之前`completionHandler`被调用。

第一种解决方案(不坚持执行顺序)是使用`DispatchGroup`:

如果我们现在运行这个函数，输出将是“10”，因为第二个`fetchData`比第一个`fetchData`花费的时间少。这可能是我们想要的(当我们不关心执行的顺序时)，但是我们可能想要在第一个完成之后调用第二个`fetchData`。

此场景的解决方案是使用`DispatchSemaphore`:

现在，当我们调用这个函数时，无论任何一个`fetchData`函数需要多长时间完成，输出都将是“01”。

重要的是要记住，我们不能在主线程上使用信号量，因为它会永远阻塞它。

同样，如果我们不关心顺序，我们可以将 semaphore.wait()放在 completionHandler 之前。这与使用 DispatchGroup 时的工作方式相同。

这将使用 DispatchGroup 返回类似“10”的解决方案。

# **使用循环的第二个场景**

调用循环中的`fetchData`函数，经过随机延迟时间:

如果我们执行这段代码，输出将是:

```
text:
```

这是因为`print(“text:”, text)`是在任何`fetchData`调用返回回调之前被调用的。

让我们添加同步:

现在输出将是随机的，例如:

```
**1 - 6 - 16 - 15 - 13 - 0 - 10 - 14 - 9 - 7 - 17 - 19 - 3 - 8 - 18 - 4 - 11 - 2 - 12 - 5 -**
```

原因是循环内的所有`fetchData`函数将被同时调用，在所有回调完成并发送`group.leave()`后，`group.notify()`将被执行。

最后，添加`DispatchSemaphore`来序列化循环内部的执行:

现在，每个`fetchData`函数将在执行前等待上一个函数完成，结果是:

```
**0 - 1 - 2 - 3 - 4 - 5 - 6 - 7 - 8 - 9 - 10 - 11 - 12 - 13 - 14 - 15 - 16 - 17 - 18 - 19 -**
```

概括一下，我们有两个同步异步函数的工具，如果我们只想等待它们全部完成，我们可以使用`DispatchGroup`，如果我们想让它们按照我们使用`DispatchSemahpore`的顺序完成和执行。

就是这样！