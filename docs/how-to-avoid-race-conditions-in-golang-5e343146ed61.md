# 如何避免 Golang 的比赛条件

> 原文：<https://betterprogramming.pub/how-to-avoid-race-conditions-in-golang-5e343146ed61>

## 编写安全的并发代码

![](img/dcb6911537bfd7a1b4263f938d864617.png)

作者图片

在 Golang 中实现并发程序的一个可能的问题是数据竞争条件。本文将详细讨论这一主题，以及用于识别竞争条件可能发生的位置、防止它们的策略以及 Golang 如何处理不同并发上下文之间共享的数据的技术。

在我们开始之前，如果您不熟悉 Golang 中的并发性，我强烈建议您先阅读以下文章:

*   [Golang、Goroutines 和 Channels 中的并发性解释](https://levelup.gitconnected.com/concurrency-in-golang-goroutines-and-channels-explained-55ddb5e1881)
*   [使用 GoLang 并发处理文件](/file-processing-using-concurrency-with-golang-9e08920fab63)
*   [利用 Select、Goroutines 和 Channels 在 Go 中掌握并发性](/concurrency-with-select-goroutines-and-channels-9786e0c6be3c)

# 什么是竞争条件？

竞争条件是两个不同的并发上下文同时读写相同的共享数据的结果，导致意外的输出。

在 Golang 中，两个并发的 goroutines 同时访问同一个变量会在程序中产生数据竞争。

## 共享数据

当运行多个 goroutines 时，通常需要在它们之间共享数据。如果没有安全的策略来访问和修改数据，这将导致数据竞争的情况。

在编写并发程序时，确定所有将在 goroutines 之间共享的变量是至关重要的。

## 关键部分

临界区是一个代码块，其中一个 goroutine 试图写入一个共享变量。

并发程序中的每个关键部分都必须实现一个策略来安全地访问和修改共享数据。

## 原子操作

当涉及到并发程序中的竞争条件、共享数据和关键部分时，原子性是一个关键概念。

我们已经讨论了什么是竞争条件，不同的 goroutines 如何共享数据，什么是临界区，以及我们需要一种策略来安全地访问和修改临界区内的数据。

当访问和修改共享变量时，并发程序需要遵循的策略是确保临界区中发生的每个操作都是原子的。

原子操作确保在当前进程完成读/写操作之前，没有其他进程/上下文可以读/写共享数据。这意味着共享变量在其被访问/写入的上下文中是原子的。通过在程序的关键部分拥有这种特性，我们可以知道共享数据是并发安全的。因此，竞争条件不会发生。

Golang 为内存访问同步提供了原语，因此操作可以是原子的。

# 同步。互斥包

`sync.Mutex`包提供了一种机制来保护代码块，使其成为并发安全的，这意味着在该代码块中的写操作将是安全的。sync 包提供的原语允许您使用内存访问同步来编写并发代码，以避免数据竞争情况。

这个机制由使用包中的`Lock`和`Unlock`方法组成。

`Lock`方法将确定调用该方法的 goroutine 刚刚获得了锁，在锁被释放之前，其他 go routine 不能使用锁。

`Unlock`方法释放锁，以便其他 goroutines 可以使用它。

当一个 goroutine 正在使用锁，而另一个 goroutine 也试图获取锁时，该 goroutine 将阻塞，直到另一个 go routine 释放锁。

让我们看一个竞争条件的例子，以及我们如何使用锁来解决它:

在上面的场景中，两个 goroutines 被附加到`numbers`的片上。让我们确定共享数据、关键部分和竞争条件。

在这个例子中，很明显`numbers`变量的切片是两个 go routine 共享的数据，但是仍然值得指出来，因为在一个更复杂的程序中，识别几个 go routine 共享什么数据可能是一个挑战。

本例中的关键部分是第 17 行和第 22 行中的两个追加操作，因为我们希望这些操作在它们运行的上下文中是原子的。

如果我们运行该程序，会产生以下结果:

```
// Expected
numbers &[]
numbers &[1 2]// Expected
numbers &[12]
numbers &[12 1 2]// Race condition
numbers &[]
numbers &[1]// Race condition
numbers &[]
numbers &[12 1 2]
```

如您所见，该程序不能保证输出是一致的，这是因为两个 goroutines 同时运行，并且修改了相同的数据。使得无法预测哪个操作将首先发生。从而导致竞态条件。

为了解决这个问题，将数字附加到共享变量的操作需要是原子的，`sync.Mutex`可以帮助我们做到这一点:

运行该程序现在将只产生两个预期的输出:

```
// Expected
numbers &[]
numbers &[1 2]// Expected
numbers &[12]
numbers &[12 1 2]
```

通常的做法是调用`mu.Lock()`，然后在一个延迟函数中调用`mu.UnLock()`方法，这是因为一个复杂的函数有几种可能的终止函数的方法，要么提前返回，要么返回一个错误，使用`defer`函数将确保当函数终止时锁被释放，不管它采用哪条路径。

`sync`包还为只读操作提供了另一种类型的锁。`sync.RWMutex`提供了`mu.RLock()`和`mu.RUnlock()`方法分别获取和释放锁。当您想要锁定一段代码，但该代码块中执行的操作是只读的时，这很有用。

# 结论

Golang 中的内存访问同步原语允许您的程序锁定和解锁操作，以便它们能够以并发安全的方式运行。这确保了在不同的 goroutines 之间共享数据的程序正确运行，并避免了数据竞争的情况。

*感谢您的阅读，敬请关注。*