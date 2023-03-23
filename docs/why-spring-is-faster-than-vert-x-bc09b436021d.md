# 为什么 Spring 比 Vert.x 快？

> 原文：<https://betterprogramming.pub/why-spring-is-faster-than-vert-x-bc09b436021d>

## 通过基准测试分析 JVM 框架之间的性能差异

![](img/a112d0c891b22aa10d21890262de5f2f.png)

拉尔夫·布隆伯格在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

问题“为什么 Spring 比 Vert.x 快？”在不同的版本中，[平均每月被问一次 StackOverflow](https://stackoverflow.com/questions/70487928/why-i-cant-see-the-performance-difference-between-spring-boot-and-vert-x/70494122#70494122) 。毕竟，Spring 仍然是目前最流行的 JVM 框架，所以很多公司都在使用它。但是 Spring Framework 并不以其性能著称。另一方面，Vert.x 被认为是性能最好的 JVM 框架之一。因此，预计 Vert.x 在任何基准测试中都将优于 Spring。但事实并非如此。

在这篇文章中，我想解决这些违反直觉的结果的不同原因，并就如何改进您的基准测试方法提出一些建议。

首先，当我们谈论一个框架或语言“快”的时候，我们指的是什么？就 web 服务而言，我们不谈获得响应的速度，也称为请求*延迟*。我们通常指的是另一个指标，叫做*吞吐量*。延迟是指返回对单个请求的响应所需的时间。吞吐量是指一台服务器在给定的时间范围内可以处理多少请求。通常:在一秒钟内。

接下来，让我们来理解开发者从哪里得到 Vert.x 应该比 Spring 更快的概念。有一个针对由 tech empowed 支持的 web 框架的[非常流行的基准，它试图使用一些场景来测量不同语言、运行时和框架的吞吐量。通常，Vert.x 框架在那些基准测试中表现非常好。](https://www.techempower.com/benchmarks/)

[例如，在第 20 轮](https://www.techempower.com/benchmarks/#section=data-r20&hw=ph&test=db)中，Vert.x 以每秒 572K 的请求数排名第 10，而 Spring 以每秒 102K 的请求数排名第 219。这的确令人印象深刻。

但是，试图重现这些令人印象深刻的结果有时证明是具有挑战性的，因此从标题的问题。

让我们试着去理解标杆管理策略的主要缺陷是什么。

*当谈到 Spring 时，我指的是具体的 Spring Framework，而不是 Spring WebFlux / Project Reactor，它们的操作方式不同。我还假设 Spring 应用程序运行在 Tomcat 容器中。*

# Vert.x 以 I/O 为中心

Vert.x framework 的独创性很早就认识到大多数现实应用程序的瓶颈是等待 I/O。这意味着应用程序写得有多好、JIT 优化有多智能以及 JVM GC 有多先进都无关紧要。大多数情况下，应用程序会等待数据库的响应，或者等待 10 年前用 Python 或 PHP 编写的服务的响应。

Vert.x 解决这个问题的方式是将所有 I/O 工作放入一个队列中。由于将新任务放入队列并不是一个特别繁重的操作，Vert.x 每秒能够处理成千上万个这样的任务。

当然，这是一个非常简单的解释。有多个队列、上下文切换、反应式驱动程序，还有一堆其他有趣的东西，我不会在这里介绍。不过，我希望大家记住的是，Vert.x 针对 I/O 进行了优化。

现在，让我们看看通常如何测试 Vert.x 的性能:

```
app.get("/json").handler(ctx -> {      
    ctx.response().end("Hello, World!"); 
});
```

让我们将上面的例子与来自 Vert.x 基准测试的代码进行比较，vert . x 基准测试的代码仍然表现很好，吞吐量为每秒 400 万个请求，但与其他一些语言和框架相比并不出色:

```
app.get("/json").handler(ctx -> {      
    ctx.response()
        .putHeader(HttpHeaders.SERVER, SERVER)
        .putHeader(HttpHeaders.DATE, date)
        .putHeader(HttpHeaders.CONTENT_TYPE, "application/json")
        .end(Json.encodeToBuffer(new Message("Hello, World!"))); 
    }
);
```

[](https://github.com/TechEmpower/FrameworkBenchmarks/blob/master/frameworks/Java/vertx-web/src/main/java/io/vertx/benchmark/App.java#L222-L228) [## master tech empower/framework benchmarks 上的 FrameworkBenchmarks/App.java

### 此文件包含双向 Unicode 文本，其解释或编译可能与下面显示的不同…

github.com](https://github.com/TechEmpower/FrameworkBenchmarks/blob/master/frameworks/Java/vertx-web/src/main/java/io/vertx/benchmark/App.java#L222-L228) 

你能看出区别吗？在大多数开发人员执行的基准测试中，I/O 几乎为零。是的，有一些，因为获得请求和编写响应仍然是 I/O，但与像与数据库或文件系统交互这样的事情相比并不多。

因此，像 Vert.x 这样的反应式框架为您提供的优势被该测试最小化了。

*如果您想从 Vert.x 这样的反应式框架中看到真正的好处，那么就编写一个执行一些 I/O 工作的基准应用程序，比如写入数据库或从远程服务中读取数据。*

# 低并发运行基准测试

Spring Framework 处理并发的方式是分配一个线程池，专门用于处理传入的请求。这也称为“每个请求的线程”模型。一旦线程用完，Spring 应用程序的吞吐量就开始下降。

```
ab -c 100 http://localhost:8080/
```

这里我们使用一个叫做 [Apache HTTP Benchmark](https://httpd.apache.org/docs/2.4/programs/ab.html) 的工具来用请求轰炸我们的服务。`-c`标志指定同时运行 100 个并发请求。

您在两个服务上运行这个测试，一个是用 Spring 编写的，另一个是用 Vert.x 编写的，看不到任何性能差异。这是为什么呢？

与 Vert.x 不同，Spring Framework 不直接控制它使用的线程数量。相反，线程的数量是由容器控制的，在我们的例子中是 Tomcat。默认情况下，Tomcat 设置的最大线程数是 200。这意味着，在您拥有至少 200 个并发请求之前，您应该看不到 Spring 和 Vert.x 应用程序之间的太大差异。你只是没有足够重视你的申请。

如果您想增加 Spring 应用程序的压力，请将并发请求的数量设置为高于线程池的最大大小。

# 在同一台机器上运行基准测试

让我们回到 Vert.x 是如何工作的。我已经提到过，Vert.x 通过将所有传入请求放入队列来优化其性能。一旦响应到达，它也被放在同一个队列中。忙于处理该队列的线程数量非常有限，称为 EventLoop 线程。请求越多，EventLoop 线程就越忙，消耗的 CPU 就越多。

当您在机器上运行基准测试时，现在会发生什么？例如:

```
ab -c 1000 http://localhost:8080/
```

接下来会发生的事情如下。基准测试工具将尝试创建尽可能多的请求，利用机器的所有 CPU 资源。Vert.x 服务将尝试为所有这些请求提供服务，也尝试利用所有的资源。

*为了在基准测试期间最大化 Vert.x 应用程序的性能，请确保在一台单独的机器上运行它，该机器不与运行基准测试的机器共享 CPU。*

这就把我们带到了下一点。

# Spring 框架性能很好

至少在过去的 5 年里，我一直是 Vert.x 的超级粉丝。但是，让我们在前面提到的基准中看看 Spring 应用程序的吞吐量。

*   明文:28K
*   JSON 序列化:20K
*   单个查询:14K
*   财富:6K
*   多个查询:1，8K
*   数据更新:0,8K

看着这些数字，并考虑到我们通常至少在 3 个实例的集群中运行我们的服务，您应该问自己:我的应用程序需要每秒处理 2K 次更新吗？

如果答案是肯定的，那么您可能需要在多台机器上运行基准测试，以使 Spring 应用程序达到崩溃的边缘。

# 结论

作为软件工程师，我们喜欢将自己喜爱的语言或框架的性能与其他人进行比较。

在这样做的时候，使用客观的衡量标准是很重要的。使用基准来测量服务吞吐量是一个好的开始，但是应该正确地进行。

评估您正在运行的测试是否受限于 CPU 或 I/O，或者是否有其他瓶颈。

此外，要确保在不同于运行应用程序代码的机器上运行基准。否则，你可能不会对结果印象深刻。

最后，我看到一些公司遇到了语言或框架的吞吐量瓶颈，甚至帮助解决了其中的一些问题。但是周围有许多成功的公司可能不需要所有的吞吐量，你可能正在为其中的一家工作。获得一个好的基准是很难的，而且要花很多时间才能做好。好好想想这是不是你应该解决的最关键的问题。