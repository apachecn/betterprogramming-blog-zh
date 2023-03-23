# Swift 中 7 种不同形式的操作队列

> 原文：<https://betterprogramming.pub/the-beauty-of-operationqueue-in-swift-8398fc5fb013>

## OperationQueue 中的 DispatchGroup、DispatchWorkItem、DispatchWorkItemFlags、DispatchSemaphore、addDependency 和 addBarrierBlock

![](img/b4a4e1feef2834ba697c8587ca80f4cc.png)

Nathalie Désirée Mottet 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

`OperationQueue`类是 GCD(大中央调度)上的高级抽象。它根据优先级和就绪状态执行排队的`Operation`对象。本文描述了在`OperationQueue`中管理异步任务的不同方法的优点。

# 1.使用 OperationQueue 的 DispatchGroup

为了等待一组排队的任务，我们可以使用`OperationQueue`的`DispathGroup`。根据苹果的文档:

> 分派组是一种阻塞线程直到一个或多个任务完成执行的方式。您可以在所有指定任务完成之前无法取得进展的地方使用此行为。

*   创建该组是为了等待`asyncBlock`任务。
*   队列将每个异步任务添加到组中，以便在任务完成时处理结果。

结果如下:

```
async block #1 startasync block #2 startasync block #2 endasync block #1 endasync block #3 startasync block #3 end
```

# 2.DispatchGroup 的 enter()、leave()和 wait()

为了管理异步任务，我们还可以注册`DispathGroup`的`enter`、`leave`和`wait`事件。

这个想法很简单，对吗？创建一个组，注册一堆`enter`事件，任务完成后注册`leave`。所有工作完成后，小组会自动执行您需要它执行的操作！

结果如下:

```
async block #1 startasync block #2 startasync block #1 endasync block #2 endasync block #3 startasync block #3 end
```

# 3.使用 DispatchWorkItemFlags 障碍

`DispatchWorkItemFlags`是一个工作项目的一组行为。包含`assignCurrentContext`、`barrier`、`detached`、`enforceQoS`、`inheritQos`等。`barrier`表示提交的工作项作为屏障。

> 在屏障之前提交的工作项目执行到完成，此时屏障工作项目执行。一旦障碍工作项目完成，队列返回到在障碍之后提交的计划工作项目。

结果如下:

```
async block #1 startasync block #2 startasync block #1 endasync block #2 endasync block #3 startasync block #3 end
```

# 4.使用带有屏障标志的 DispatchWorkItem

`DispatchWorkItem`是在一个调度队列上执行的，或者与一个具有封装工作的调度组一起执行。

结果如下:

```
async block #1 startasync block #2 startasync block #1 endasync block #2 endasync block #3 startasync block #3 end
```

# 5.使用调度信号量

`DispatchSemaphore`包含 value 参数，用于控制在给定时刻可以执行的线程数量。当调用`DispatchSemaphore`的`signal()`方法时，该值增加 1，当调用`wait()`方法时，该值减少 1。

*   `DispatchSemaphore`的初始值为 2。它允许两个线程同时执行。
*   当调用第 4 行的第一个`semaphor.wait()`时，值为 1。
*   从值≥ 0 开始执行`aysncBlock(id: 1)`。
*   调用第 10 行的第二个`semaphor.wait()`时，值为 0。
*   由于值≥ 0，所以执行`aysncBlock(id: 2)`。
*   调用第 16 行的第二个`semaphor.wait()`时，值为-1。
*   由于值<为 0，因此`aysncBlock(id: 3)`正在等待。
*   `aysncBlock(id: 1)`在两秒后结束，由于第 6 行的`semaphor.signal()`被执行，数值增加 1。
*   由于现在值为 0，所以执行`aysncBlock(id: 2)`。

结果如下:

```
async block #1 startasync block #2 startasync block #1 endasync block #2 endasync block #3 startasync block #3 end
```

# 6.使用操作队列中的`addDependency()`

根据苹果公司的文档:

> 直到它的所有相关操作都执行完毕，接收方才被认为准备好执行。如果接收者已经在执行它的任务，那么添加依赖项没有实际效果。此方法可能会更改接收器的`isReady`和`dependencies`属性。

结果如下:

```
async block #2 startasync block #1 startasync block #2 endasync block #1 endasync block #3 startasync block #3 end
```

# 7.请使用 OperationQueue 的 addBarrierBlock()

使用`addBarrierBlock(`添加任务时，操作队列如下:

*   在关卡之前提交的所有任务都将被执行。
*   当所有提交的任务完成后，将执行关卡。
*   然后回到关卡后提交的任务。

结果是:

```
async block #1 startasync block #2 startasync block #1 endasync block #2 endasync block #3 startasync block #3 endasync block #4 startasync block #4 end
```

# 结论

`OperationQueue`在 iOS 中起着基础的作用。重要的是不仅要了解什么是`OperationQueue`，还要了解它是如何工作的，以及我们如何使用它。

我希望您喜欢这篇文章，以及我们如何轻松管理异步任务的美妙之处。