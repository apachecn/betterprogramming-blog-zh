# 用 Swift 中的 DispatchSemaphore 限制并发网络请求

> 原文：<https://betterprogramming.pub/limit-concurrent-network-requests-with-dispatchsemaphore-in-swift-f313afd938c6>

## 设置一次执行的最大操作数

![](img/726c38361452364156dd93535370fe2a.png)

凯文·克罗斯比在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

在本文中，我们将学习如何使用`DispatchSemaphore`来限制正在进行的网络请求的数量。

限制资源密集型操作允许我们更有效地管理线程资源。例如，当您的任务是同时获取十幅高质量图像时，一次只允许执行一个或两个图像加载操作是很有用的。

简而言之，这是您将在本教程中掌握的内容:

*   后台执行任务`DispatchQueue`。
*   利用`DispatchSemaphore`并设置并发操作的最大数量。
*   理解使用`sync`和`async`执行的区别。

# 我们开始吧

首先，让我们创建一个`URL`、一个背景`DispatchQueue`和一个值为 2:

信号量的`value`参数表示允许的并发操作的最大数量。换句话说，我们将只允许同时获取两个图像。

现在，让我们添加一个名为`loadRandomPhoto()`的方法并创建一个`async`任务:

在任务体中增加`semaphore.wait()`和`semaphore.signal()`，如下:

`semaphore.wait()`命令*将信号量计数器递减*1。我们在初始化`DispatchSemaphore`时将计数器设置为 2，因此一旦运行`loadRandomPhoto()`功能，`value`将减 1。

`semaphore.signal()`命令增加信号量的计数器，意味着一个操作结束，另一个操作可以取代它的位置。

我们希望在`async`任务结束时运行`semaphore.signal()`命令。`defer`语句很方便地实现了这一点，所以我们不需要担心将命令插入错误的位置。

现在让我们从`url`属性中获取`Data`:

出于调试目的，我们简单地打印从`Data`获得的`UIImage`。

太好了！现在，我们必须启动几个图像加载操作，并观察输出:

正如我们所见，`for`循环触发了`loadRandomPhoto()`函数十次。

现在，如果我们运行 Xcode Playground，我们将看到成批打印的两个图像:

![](img/1471bb6509d68a9078e37a7d863b393d.png)

信号量已经完成，但是使用`sync`执行和`async`执行有什么区别呢？

当使用`sync`时，调用它的线程将被阻塞，直到任务完成，而`async`执行不会等待任务完成。因此，它不会阻塞当前线程。

在我们的例子中，让我们删除`DispatchSemaphore`并将`async`执行改为`sync`执行:

因此，我们看到图像一次只加载一个:

![](img/f1e67f9c56e4d60760a17ebccb3293d3.png)

因此，当使用`value`设置为 1 的`DispatchSemaphore`时，使用`sync`会产生相同的结果。

然而，在我们上面的例子中，尽管我们指定了`DispatchQueue`的`.background`服务质量，但是`sync`调用仍然发生在主线程*上。结果，我们让图像加载操作冻结我们的 UI，直到它们完成。*

因此，在`DispatchQueue.main`上调用`sync`时最好非常小心。如果我们从主线程调用它，应用程序将由于[死锁](https://stackoverflow.com/a/49260434)而终止。另一方面，当从后台线程调用它时，我们应该避免添加耗时的代码块来防止 UI 冻结。

# 包扎

我希望这篇教程对你有所帮助。感谢阅读！