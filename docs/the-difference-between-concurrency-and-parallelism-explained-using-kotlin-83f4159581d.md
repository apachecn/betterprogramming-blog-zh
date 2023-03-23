# 使用 Kotlin 的并发与并行

> 原文：<https://betterprogramming.pub/the-difference-between-concurrency-and-parallelism-explained-using-kotlin-83f4159581d>

## 了解差异

![](img/4250540ceeecc1de8c356b9a97d7a6a7.png)

Kotlin 的官方文档将 Kotlin 协程描述为“用于异步编程等”的工具。它们的主要目的是为程序员提供“异步或非阻塞”编程的简单工具。这到底是什么意思？“异步”与术语“并发性”和“并行性”有什么关系，这两个术语我们在这个上下文中经常听到，而且经常混淆。

在本文中，我们将看到协程主要关注并发性，而不是并行性。协程提供了复杂的方法来帮助我们构建代码，以使其高度并发地可执行。此外，它们允许启用并行性，这不一定是默认行为。如果您还没有完全理解这两个概念之间的区别，不要担心，在本文中它会变得更加清晰。许多人，包括我自己，都在努力正确使用这些术语。让我们了解更多关于协程的知识，以及它们与所讨论主题的关系。

*(你可以在* [*这篇老文章*](https://kotlinexpertise.com/kotlin-coroutines-guide/) *)中找到 Kotlin coroutines 的一般介绍*

# 异步——一种编程模型

异步编程是我们在过去几年中经常读到和听到的一个话题。主要指“独立于主程序流程的事件的发生”，也指“处理这些事件的方法”([维基百科](https://en.wikipedia.org/wiki/Asynchrony_(computer_programming)))。异步编程的一个重要方面是异步启动的动作不会立即阻塞程序，而是同时发生*和*。当异步编程时，我们经常发现自己触发了一些子例程，这些子例程立即返回到调用者，让主程序流继续，而不等待子例程的结果。一旦需要结果，您可能会遇到两种情况:1)结果已经被完全处理并且可以被请求，或者 2)您需要阻塞您的程序直到它可用。这就是许多语言中的未来或承诺的运作方式。另一个流行的异步例子在[反应式宣言](https://www.reactivemanifesto.org/)的这一部分中有所描述，它涵盖了反应式流的使用:

> *反应式系统依靠*异步消息传递*在组件之间建立边界，确保松散耦合、隔离和位置透明。[…]非阻塞通信允许接收者仅在活动时消耗资源，从而减少系统开销。*

总之，我们可以将软件工程领域中定义的异步描述为一种编程模型，这种模型通过**实现了**非阻塞和并发编程。我们分派任务，让我们的程序继续做别的事情，直到我们收到结果可用的信号。想想这个小漫画:

![](img/b2ed1ed78062f649842fa025f4be9629.png)

即使机器在帮我们洗衣服，我们也可以继续看书。我们委派我们的工作。

*免责声明*:我从【Quora 贴子中截取了这张照片和下面两张图片，该贴子类似地报道了这个话题。

# 并发性——它是关于结构的

在我们了解了异步指的是什么之后，让我们看看什么是*并发*。并发并不像许多人错误地认为的那样，是关于“并行”或“同时”运行事物。Rob Pike 是谷歌的一名工程师，他因在 Golang 方面的工作而闻名，他将并发描述为“独立执行任务的组合”,并强调并发实际上是关于构建程序的。这意味着并发程序同时处理多个正在进行的任务，但不一定同时执行。所有任务的工作可能以某种任意的顺序交错，类似于杂耍者使用的对象。

并发不是并行。它试图分解任务。不过，这些任务不一定要同时执行。它的首要目标是*结构*，而不是*并行*。

# 并行性——关乎执行

*并行*，经常被误当作*并发*的同义词，是关于多件事情的同时执行。如果并发是关于结构的，那么并行就是关于多个任务的执行。我们可以说并发性使得并行性的使用更加容易，但是它甚至不是一个先决条件，因为我们可以在没有并发性的情况下拥有[并行性](https://en.wikipedia.org/wiki/Parallel_computing)。

最后，正如 Rob Pike 所描述的那样:“并发就是同时处理许多事情。并行就是同时做很多事情”。你可以在 [YouTube](https://youtu.be/oV9rvDllKEg) 上观看他的演讲*并发不是并行*。

# 协同程序在并发性和并行性方面

首先，协程是关于并发性的。它们提供了很好的工具，让我们将任务分解成不同的块，默认情况下这些块不会同时执行。一个简单的例子说明了这一点，它是 Kotlin 协程[文档](https://kotlinlang.org/docs/reference/coroutines/composing-suspending-functions.html)的一部分:

这个例子在大约 1000 毫秒内结束，因为两个“有用的”任务各花费大约 1 秒钟，我们在`async`协程构建器的帮助下异步执行它们。这两个任务都只是使用一个简单的非阻塞`delay`来模拟一些合理的长时间运行的动作。让我们看看框架是否真正同时执行这些任务。因此，我们添加了一些日志语句，告诉我们操作在哪些线程上运行:

```
[main] DEBUG logger - in runBlocking
[main] DEBUG logger - in doSomethingUsefulOne
[main] DEBUG logger - in doSomethingUsefulTwo
```

因为我们在主线程中使用了`runBlocking`，所以它运行在同一个线程上。`async`构建器没有指定单独的[协程作用域](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/)或[协程上下文](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines.experimental/-coroutine-context/index.html)，因此也固有地在`main`上运行。我们有两个任务运行在同一个线程上，它们在延迟 1 秒后完成。这是可能的，因为`delay`仅挂起协程，而不阻塞`main`。正如正确描述的那样，该示例是并发性的示例，没有利用并行性。接下来，让我们把函数改成真正需要时间的东西，看看会发生什么。

# 并行协同程序

我们让函数`doSomethingUseful`基于随机生成的`BigInteger`来计算[下一个可能的素数](https://docs.oracle.com/javase/7/docs/api/java/math/BigInteger.html#nextProbablePrime())，而不是仅仅延迟协程，这碰巧是一个相当昂贵的任务(因为这个计算是基于随机的，所以它不会在确定性时间内运行):

请注意，`suspend`关键字已经没有必要了，实际上可能会产生误导。该函数不使用其他挂起函数，并在需要的时间内阻塞调用线程。运行代码会产生以下日志:

```
22:22:04.716 [main] DEBUG logger - in runBlocking
22:22:04.749 [main] DEBUG logger - in doSomethingUsefulOne
22:22:05.595 [main] DEBUG logger - Prime calculation took 844 ms
22:22:05.602 [main] DEBUG logger - in doSomethingUsefulOne
22:22:08.241 [main] DEBUG logger - Prime calculation took 2638 ms
Completed in 3520 ms
```

正如我们很容易看到的，任务仍然像在`async`协程中一样并发运行，但是不再同时执行。总的运行时间是两个子计算的总和(大概)。在将挂起代码改为阻塞代码后，结果发生了变化，我们在执行时不再赢得任何时间。

# 关于示例的注释

请注意，我发现文档中提供的例子有点误导，因为在将`async`协程构建器应用于之前顺序执行的代码后，它总结为“这是两倍的速度，因为我们有两个协程的并发执行”。它只是“快了两倍”,因为并发执行的协程只是以非阻塞方式延迟。这个例子给人的印象是我们免费获得了“并行性”,尽管据我所知，它只是为了演示异步编程。

现在我们如何让协程并行运行呢？为了修复上面的主要示例，我们需要将这些任务分派到一些工作线程上，以便不再阻塞主线程。我们有几个可能性来完成这项工作。

# 让协程并行运行

## 1.跑进`[GlobalScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-global-scope/)`

我们可以在`GlobalScope`中生成一个协程。这意味着协程不绑定到任何`Job`，只受整个应用程序生命周期的限制。这是我们从产生新线程中了解到的行为。很难跟踪全局协程，而且整个方法看起来很幼稚，容易出错。尽管如此，在这个全局范围内运行会将一个协程分派到`[Dispatchers.Default](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-dispatchers/-default.html)`上，这是一个由 kotlinx .协程库管理的共享线程池。默认情况下，该调度程序使用的最大线程数等于可用 CPU 核心数，但至少是两个。

将这种方法应用到我们的例子中很简单。我们没有在`runBlocking`的范围内运行`async`，而是在`GlobalScope`中生成它们:

```
val time = measureTimeMillis {
    val one = GlobalScope.async { doSomethingUsefulOne() }
    val two = GlobalScope.async { doSomethingUsefulTwo() }
}
```

输出验证了我们现在大致运行在`max(time(calc1), time(calc2))`:

```
22:42:19.375 [main] DEBUG logger - in runBlocking
22:42:19.393 [DefaultDispatcher-worker-1] DEBUG logger - in doSomethingUsefulOne
22:42:19.408 [DefaultDispatcher-worker-4] DEBUG logger - in doSomethingUsefulOne
22:42:22.640 [DefaultDispatcher-worker-1] DEBUG logger - Prime calculation took 3245 ms
22:42:23.330 [DefaultDispatcher-worker-4] DEBUG logger - Prime calculation took 3922 ms
Completed in 3950 ms
```

我们成功地将并行性应用到我们的并发示例中。正如我所说的，这个修复是幼稚的，可以进一步改进。

## 2.指定协程调度程序

而不是在`GlobalScope`中产卵`async`，我们仍然可以让它们在`runBlocking`的范围内运行，也就是说，作为`async`的孩子。为了获得相同的结果，我们现在显式设置一个协程调度程序:

```
val time = measureTimeMillis {
    val one = async(Dispatchers.Default) { doSomethingUsefulOne() }
    val two = async(Dispatchers.Default) { doSomethingUsefulTwo() }
    println("The answer is ${one.await() + two.await()}")
}
```

这种调整导致与之前相同的结果，同时没有失去我们想要的父子结构。尽管如此，我们仍然可以做得更好。再次拥有真正的悬浮函数难道不是最理想的吗？在执行阻塞函数时，最好只调用不阻塞调用者的挂起函数，而不是注意不阻塞主线程。

## 3.暂停阻塞功能

我们可以使用`[withContext](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/with-context.html)`，它“立即从新的上下文应用 dispatcher，将块的执行转移到块内的不同线程中，并在它完成时返回”:

通过这种方法，我们将分派任务的执行限制在挂起函数内的素数计算上。输出很好地展示了只有真正的质数计算发生在不同的线程上，而其他的都在 main 上。多线程何时变得如此简单？我真的最喜欢这个解决方案。

```
23:00:20.591 [main] DEBUG logger - in runBlocking
23:00:20.648 [DefaultDispatcher-worker-1] DEBUG logger - in doSomethingUsefulOne
23:00:20.714 [DefaultDispatcher-worker-2] DEBUG logger - in doSomethingUsefulOne
23:00:21.132 [main] DEBUG logger - Prime calculation took 413 ms
23:00:23.971 [main] DEBUG logger - Prime calculation took 3322 ms
Completed in 3371 ms
```

# 注意:我们可以互换使用并发性和并行性，尽管我们不应该这样做

正如在本文介绍部分已经提到的，我们经常将术语并行性和并发性作为同义词使用。我想向您展示，即使是 Kotlin 文档也没有明确区分这两个术语。“共享可变状态和并发性”一节(截至 2018 年 11 月 5 日，未来可能会更改)介绍了:

> *可以使用*多线程调度程序*像* `*Dispatchers.Default*` *一样同时执行*和*协程。它提出了所有常见的并发问题。主要问题是对共享可变状态访问的同步。在协程领域，这个问题的一些解决方案类似于多线程领域的解决方案，但其他解决方案是独特的。*

这句话应该读作“协同程序可以使用类似于`Dispatchers.Default`的多线程调度程序在*并行*中执行……”

# 结论

了解并发性和并行性之间的区别很重要。我们知道并发主要是指同时处理许多事情，而并行是指同时执行许多事情。协程提供了复杂的工具来支持并发，但并没有免费提供给我们并行性。在某些情况下，有必要将阻塞代码分派到一些工作线程上，以便让主程序流继续运行。请记住，我们最需要并行处理 CPU 密集型和性能关键型任务。在大多数情况下，不要担心并行性，为我们从协程中获得的奇妙的并发性而高兴就好了。