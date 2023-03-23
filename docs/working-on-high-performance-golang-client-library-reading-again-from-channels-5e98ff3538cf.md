# 致力于高性能 Golang 客户端库——再读通道？

> 原文：<https://betterprogramming.pub/working-on-high-performance-golang-client-library-reading-again-from-channels-5e98ff3538cf>

## 自定义环形队列及其问题

![](img/be318f6367897722a66604c6900e9592.png)

约瑟夫·巴里恩托斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

本文是我从构建高性能 golang Redis 客户端库`[rueidis](https://github.com/rueian/rueidis)`中学到的常见技巧系列的第二个主题。

这些提示对日常 golang 编程也很有用:

*   [第 1 部分——管道上的配料](https://ruian.medium.com/writing-high-performance-golang-client-library-part-1-batching-on-pipeline-97988fe3211?source=friends_link&sk=ff4d0b2200f9416025f6cac6f2c0d117)
*   第 2 部分—从频道中再次读取？
*   第 3 部分——通过同步消除不良繁忙环路。Cond
*   第 4 部分—优雅地关闭并发编写器的通道

在第 1 部分中，我们讨论了流水线上的批处理如何提高性能，并在 golang 中仅用`channel`和`bufio.Writer`实现了`writing`功能。

在这篇文章中，我将讨论`reading`函数的实现，该函数将来自读取流的响应映射回原始请求。

# 阅读功能

如前一部分所述，为了利用管道技术，客户端库应该同时保持写入输出流和读取输入流。因此，`reading`功能应该是这样的:

类似于`bufio.Writer`，`bufio.Reader`自动为我们批量 socket read 系统调用。我们只需要从缓冲区中提取我们的响应，这就是`readResponse`函数所做的。之后，我们需要将响应映射回原始请求，或者更准确地说，通知原始调用者。

一个关键点是循环必须由输入流驱动。也就是说，在循环开始时继续从`bufio.Reader`读取，因为这样我们不仅可以轻松地处理来自服务器的带外消息，而且一旦套接字关闭，我们还可以立即得到通知。后者对于流水线活性监控是至关重要的。

在这个循环中，有许多方法可以找到最初的调用者。以下部分是关于我尝试过的两种方法及其优缺点。

# 双通道

因为我们正在流水线化请求/响应模型协议，所以我们应该期望服务器也按照我们发送的相同顺序进行响应。但是，我们不允许再次从通道中读取相同的数据。

因此，保持请求顺序的一个简单方法是，在从`writing`通道检索请求后，将它们推到另一个`waiting`通道。

我们可以在前面的`writing`函数中添加下面两行`waiting <-req`:

通过读取`waiting`通道完成`reading`功能:

下面是我们如何使用这些函数:

一旦我们调用了`pipelining`函数，管道就建立起来了，我们可以并发地调用`makeRequest`函数来发送请求和接收响应。

这种方法简单而有效。然而，它在两个方面还不够好:

1.  将请求从`requests`通道移动到`waiting`通道看起来效率很低，并且需要两倍大小的通道缓冲区。
2.  它需要为每个并发请求分配一个`respCh`通道，这也是很昂贵的。

下面的基准测试结果也揭示了上述两个问题。与我在`[rueidis](https://github.com/rueian/rueidis)`中使用的方法相比，双通道方法只有大约 50%的吞吐量:

# 自定义环形队列

通道不允许我们从通道中再次读取相同的数据。既然他们在内部使用环形队列，为什么我们不能定制环形队列来解决上述问题呢？

在 web 上，有许多文章讨论如何构建无锁的环形队列或环形缓冲区。都值得一读。我还在`[rueidis](https://github.com/rueian/rueidis)`中使用了他们的一些技巧，包括:

1.  用`&`代替`%`循环环。
2.  计数器之间的填充以减少 CPU 错误共享。
3.  依靠 Golang 提供的事前保证。

在这一系列的文章中，我不打算一一介绍。如果你对它们感兴趣，你可以查看我放在附录中的参考链接。

在接下来的文章中，我们将从构建一个无锁的环形队列开始，就像我在前面的`[rueidis](https://github.com/rueian/rueidis)`中所做的那样，我试图追求最佳的性能。但是在下一篇文章中，我们将放弃无锁环队列，我将向您展示为什么它在我们的例子中也不好。

为了支持管道用例，我们需要一个支持多个并发写者和两个并发读者的环形队列。它们是:

1.  用户可以将请求并发写入队列。
2.  一个 goroutine 从队列中读取请求，然后写入套接字。
3.  一个 goroutine 从套接字读取响应，然后从队列中找到相应的请求。

我们可以从定义队列接口开始，我认为它是一个用于管道化请求/响应协议的通用接口:

1.  `EnqueueRequest`由并发写入器使用，并被阻塞，直到返回响应。
2.  `NextRequestToSend`被我们的管道编写器 goroutine 用来向输出流发送下一个请求。
3.  `ReplyToNextRequest`由我们的读取器 goroutine 在管道中使用，用于在收到来自传入流的响应时查找原始调用者。

我们可以这样定义我们的环形队列实现:

1.  `slots`字段是我们存储请求的地方。
2.  `write`字段是由并发写入器递增的原子计数器，以找到它们应该写入哪个槽。
3.  `read1`和`read2`字段独立地专用于我们的写线程和读线程，它们不需要成为原子计数器。

对环槽的访问仍然需要同步。那就是:

1.  写入者不应该覆盖具有未实现的请求的槽。
2.  管道的写线程不应该读取没有被写的槽。
3.  管道的读取器 goroutine 不应读取管道的写入器 goroutine 尚未读取的槽。

此外，为了避免在每个请求上为调用者分配响应通道，我们将它们预先分配到槽上并重用它们。我们需要找到一种方法来保证一旦调用者获得了通道，其他人就不能同时读取该通道，否则，响应将被传递给错误的调用者。

产生的插槽结构定义如下:

我们可以使用插槽的`mark`字段来实现上述要求:

`EnqueueRequest`在每次调用时递增`write`计数器，它忙于等待`mark`字段为零，并保证它是唯一一个在`ch`等待由`CompareAndSwap`操作的并发调用响应的。将请求放入插槽后，它将`mark`更改为下一个值，以允许我们的编写器 goroutine 读取它:

`NextRequestToSend`仅由我们的作家 goroutine 使用。无需原子操作即可访问`read1`计数器。它检查下一个槽的`mark`是否为 2，然后在将`mark`更改为下一个值之前复制 req。如果`mark`不为 2，则返回`ok = false`通知写入器刷新输出缓冲区。

最后，`ReplyToNextRequest`与`NextRequestToSend`相似，但它仅由我们的阅读器 goroutine 使用，并递增`read2`计数器，并将响应传递回调用者:

请注意，`s.ch`通道应该是**非缓冲的**，以确保在我们将`mark`变回零并允许其他`EnqueueRequest`调用者通过`CompareAndSwap`操作获取`mark`之前，调用者已经收到响应。

就是这样。不过，它实际上并不是完全无锁的，因为我们仍然在`s.ch`上使用阻塞通道操作。

如果我们忽略阻塞的`s.ch`通道操作，`EnqueueRequest`上的基准测试结果将会令人吃惊。与双通道相比，完全无锁的环形队列本身的吞吐量提高了近 6 倍:

然而，在我们的案例中，只对无锁部分进行基准测试会产生误导。将环形队列放入`[rueidis](https://github.com/rueian/rueidis)`后，并没有显示出大约 6 倍的性能提升，而只是前面提到的 2 倍。

而且最重要的是，无锁环队列有一个明显的缺点:忙着等待。

# 下一步是什么:去除坏的忙循环

现在，不仅是`EnqueueRequest`有忙等待循环，而且编写器 goroutine 也有同样的问题，因为我们失去了写通道的阻塞行为:

在下一篇文章中，我将分享它们为什么不好，以及我如何在不影响`[rueidis](https://github.com/rueian/rueidis)`性能的情况下移除它们。

# 附录

 [## 这些年来我一直写错了环形缓冲区

### 所以我实现了一个单元素的环形缓冲区。我相信你会同意，这是一个完全合理的数据…

www.snellman.net](https://www.snellman.net/blog/archive/2016-12-13-ring-buffers/) [](https://itnext.io/understanding-the-lmax-disruptor-caaaa2721496) [## 了解 LMAX 干扰器

### 了解 LMAX 中断器库(线程间通信库)的原理

itnext.io](https://itnext.io/understanding-the-lmax-disruptor-caaaa2721496) [](https://go.dev/ref/mem) [## Go 记忆模型

### Go 内存模型指定了在什么条件下可以保证在一个 goroutine 中读取变量…

go.dev](https://go.dev/ref/mem)