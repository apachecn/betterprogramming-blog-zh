# 用 Java 和 Kotlin 理解结构化并发

> 原文：<https://betterprogramming.pub/understanding-structured-concurrency-with-java-and-kotlin-53db895967f9>

## 探索如何更安全、更轻松地运行多个程序

![](img/1b14230a8ecba8b87759083e2cb4ffad.png)

迪帕克·劳特拉在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

你可能最近听说过[结构化并发](https://en.wikipedia.org/wiki/Structured_concurrency)，并想知道它到底是关于什么的。结构化并发是一种范式，它试图使并发程序更容易阅读和理解，更快编写，最重要的是，使它们更安全。

在我们进入细节之前，为什么需要这个？我们来过一遍背后的原因。

# 背景

为了理解结构化并发的需要，我们必须回顾过去，看看几年前并发程序是如何编写的。

在非结构化并发范例中，我们在代码中的任何地方开始线程，并且不清楚这个线程在哪里开始或结束。

我们可以用 Java 看下面的例子；尽管比 Java 中的其他老例子更容易理解，但它仍然有一个主要问题:它不安全，因为一个线程的失败不会取消任何其他任务，并且结果是不可预测的。

如果我们运行上面的代码，我们会看到 3 号任务是如何立即运行异常的，尽管发生了异常，其他任务都没有停止。应用程序继续运行，几秒钟后其他任务成功完成。这不是我们所期望的。理想情况下，我们希望立即取消其他任务。想象其中一个任务永远运行，我们可以在每次失败时泄漏一个线程。

```
> Task :UnstructuredConcurrency.main() FAILED
I'm task 3 running on ForkJoinPool.commonPool-worker-3
I'm task 2 running on ForkJoinPool.commonPool-worker-2
I'm task 1 running on ForkJoinPool.commonPool-worker-1Deprecated Gradle features were used in this build, making it incompatible with Gradle 8.0.You can use '--warning-mode all' to show the individual deprecation warnings and determine if they come from your own scripts or plugins.See https://docs.gradle.org/7.4/userguide/command_line_interface.html#sec:command_line_warnings
2 actionable tasks: 1 executed, 1 up-to-date
Exception in thread "main" java.util.concurrent.CompletionException: java.lang.RuntimeException: Something went wrong!
```

那么，解决办法是什么？这就是结构化并发发挥作用的地方。让我们看看怎么做。

# 结构化并发

结构化并发的主要目标有两个:能够编写更容易理解的并发代码和避免线程泄漏。这个想法总是保证当我们的代码的控制流被分成多个并发线程时，我们确保在这个流结束时，我们总是合并这些线程并保持对它们如何被执行的控制。借助结构化并发，我们可以确保在我们的应用程序中没有孤立线程脱离控制。

![](img/1f8899cfee005684e0fc27b2ca4ee1ea.png)

照片由 [Alain Pham](https://unsplash.com/@alain_pham?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/structure?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

这意味着并发代码的开始和结束总是显式的。这样，任何开发人员都可以很容易地理解并发代码，而不依赖于他们在并发编程方面的经验或专业知识。简单永远是赢家。

近年来有几种编程语言引入了结构化并发——其中包括 [Golang](https://go.dev/) 、 [Kotlin](https://kotlinlang.org/) 和 [Python](https://www.python.org/) 。我们将特别关注 [Kotlin](https://kotlinlang.org/) 如何实现结构化并发。

# Kotlin 中的结构化并发

Kotlin 通过使用协程作用域来实施结构化并发。协程是 Kotlin 给它的“虚拟线程”起的名字，虚拟线程是需要一个[操作系统](https://en.wikipedia.org/wiki/Operating_system)线程来完成工作的执行单元，但不依赖于给定的操作系统线程。它们可以等待 [IO](https://en.wikipedia.org/wiki/Input/output) 而不阻塞给定的操作系统线程，另一个协程可以在此期间占用相同的操作系统线程。你可以在这里阅读更多关于 Kotlin 协同程序的内容。

![](img/20a0625a2ec972d4e7d64fbeb8a673a4.png)

科特林标志

其思想是协程作用域总是属于一个具有有限生命周期的实体，例如，一个繁重的调度批处理作业使用数千个线程来实现更高的吞吐量。

当该作业结束时，由于它的作用域也应该终止，所有属于它的协程或虚拟线程都不应该继续存在。

在[科特林](https://kotlinlang.org/)有一个[全球镜](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-global-scope/)可用，尽管不推荐使用。为什么？因为全局范围将在应用程序的整个生命周期中持续，在大多数情况下，这不是我们想要的，因为这将导致多线程泄漏。

全局范围唯一有效的用例是当给定实体的生命周期与应用程序或服务相同时。例如，一个不断重复执行的后台作业。假设我们正在实现一个分布式系统，它需要在每个节点中运行一个后台作业，只要应用程序在运行，就向系统中的其余节点发送一个[周期心跳](https://en.wikipedia.org/wiki/Heartbeat_(computing))。

如果我们想在 [Kotlin](https://kotlinlang.org/) 中这样做，我们必须用`GlobalScope.launch`运行一个线程；通过这样做，我们创建了一个(虚拟)线程，它只会在应用程序被告知关闭时停止。

作为一个经验法则，你总是需要为你的线程定义一个范围。运行一个或多个线程并不重要。这样做可以保证，如果您的应用程序在该范围内出现问题，该范围内的所有子线程都会被正常取消，无论发生什么情况，您都不会有线程泄漏！

让我们看一个 kot Lin[中的例子来理解我们刚刚描述的行为。](https://kotlinlang.org/)

在上面的例子中，我们在给定的范围内运行两个后台线程。运行此代码的结果将类似于以下内容:

```
Adding random number to 10
Adding random number to 1
Task completed with result 13232!Process finished with exit code 0
```

`Job2`首先打印其值，因为我们已经指定了更长的延迟来模拟 IO 延迟。

为了证明这段代码在出错时是安全的，当第一个线程等待一秒钟时，我们将在第二个协程中抛出一个异常。在前面的例子中，使用`[CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html)`，当出现异常时，其他线程继续运行，就像什么都没发生一样。在本例的后面，这种情况不会发生。我们将取消该范围内任何正在运行的线程。用于模拟我们刚刚描述的内容的代码如下:

如果您检查第二个作业，我们会在 500 毫秒后引发一个异常。此时，`job1`仍然应该等待额外的 500ms，然后打印一些东西。然而，如果事情实现正确，我们应该会看到一个异常被抛出，运行时应该立即取消所有正在运行的线程。这正是我们运行这段代码时发生的情况:

```
Exception in thread "main" java.lang.RuntimeException: Something went wrong!
	at org.theboreddev.CoroutinesKt$sumNumbersToRandomNumbers$2$job2$1.invokeSuspend(Coroutines.kt:27)
	at kotlin.coroutines.jvm.internal.BaseContinuationImpl.resumeWith(ContinuationImpl.kt:33)
	at kotlinx.coroutines.DispatchedTask.run(DispatchedTask.kt:106)
	at kotlinx.coroutines.scheduling.CoroutineScheduler.runSafely(CoroutineScheduler.kt:570)
	at kotlinx.coroutines.scheduling.CoroutineScheduler$Worker.executeTask(CoroutineScheduler.kt:750)
	at kotlinx.coroutines.scheduling.CoroutineScheduler$Worker.runWorker(CoroutineScheduler.kt:677)
	at kotlinx.coroutines.scheduling.CoroutineScheduler$Worker.run(CoroutineScheduler.kt:664)
```

您可以随意自己运行它，看看它是否像预期的那样工作。

我们已经看到了 [Kotlin](https://kotlinlang.org/) 如何允许我们在相同的范围内对协程进行分组，以及它如何相应地自动处理该范围内的任何线程。但是 Java 呢？我们能用 Java 做类似的事情吗？

# Java 中的结构化并发

简单的回答是没有，或者至少现在还没有。JDK 团队长期以来一直致力于 Java 并发模型的一些改变。这个项目叫做[项目织机](https://wiki.openjdk.org/display/loom/Main)。这项工作中实现的变化将引入 Java 中的[虚拟线程](https://en.wikipedia.org/wiki/Virtual_threads)和结构化并发。关于结构化并发，它已经作为 [JEP 428](https://openjdk.org/jeps/428) 的一部分包含在 Java 19 中，但是它仍然处于“孵化器”阶段。[虚拟线程](https://en.wikipedia.org/wiki/Virtual_threads)作为 [JEP 425](https://openjdk.org/jeps/425) 的一部分，在 Java 19 中作为预览特性被引入。你必须[启用预览](https://theboreddev.com/how-to-enable-previews-in-java/)才能测试它。

当这些变化变得稳定时，Java 并发模型和我们对 Java 中多线程的理解将会变得更好。

Java 中现有的将 Java 线程绑定到 OS 线程的限制将不复存在，能够构建更加健壮、可靠和高性能的系统。这是 Java 生态系统的巨大进步！

下面的代码举例说明了 Java 中结构化并发的新提议。你会发现这是 Java 中一个全新的范例。

```
Response handle() throws ExecutionException, InterruptedException {
    try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
        Future<String>  user  = scope.fork(() -> findUser());
        Future<Integer> order = scope.fork(() -> fetchOrder());scope.join();           // Join both forks
        scope.throwIfFailed();  // ... and propagate errors// Here, both forks have succeeded, so compose their results
        return new Response(user.resultNow(), order.resultNow());
    }
}
```

在本文中，我们不打算深入研究 Java 虚拟线程的细节，但是我保证我们很快会更多地讨论它们。从我们到目前为止所看到的来看，它看起来非常有前途，但是我们将把它留在这里作为一个小的引子，让您开始对 Java 中的未来并发模型感兴趣！

# 结论

我们已经看到一些语言如何实现虚拟线程来构建更具弹性的系统，其中一个 [OS](https://en.wikipedia.org/wiki/Operating_system) 线程可以在数百或数千个虚拟线程之间共享，因为与现有的 Java 线程相比，虚拟线程非常轻量级。甚至 Java 现在也在向这种新范式迁移，这种新范式允许并发代码更容易理解和编写。很快，我们将不再需要编写容易出错的并发代码，因为我们必须考虑代码可能错误执行的许多可能情况！

如果你对 [Golang](https://go.dev/) 如何实现并发感兴趣，可以从这里看“go routines”[开始。](https://go.dev/tour/concurrency/1)

这就是我们今天的全部内容！我们真的希望您喜欢这篇文章，并且更好地理解什么是结构化并发以及我们为什么需要它。

一如既往，如果你喜欢我们的文章，请继续关注。

感谢阅读！