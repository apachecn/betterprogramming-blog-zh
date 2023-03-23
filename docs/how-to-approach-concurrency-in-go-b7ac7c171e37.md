# 如何在围棋中实现并发

> 原文：<https://betterprogramming.pub/how-to-approach-concurrency-in-go-b7ac7c171e37>

## 并发永远是最好的选择吗？让我们找出答案

![](img/45f82e9f0c6aa9cd36b40da1c953bfa7.png)

[https://gopherize.me/](https://gopherize.me/)

本文旨在展示根据程序的工作负载类型，何时使用并发更有益。

因此，我不会讨论流行的并发术语，比如 goroutines、等待组、通道和数据竞争等等。我打算撰写一系列文章，详细解释最常用的并发构建块和怪癖的有用示例。

# 如何解决问题？

在我早期使用并发的时候，一个常见的错误是试图从一开始就使用并发来实现问题解决方案。这不仅使寻找最佳解决方案变得更加困难，而且存在使用并发性不适合该问题的风险。

根据我的经验，我建议您从获得一个可工作的顺序解决方案开始，然后迭代，如果必要的话，检查实现并发解决方案是否有意义。

在考虑并发性之前，尝试用顺序解决方案来解决问题。

# 并发性和并行性

我不会详细解释什么是并发性和并行性，因为互联网上有许多解释得很好的文章。尽管如此，我还是要留下围棋创始人之一的几个定义:

> “并发是指同时处理许多事情。并行就是同时做很多事情。”—罗布·派克
> 
> “并发与结构有关，而并行与执行有关。换句话说，并发是一种构建事物的方式，以便您可以(可能)使用并行性来更好地完成工作。”—罗布·派克

# 工作量

了解并发性是否合适的一个有用方法是了解您的程序的工作负载。分析并发性时，主要需要考虑两种类型的工作负载:

## **CPU 密集型工作负载**

它们描述了程序的执行高度依赖 CPU 的情况；它直接关系到中央处理器的速度。

当我们有一个以上的内核可用时，这些是利用并行性的完美工作负载(我们将在基准测试部分看到这并不总是正确的)。如果我们在具有多个 go routine 的单核中运行它，我们不会看到任何性能改进，因为 go 运行时将浪费宝贵的时间来调度 go routine 的进出，也称为上下文切换。

## **I/O 相关的工作负载**

它们描述了程序的执行高度依赖于输入输出系统而不是 CPU 资源的情况。

一旦一个任务块通过了 I/O，Go 调度程序将恢复这些工作负载上的任何其他任务。因此，我们可以利用单个内核中的多个 goroutines 来提高性能。使用并行性不会提高性能。

我们需要小心，不要产生比可用内核更多的 goroutines，以避免性能下降。

# 我们来编码吧！

我将编写一些简单的函数，我们可以用它们来运行基准测试，以检查哪个性能更高，尝试利用并发性和并行性。

下面是使用的 Golang 函数:

下面是使用的基准测试函数:

基准测试的结果可能因运行的机器而异。您应该不止一次地进行基准测试，同时尽可能让您的机器空闲。

在运行基准测试时使用`-count`标志来运行特定的次数；为了举例，我跳过了它。

我在我的笔记本电脑上运行这些基准测试，总共有十个内核，所以使用 Golang `runtime.NumCPU()`的函数的并发版本将总共使用十个 goroutines。

# 基准测试:CPU 工作负载

以下是运行一个线程时，没有并行性的工作负载基准的情况:

```
go test -cpu=1 -run=XXX -bench=. -benchtime=5s
goos: darwin
goarch: arm64
pkg: concurrency-workloads
BenchmarkComputeHighNumberSequentially      1869           3147006 ns/op
BenchmarkComputeHighNumberConcurrently       632           9490364 ns/op
```

运行顺序解决方案比运行并发解决方案快 3 倍。由于 goroutines 上下文切换造成的开销，这是可以预料的。

下面是一个利用并行性并运行十个线程的工作负载基准测试示例:

```
go test -cpu=10 -run=XXX -bench=. -benchtime=5s
goos: darwin
goarch: arm64
pkg: concurrency-workloads
BenchmarkComputeHighNumberSequentially-10           1794           3145169 ns/op
BenchmarkComputeHighNumberConcurrently-10           3162           1699410 ns/op
```

通过在每个线程上分别运行 10 个 go routine，我们可以观察到，由于 10 个 go routine 同时并行工作，因此并发解决方案比顺序解决方案快 2 倍。

尽管有这些结果，并不是所有的 CPU 工作负载都适合并发。接下来，我们将对合并排序算法进行基准测试来演示它:

```
go test -cpu=1 -run=XXX -bench=. -benchtime=5s
goos: darwin
goarch: arm64
pkg: concurrency-workloads
BenchmarkMergeSortSequentially   3908991              1530 ns/op
BenchmarkMergeSortConcurrently    452580             12307 ns/op
```

```
go test -cpu=10 -run=XXX -bench=. -benchtime=5s
goos: darwin
goarch: arm64
pkg: concurrency-workloads
BenchmarkMergeSortSequentially-10        4068475              1496 ns/op
BenchmarkMergeSortConcurrently-10         533010             12462 ns/op
```

顺序解决方案如何可能比并行使用并发性更有效？让我们分析一下合并排序算法在做什么来理解前面的结果。

我们在每次迭代中生成几个 goroutines 来计算列表的第一个和第二个块。由于这是一个递归算法，我们将会在这样一种情况下结束，即我们将会生成 goroutines，但只计算一个元素。这是非常低效的。我们现在的情况是，创建和安排一个 goroutine 比合并同一个 goroutine 中的单个项目更昂贵。

因此，如果我们想要并行化的工作负载很小，以至于创建和调度一个 goroutine 的成本更高，那么并发和并行的优势就丧失了。

当分解工作或组合结果的成本非常高时，并发可能不是一个好的选择。

# 基准:I/O 工作负载

这是一个没有并行性的工作负载，运行一个线程:

```
go test -cpu=1 -run=XXX -bench=. -benchtime=5s
goos: darwin
goarch: arm64
pkg: concurrency-workloads
BenchmarkComputeURLSequentially                2        2814863021 ns/op
BenchmarkComputeURLConcurrently                7         838693786 ns/op
```

运行并发解决方案的速度提高了约 4 倍。这是意料之中的，因为 I/O 工作负载的 go routine 正在移入和移出等待状态，利用新的 go routine 来高效地使用同一个线程。

利用并行性的工作负载示例，运行 10 个线程:

```
go test -cpu=10 -run=XXX -bench=. -benchtime=5s
goos: darwin
goarch: arm64
pkg: concurrency-workloads
BenchmarkComputeURLSequentially-10             2        2548004562 ns/op
BenchmarkComputeURLConcurrently-10             7        1001367625 ns/op
```

通过用 10 个 goroutines 运行工作负载，每个 goroutines 都在一个单独的线程上，性能并没有提高。如前所述，这是因为我们可以在单线程中高效处理 I/O 工作负载的上下文切换，因此使用并行性并不会提高性能。

# 结论

一般来说，我们可以得出结论，CPU 受限的工作负载非常适合利用并行性的并发性。而另一方面，I/O 相关的工作负载并不适合并行。

然而，每个工作负载都需要仔细分析，因为我们观察到，由于其实现的性质，CPU 绑定的合并排序算法不太适合并发和并行。

最后，我想提一下，增加并发性总是会带来额外的复杂性，所以要明智地使用它，并且只有当它增加性能改进时才使用它！

# 资源

*   [阿登实验室并发系列](https://www.ardanlabs.com/blog/2018/12/scheduling-in-go-part3.html)