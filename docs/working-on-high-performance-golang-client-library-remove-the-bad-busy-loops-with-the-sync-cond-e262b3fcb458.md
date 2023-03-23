# 使用高性能 Golang 客户端库——使用 Sync 消除糟糕的繁忙循环。Cond

> 原文：<https://betterprogramming.pub/working-on-high-performance-golang-client-library-remove-the-bad-busy-loops-with-the-sync-cond-e262b3fcb458>

## 使用同步。同步和不同步条件。互斥（体）…

![](img/1989d3ff34774026ea37c68530520b52.png)

保罗·马尔桑在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄的照片

这是我从构建高性能 Golang Redis 客户端库`[rueidis](https://github.com/rueian/rueidis)`中学到的常见技巧系列的第三个主题。

我认为这些技巧值得分享，因为它们对日常 Golang 编程也很有用:

*   [第 1 部分——管道上的配料](https://ruian.medium.com/writing-high-performance-golang-client-library-part-1-batching-on-pipeline-97988fe3211?source=friends_link&sk=ff4d0b2200f9416025f6cac6f2c0d117)
*   [第 2 部分—再次从频道中读取？](/working-on-high-performance-golang-client-library-reading-again-from-channels-5e98ff3538cf?source=friends_link&sk=a336644446cf70749e1a0f1aa0d4c706)
*   第 3 部分—通过同步消除不良繁忙循环。Cond
*   第 4 部分—优雅地关闭并发作者的通道

在前面的第 2 部分中，与双通道方法相比，我们用自定义的环形队列实现了更高的请求/响应通信吞吐量。

但是，有两个地方的定制振铃队列使用忙等待:

1.  `EnqueueRequest`使用一个忙循环来等待时隙可用。
2.  正在编写的 goroutine 在一个繁忙的循环中调用`NextRequestToSend`,因为它没有阻塞行为，而 Golang 通道有。

在以下几节中，我将介绍:

1.  这些忙循环有什么问题？
2.  用`sync.Mutex`通过`sync.Cond`去除不良忙回路
3.  在没有`sync.Mutex`的情况下，通过`sync.Cond`最小化坏忙循环

# 这些忙循环有什么问题？

Golang 以使并发编程变得容易而闻名，它的运行时调度器在操作系统的进程上调度 goroutines 方面做得很好。但是 Go 程序的真正并发性仍然受到你所拥有的 CPU 内核的限制。

做一个忙循环基本占用一个 CPU 核。此外，如果循环需要不确定的时间来完成，这意味着内核很难在不确定的时间内做其他有用的工作，并导致糟糕的整体性能。

我们的自定义环形队列就是这种情况:

`EnqueueRequest`将一直循环，直到环槽可用，但是只有当我们已经处理了它之前的响应时，环槽才可用。也就是我们已经发出了之前的请求，收到了服务器的响应，最重要的是，需要多长时间是未知的。

类似地，我们编写的 goroutine 只是不断循环并调用`NextRequestsToSend`，但是用户何时发出请求也是未知的:

写程序会一直占用你的一个 CPU 核心。而`EnqueueRequest`，在最坏的情况下，会占领所有的。

我们可以通过用更高的并行度设置对定制环队列进行基准测试来确认性能下降。

基准源代码:[https://gist . github . com/rue Ian/FFA 36 c 008 be 14717732377 a 35 a 3956d 0](https://gist.github.com/rueian/ffa36c008be14717732377a35a3956d0)

从结果中可以看出，当并行度上升时，自定义环形队列的性能比通道方法差得多。这是因为 goroutines 之间获取操作系统进程的竞争也变得更加困难。

# 用`sync.Mutex`通过`sync.Cond`去除不良忙回路

对于我们的`EnqueueRequest`，我们需要能够在插槽不可用时让 goroutine 进入睡眠状态，并在插槽可用时唤醒它。

这种能力就像其他编程语言中的 ***信号量*** 所提供的一样。

在 Golang 中，有两种使用类似“信号量”的同步技术的推荐方法:

1.  golang.org/x/sync/semaphore
2.  `sync.Cond`

前者提供了一个复杂的加权信号量机制，用一个`sync.Mutex`和一个通道链表实现，允许用户一次`Acquire`和`Release`多个信号。关于这一点，我在附录中做了更多的介绍。

后者用`Wait`和`Signal`方法提供了一个简单得多的接口，但是要求用户准备一个`sync.Locker`来避免在这种情况下竞争。

因为我们的等待条件是基于信号量外部的插槽状态，所以使用`EnqueueRequest`中的`sync.Cond`更合适。

我们可以将`sync.Cond`添加到我们的插槽中，并用`sync.Mutex`初始化它:

并用它重写我们的自定义环形队列:

现在，如果时隙不可用，我们会让`EnqueueRequest`进入睡眠状态，并在前一个时隙响应发出时，用`cond.Signal()`唤醒一个 goroutine。

现在，基准测试结果优于渠道方法:

基准源代码:[https://gist . github . com/rue Ian/8c 18 e 905 aa 6b 543 D2 b 2 DAC 8 f 975 F2 bef](https://gist.github.com/rueian/8c18e905aa6b543d2b2dac8f975f2bef)

然后，我们将处理我们的写作程序中的繁忙循环。

# 在没有`sync.Mutex`的情况下，通过`sync.Cond`最小化坏忙循环

在我们的新`EnqueueRequest`中，只有当环总是被回收时，才会有锁争用。

但是如果我们以同样的方式在我们的 writing goroutine 上使用`sync.Cond`，这意味着每个`EnqueueRequest`调用都需要访问我们的 writing goroutine 的`sync.Cond`，以检查是否有必要唤醒它。这无疑会有很多锁争用。

幸运的是，这种情况下我们不需要真正的`sync.Locker`。我们可以稍微放松一下我们写程序的睡眠状态，并且仍然使它不占用一个 CPU 内核。

只有当不再有飞来飞去的`EnqueueRequest`呼叫时，我们才让我们的写作程序进入睡眠状态，并且只在下一个`EnqueueRequest`唤醒它。

为此，我们使用两个原子计数器:`waits`和`sleep`。

我们使用`sleep`计数器来标记写入程序何时进入休眠状态以及何时被唤醒。

我们在进入`EnqueueRequest`前增加`waits`计数器，离开后减少计数器。如果在我们增加后`waits`计数器为 1，我们将尝试用`cond.Broadcast()`唤醒写入程序。

重要的是，我们先访问`waits`计数器，然后在`makeRequest`函数中访问`sleep`计数器，而另一方面，我们先访问`sleep`计数器，然后在`writing`函数中访问`waits`计数器。

这些颠倒的访问顺序可以保证我们不会错过唤醒 goroutine 的机会。

我们仍然使用一个繁忙的循环来唤醒我们的`makeRequest`函数中正在写的 goroutine，但是这个繁忙的循环比前一个要好，因为我们知道它很快就会完成。

让写程序进入睡眠状态确实会增加一些开销。但是，现在它不会在没有请求发送的时候占用和浪费你的一个 CPU。

**更新**:完全消除繁忙环路实际上是可能的。看看 https://github.com/rueian/rueidis/pull/9/files 的[看看怎么做。](https://github.com/rueian/rueidis/pull/9/files)

# 下一步是什么:关闭拥有并发作者的渠道

线程安全客户端库的最后一部分可能是如何关闭它的问题。最后一个帖子，我会分享`[rueidis](https://github.com/rueian/rueidis)`是如何优雅处理的。

# 附录

 [## 旗语

### Example_workerPool 演示了如何使用信号量来限制处理并行任务的线程数量…

pkg.go.dev](https://pkg.go.dev/golang.org/x/sync/semaphore)