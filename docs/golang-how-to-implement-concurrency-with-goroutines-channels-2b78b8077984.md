# Golang:如何用 Goroutines 和通道实现并发性

> 原文：<https://betterprogramming.pub/golang-how-to-implement-concurrency-with-goroutines-channels-2b78b8077984>

## Go 中的并发要简单得多

![](img/4c872173fb18f56273d21fbcfab29a8d.png)

正如我们所看到的，goroutines 是类固醇并发。

一年前，我开始在一个团队中工作，为 Mercado Libre 的船运区域开发和维护一个核心服务，这个服务是用 go 构建的。我第一次使用围棋的经历(在得到这个机会之前)并不愉快。在 Spring Boot，一切都是基于注释的，手动定义处理程序似乎是昨日黄花。我错了。

围棋是一门奇妙的语言。它被编译成二进制(如果我可以补充的话，用最少的编译时间)，令人难以置信的高性能(对于一种垃圾收集语言来说，相当接近 C ),然而，与大多数流行的语言(25 个关键字)相比，它真的很简单。它的语法类似于 C 语言，有一些令人困惑的元素，但是一旦你习惯了就会非常喜欢(多返回值、裸返回、隐式数据类型、错误检查等)。

但是以我的拙见(也是本文的主题)，Go 最好的特性是 Goroutines 和 Channels。

**Goroutines** 和 **Channels** 是一个轻量级的内置特性，用于管理同时执行的几个函数之间的并发和通信。这样，可以编写在主程序之外执行的代码，这样就不会中断主程序并返回一个或多个值(如果只是一个独立的操作，则不返回任何值)。Go 对此有两个关键词:`go`和`chan`。

实现非常简单。首先，定义一个想要并发执行的函数。

如你所见，这只是一个普通的函数，没什么特别的。现在我们用关键字`go`将它作为一个 goroutine 执行:

这为我们提供了以下控制台输出:

```
We are executing a goroutine
Done!
9
Process finished with the exit code 0
```

完成后，我们现在有一个并发执行的代码。如你所见，主程序创建了一个新的 goroutine 来执行`timesThree`功能，并继续下一条指令。因此`fmt.Println("Done!")`在 goroutine 之前执行。

但是如果我们需要从那个函数返回一些值来继续我们的主流程，会发生什么呢？这就是渠道拯救世界的地方。

# 频道

正如它们的名字告诉我们的那样，就像我们的数据在 goroutines 之间的双行道。我们必须用函数`make`、关键字`chan`和括号之间的数据类型来初始化它。

```
ch := make(chan dataType)
```

假设我们需要操作的结果。然后我们需要将通道作为参数传递给 goroutine 函数，以便它返回带有字符 **< -** 的结果来赋值。

这是我们得到的控制台输出:

```
We are executing a goroutine
9
The result is: 9
Process finished with the exit code 0
```

一旦主程序执行了 goroutine，它在继续之前会等待通道获取一些数据，因此在 go routine 返回结果后会执行`fmt.Println("The result is: %v", result)`。这并不意味着主程序将等待完整的 goroutine 执行，直到数据被提供给通道。

现在，如果我们需要 goroutine 返回多个值呢？这就是为什么我们有缓冲通道。

# 缓冲通道

让我们让我们的`timesThree`函数接收一个数字数组，并对它进行迭代，将每个元素乘以 3

让我们运行它，看看我们得到了什么:

```
We are executing a goroutine
The result is: 6
Process finished with the exit code 0
```

为什么主程序不打印所有 3 个值？因为通道只能容纳一个值。我们可以通过向`make`函数传递第二个参数来分配通道容量，从而实现一个缓冲通道，该参数包含它在被读取之前可以获得的元素数量。

这样我们就可以得到`timesThree`函数返回的所有值。

```
We are executing a goroutine
Result: 6 
Result: 9 
Result: 12Process finished with the exit code 0
```

# 作为 goroutines 的匿名函数

另一个很棒的特性是，如果我们不重用匿名函数，它可以作为一个 goroutine 来执行。注意，我们在关键字`go`后声明函数，并在最后一个花括号后的圆括号中传递参数。

# goroutines 之间的通道

通道不仅用于 goroutine 和主程序之间的交互，它们还提供了不同 goroutine 之间的通信方式。例如，让我们创建一个函数，它将从`timesThree`返回的每个结果中减去 3，但前提是它是偶数。

控制台输出:

```
We are executing a goroutine
The functions continues after returning the result
The functions continues after returning the result
Result: 3 
Result: 9 
Result: 9Process finished with the exit code 0
```

即使在这种情况下，没有必要让`minusThree`表现得像一个 goroutine，并通过通道返回结果，它也说明了 go routine 之间的交互是如何工作的。当您在一个需要执行的解决方案中有两个不同的函数，并且其中一个函数的某些条件会影响另一个函数的结果时，这一点特别有用。

# 范围和关闭

这些特性使我们能够从 goroutine 接收连续的元素，直到它关闭通道。有了这条指令，`for i := range ch`我们就可以在 goroutine 的结果发送后立即对其进行迭代。一旦发送完数据，goroutine 应使用函数`close`关闭通道。

如果 goroutine 在发送完数据后没有关闭通道，程序将崩溃，并出现以下错误:

```
fatal error: all goroutines are asleep - deadlock!
```

发生这种情况是因为主程序试图从通道接收一个值，但是没有一个活动的 goroutine 能够发送它。

我之前没有提到过`close`函数，因为正如《Go 编程语言》一书中所说:

> 当你看完一个频道时，你不必把它都关掉。**只有在重要的时候才需要关闭一个通道，告诉接收程序所有数据已经发送。被垃圾收集器确定为不可达的通道将回收其资源，不管它是否关闭。**

# 挑选

如何才能同时从多个通道读取？作为一种同时等待多个通道的方式，防止一个通道阻塞另一个通道。

如果我们查看控制台输出，我们可以看到主程序同时从两个 goroutines 接收数据。

```
We are executing a goroutine
Result minusThree: -1 
Result timesThree: 6 
Result minusThree: 0 
Result timesThree: 9 
Result timesThree: 12 
Result timesThree: 15 
Result minusThree: 1 
Result timesThree: 18 
Result minusThree: 2 
Result minusThree: 3Process finished with the exit code 0
```

如果我们想在每次迭代中执行其他的东西，没有来自任何通道的数据，我们可以添加一个 **default** case(就像在 switch 语句中一样)。

# **互斥**

使用并发时可能出现的一个问题是当两个`gshare`相同的资源不应该被多个 goroutines 同时访问时。

在并发中，修改共享资源的代码块被称为临界区*。*让我们举例说明它在控制台上打印的内容。

```
We are executing a goroutine
27
81
3
9
243
6561
729
19683
59049
2187Process finished with the exit code 0
```

因为 goroutines 同时访问和重新分配相同的内存空间，所以我们会得到有问题的结果。在这种情况下，`n *= 3`将成为*关键部分*。

我们可以通过使用`sync.Mutex`实现**互斥**来锁定访问变量的代码块，从而轻松解决这个问题。这可以防止多个 goroutine 同时访问`Lock()`和`Unlock()`函数之间的指令。

现在我们得到 3 的幂作为输出:

```
We are executing a goroutine
3
9
27
81
243
729
2187
6561
19683
59049Process finished with the exit code 0
```

# **结论**

Goroutines 是一个令人惊叹的轻量级特性，它使得并发性非常容易实现，这也是 Go 的采用在最近几年没有停止增长的原因之一(也是为什么最近我如此喜欢它)。

非常感谢你的阅读！

请务必关注我，以免错过我接下来的报道！