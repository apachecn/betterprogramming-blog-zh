# 第 1 部分 RxJava 入门

> 原文：<https://betterprogramming.pub/part-1-getting-started-with-rxjava-ed9fa4e3d2ad>

## # 100 日代码的第 34 天

## 学习 RxJava 应该不难

![](img/e1c153582291aa4244782d13f7485148.png)

2019 年，我终于在搭建 AR apps 的时候碰到了一些场景。我从各种渠道获得的大多数支持都使用这种编写代码的模式，因此我必须学习 RxJava。

浏览网上无穷无尽的资源让我很难学习。我讲这个故事是为了让你更容易理解。

RxJava 不是一种不同的编程语言。这只是一种编写代码的方式，使你的程序以函数的方式运行。它不是功能代码，但它以那种方式运行。RxJava 还有许多其他很酷的方式来帮助你编写干净、易读的代码——因为它来自函数式编程的世界。

# 入门指南

## 导入依赖关系

*   将 RxJava 相关的依赖项导入到项目中。会有 [RxAndroid](https://github.com/ReactiveX/RxAndroid) 和 [RxJava](https://github.com/ReactiveX/RxJava) 。您需要将两者都导入到您的项目中。

## RxJava 的重要概念

让我们详细了解其中的每一项，以帮助您轻松应对未来的挑战。

**1。可观察的**

现在你正在阅读这个媒介故事。你知道你会从中获得一些重要的东西——因此我可以说你正在*观察*它。在这个糟糕的例子中，我的故事变成了一个*可观察。在你的程序中，可能会有一些你想观察的物体。这些物体被称为可观测量。*

**2。观察员**

如果你还在读，你们这些可爱的人就是观察者，即使在所有这些坏例子之后，他们仍然在观察我的故事。因此，如果没有人阅读这个故事，我写这个故事就毫无意义。因此，如果没有观察者，拥有可观察的物体就没有任何意义。

**3。调度程序**

我们都知道管理线程和并发对我们所有人来说都是一件痛苦的事情。RxJava 通过在*调度器的帮助下抽象出许多痛苦来帮助我们。*所以你不必使用线程池和执行器，RxJava 会帮你处理的。

**4。订阅**

最终，结果将被称为*订阅。*为什么我们要分开订阅？这样，当我们不再需要来自观察者的结果时，我们可以取消订阅。这是避免内存泄漏的一个非常简洁的方法。

## ***我们在 RxJava 编程中遵循的一个常见模式是:***

*   创造一个可观察的
*   对可观察对象应用算子
*   指定您想要处理的线程
*   指定要将结果发布到哪个线程
*   考虑如何为观察者订阅可观察的

# 说够了——让我们编码吧！

1.  让我们创建一个`Task`数据类。

2.创建一个`DataRepository`类。这个类将为我们提供任务对象列表。

3.创建一个带有无限进度条的 UI。我们将使用它来查看我们的用户界面是否在任何时间点冻结。

4.在`MainActivity`中，让我们获取任务列表，并使用 RxJava 将它们记录到控制台。

这里是`logcat`输出。观察 RxJava 代码的生命周期。

```
Subscribed
Task 1 processed, moving on to the next one
Task 2 processed, moving on to the next one
Task 3 processed, moving on to the next one
Task 4 processed, moving on to the next one
Task 5 processed, moving on to the next one
Task 6 processed, moving on to the next one
Task 7 processed, moving on to the next one
Task 8 processed, moving on to the next one
Task 9 processed, moving on to the next one
Task 10 processed, moving on to the next one
Got the whole data from the data source
```

5.让我们稍微修改一下我们的任务 observable。请注意，我们添加了一个过滤操作符。

我们的 UI 不会在这里冻结。这项工作在后台线程中完成，我们在主线程上观察，`.subscribe()`在主线程上工作并发布更新。

如果你做到了这些，你就太棒了。

感谢阅读。请继续关注 RxJava 上的未来系列。