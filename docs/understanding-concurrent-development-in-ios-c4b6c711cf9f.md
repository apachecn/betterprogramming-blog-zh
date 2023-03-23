# 理解 iOS 中的并发开发

> 原文：<https://betterprogramming.pub/understanding-concurrent-development-in-ios-c4b6c711cf9f>

## 用 OperationQueue 处理复杂的流

![](img/e960a35d9bf1b647fe5838f42274b2d2.png)

[JuniperPhoton](https://unsplash.com/@juniperphoton?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

并发和异步是任何应用程序的两大主题。我们每天都在使用异步代码进行网络请求和其他操作，我们需要掌握它。

并发编程是一个更隐蔽的话题，但是它也可以出现在任何应用程序中。如果我们开发一个图像处理应用程序，并希望同时对不同的图像应用不同的滤镜来创建一个拼贴画，该怎么办？有了并发开发，我们不必等待所有的过滤按顺序完成。如果我们同时应用所有的过滤器，我们只能等待最慢的一个。这个话题如此重要，以至于苹果提供了一个[并发编程指南](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/Introduction/Introduction.html)。

今天，我们将探索如何使用最重要的抽象之一来开发异步和并发代码:`OperationQueue`。

# 什么是操作队列？

一个`[OperationQueue](https://developer.apple.com/documentation/foundation/operationqueue)`是一个抽象，它让我们把我们的代码看作一个叫做`[Operation](https://developer.apple.com/documentation/foundation/operation)`的工作单元。我们可以安排它们异步执行，同时运行多个操作。决定我们的操作何时运行的是操作系统。

这个决定是通过考虑两个方面做出的:有多少资源可用，以及我们可以用来配置`OperationQueue`和`Operation`本身的一系列设置。常见设置有:

*   `OperationQueue.maxConcurrentOperationCount`:`OperationQueue`可以同时运行的操作数。默认情况下，它被设置为`defaultMaxConcurrentOperationCount`，该值由 iOS 决定。

*注意:我们可以将* `*maxConcurrentOperationCount*` *设置为* `*1*` *来实现一个串行队列。当我们必须对共享资源进行操作，并且不想手动处理多线程、锁和信号量时，这是一种非常有用的同步机制！*

*   `OperationQueue.qualityOfService`:此值用作我们入队的所有操作的默认服务质量(QoS)值，当这些操作尚未指定 QoS 时。具有高 QoS 的操作在具有较低 QoS 的操作之前执行。
*   `OperationQueue.isSuspended`:是否暂停`OperationQueue`。当处于挂起状态时，我们可以让操作入队，但它们不会被执行。
*   `Operation.isReady`:操作是否准备好执行。
*   `Operation.isCancelled`:是否取消操作。当一个排队的操作成为下一个要执行的操作时，`OperationQueue`检查它是否被取消。如果是，队列将不会执行该操作。
*   `Operation.queuePriority`:该值与服务的 QoS 一起使用，决定哪个操作可以运行。当有多个具有相同 QoS 的操作准备执行时，`OperationQueue`检查队列优先级并选择具有最高值的操作。
*   `Operation.dependencies`:该数组包含一组必须在当前操作运行之前完成的操作。例如，我们可以定义一个依赖于其他*过滤器*操作的*合并*操作。*联合收割机*操作必须等待所有其他操作完成后才能运行。

## 使用操作队列

使用`OperationQueue`很简单。我们使用默认的初始化器创建一个新的队列。我们可以为它的一般行为设置一些属性，并为它命名。该名称有助于区分不同的队列。

默认情况下，`OperationQueue`是*而不是*暂停。一旦插入新操作，如果队列有足够的资源，它将开始执行该操作。一旦创建了队列，我们就可以开始入队操作了。我们可以在两种不同类型的操作之间进行选择:`BlockOperation`或`Operation`类的定制实现。

`BlockOperation`是最简单的类型。我们可以通过使用`addOperation(_ block: [@escaping](http://twitter.com/escaping) () -> Void)`重载来添加一个`BlockOperation`。`BlockOperations`不支持 QoS、优先级或依赖性。它们可以通过使用`OperationQueue`的方法取消，但是一旦开始，我们就无法阻止它们。

当我们不需要太多的控制，并且希望在方便的时候以异步方式运行一些代码时，这些简化的操作非常有用。

另一方面，`Operation`是一个抽象类，我们可以将其用作自定义操作的父类。我们可以定义任意多的子类，并将它们提交给同一个`OperationQueue`。

为了实现这个子类，我们必须在两种执行模式之间进行选择:同步和异步执行。以下是他们的不同之处:

*   同步操作要求我们实现`Operation`类的`main()`函数。如果队列已达到最大并发操作数，则可能必须等到 main 方法返回后才能开始另一个操作。
*   异步操作要求我们实现`start()`方法以及一系列其他变量，如`isAsynchronous`、`isExecuting`和`isFinished`。`start`方法必须启动一个异步操作，比如网络调用。我们的职责是处理`Operation`状态，更新`isExecuting`、`isFinished`和其他与状态相关的属性。

如果我们的用例允许任务取消，我们需要使用标准的`Operation`。正确处理取消是我们作为并发开发人员的职责之一。典型的方法是在`main()`执行的不同点检查取消状态，如果已经取消，则终止操作。如果操作在任何时候被取消，我们应该回滚该操作执行的所有更改。

# 操作队列与调度队列

`OperationQueue`和`[DispatchQueue](https://developer.apple.com/documentation/dispatch/dispatchqueue)`是亲戚。我很确定你已经用过几次`DispatchQueues`。`DispatchQueue.main.async`或`DispatchQueue.global().async`是将执行转移到适当线程的常见代码片段。

这种方法没有什么不好。大多数时候，我们仍然需要访问主队列来更新应用程序的 UI。然而，调度队列有一些限制。[这篇文章](https://tclementdev.com/posts/what_went_wrong_with_the_libdispatch.html)阐明了我们对分派队列没有多少控制权。任务一旦提交给他们，就没了。如果它不终止，或者如果它无限循环，我们无法停止它，我们是在浪费资源。当这样的事情发生时，我们完全失去了对任务的访问。我们不能探索它运行在哪个线程上，检查它的状态，或者取消它。

然而，使用`OperationQueue`，我们总是可以访问`operations`数组，检查排队操作的状态，检查正在运行的操作，并最终停止它们。

根据经验法则:

*   如果需要在主线程中分派一些工作，使用`DispatchQueue.main`。
*   如果你不得不偶尔分派一些任务，并且对它们的管理不感兴趣，你可以使用`DispatchQueue.global()`。
*   如果应用程序非常依赖异步操作，但您对精细管理不感兴趣，请使用带有`BlockOperations`的`OperationQueue`。
*   如果应用程序需要编排不同的复杂活动，包括取消、优先级和其他奇特的功能，那么使用带有一些自定义功能的`OperationQueue`。

# 结论

在今天的文章中，我们探讨了`OperationQueue`的主要概念，以及我们如何利用这种抽象来调度不同线程上的大量工作。

我们已经研究了它们支持的操作类型，以及如何对它们进行定制。最后，我们分析了`OperationQueue`和`DispatchQueue`的区别。

重要提示:`OperationQueue`不会因联合收割机而死亡，也不会因`async/await`而死亡。虽然它们可能看起来像相似的机制，但它们可以用于非常不同的目的。例如，我们已经可以在一个`BlockOperation`中使用 Combine 的一些出版商来实现复杂的结果。

一如既往，为正确的工作使用正确的工具！