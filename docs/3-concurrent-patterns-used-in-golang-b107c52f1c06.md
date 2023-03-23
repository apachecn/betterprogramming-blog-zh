# Golang 中使用的 3 种并发模式

> 原文：<https://betterprogramming.pub/3-concurrent-patterns-used-in-golang-b107c52f1c06>

## Go 中的并发性

![](img/52e2ab080b519bc394f31a32be829f24.png)

图片来自 [Golang](https://golang.org/) 。

当一个程序或者一个算法是*并发*时，意味着它被设计成分解成更小的部分，这些部分将在独立的进程中并行执行。换句话说，正如[罗布·派克所说](https://blog.golang.org/waza-talk):

> “并发是指同时处理许多事情。并行就是同时做很多事情。”

并发性与程序的设计有很大关系。当设计开始时，重用以前解决问题的模式是一个好主意，这样可以避免重新发明轮子。

在本文中，我将向您展示在 Go 中实现并发计数器的三种不同模式。

# 1.原子的

`[sync/atomic](https://golang.org/pkg/sync/atomic)`由 Go out of the box 提供，允许您在多个 goroutines 中安全地执行整数的原子操作。

在我们的例子中，我们将从不同的 goroutines 同时增加我们的计数器。为此，我们将调用`AddInt32`，传递一个指向计数器的指针和一个增量作为参数。然后，为了安全地访问计数器值，我们可以使用`LoadInt32`:

并发计数器的原子方法

这里的输出是什么？

```
Counter: 100
```

请注意，goroutines 正在后台运行，并且在执行`log.Printf`时仍将运行，因此我们需要使用`[sync.WaitGroup](https://golang.org/pkg/sync/#WaitGroup)`来等待它们。

基本上，我们将我们的算法分成 100 个更小的部分，并以两个一批的方式分派它们，从而异步地将计数器加 1。这种方法基于共享内存——更具体地说，共享对整数的引用——并同步对它的访问。

# 2.互斥（体）…

互斥锁允许你同步访问更复杂的数据结构。在本例中，我们将使用`[sync.RWMutex](https://golang.org/pkg/sync/#RWMutex)`，它提供了两种锁定模式:

*   `Lock/Unlock`:在写入模式下锁定/解锁数据结构。作者和读者都不能访问它。
*   `RLock/RUnlock`:在读取模式下锁定/解锁数据结构。读者可以访问它，但作者不能。利用这一点，您可以在有大量读者和少量作者的情况下获得更好的性能。

考虑到这一点，我们将创建一个`counter`数据结构，它由计数器的实际值和同步其访问的互斥体组成。我们还将分别实现`increment`和`getValue`方法来安全地更新和访问计数器的值:

并发计数器的互斥方法

值得注意的是，我们需要在执行操作后解锁互斥体。否则，即将到来的将无限期等待，你的程序最终会崩溃。

和前面的例子一样，这种方法通过共享内存来工作。但是在这种情况下，结构引用是从多个 goroutines 中访问的。

# 3.频道

在这篇[博客文章](https://blog.golang.org/codelab-share)中，Go 团队描述了一种新的处理并发的优雅方式:

> “不要通过共享内存来交流；而是通过交流来分享记忆。”

这意味着不要在共享内存中与复杂的互斥情况作斗争，而要使用通道来传递 goroutines。

但是……为什么呢？

当向通道发送消息时，只有一个 goroutine 将接收它，因此从那里访问数据是安全的，并且不需要显式同步，因为它是由 Go 在幕后处理的。

按照这种方法，我们将有一个 goroutine 来保存计数器的状态，另一方面，其他 go routine 将发送消息，第一个 go routine 将接收这些消息以与状态进行交互。我们有两种类型的消息:

*   `incrementOp`:请求递增计数器的操作。
*   `valueOp`:请求计数器值的操作。

并行计数器的通道方法

请注意，所有操作都有一个`res`通道，其目的如下:

*   接收操作的响应
*   同步 goroutines

如您所见，这种策略不像前面的策略那样基于共享内存，而是依赖于通过通道发送操作。

# 包扎

并发可能很难，因此找到合适的工具来解决您的问题很重要:

*   `[sync.atomic](https://golang.org/pkg/sync/atomic)`:用整数做运算时有用。
*   `[sync.Mutex](https://golang.org/pkg/sync/#Mutex)`和`[sync.RWMutex](https://golang.org/pkg/sync/#RWMutex)`:用于同步访问更复杂的数据结构。这是经典的方法，允许自定义锁定。
*   通道:当互斥不是一个选项或者操作起来很复杂的时候。

编码快乐！感谢阅读。

# 资源

[](https://github.com/mmontes11/go-examples/tree/master/concurrency/counter) [## mmontes 11/go-示例

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/mmontes11/go-examples/tree/master/concurrency/counter) 

*   [https://blog.golang.org/waza-talk](https://blog.golang.org/waza-talk)
*   https://blog.golang.org/codelab-share
*   【https://golang.org/pkg/sync/atomic 
*   [https://golang.org/pkg/sync](https://golang.org/pkg/sync/)
*   [https://gobyexample.com](https://gobyexample.com/)