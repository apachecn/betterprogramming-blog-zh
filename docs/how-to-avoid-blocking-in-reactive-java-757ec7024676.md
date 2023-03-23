# 如何在反应式 Java 中避免阻塞

> 原文：<https://betterprogramming.pub/how-to-avoid-blocking-in-reactive-java-757ec7024676>

## 异步编程的良好实践

![](img/01702a2c596c549698caa5ff69d028b2.png)

图片来自[约翰·霍华德](https://pixabay.com/users/jonbonsilver-236141/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=332857)来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=332857)

反应式编程的关键是做出反应。你不要说“现在做”，你要说“什么时候做”“什么时候”适用于你有工作要做的时候。工作以事件的形式出现:消息总线上的消息或 HTTP 请求。

首先，我应该解释为什么反应式编程很重要。Java 的好处之一是线程相对容易。这使得线程成为处理事件的主要模型。当你得到一个事件时，你分派一个线程来处理它。问题是当你得到很多事件时，你最终会创建很多线程。线程可能很昂贵；每个线程都有堆栈内存，切换线程需要系统调用和上下文切换。

[Node.js](https://nodejs.org/en/) 系统在创建时只有一个线程。(它在 v10.5.0 中引入了工作线程)。然而，它成为一个非常受欢迎的系统，用于构建可以处理成千上万个请求的服务器。它通过使用事件驱动的习语来处理请求。因为它只有一个单线程，所以大多数实现 HTTP 服务器或客户机、数据库客户机或其他 I/O 密集型库的库都必须使用单线程的单事件循环。

但是 Java 使用的是请求线程数，这已经成为伸缩的瓶颈。像 [Scala](https://www.scala-lang.org/) 这样的语言，之所以这样命名是因为它更具可伸缩性，是用广泛的框架创建的，以支持事件驱动或异步 I/O。Java 9 引入了带有`Publisher`和`Subscriber`的`Flow`类。这些被用作两个完全反应式框架 [RxJava](https://github.com/ReactiveX/RxJava) 和 [Project Reactor](https://projectreactor.io/) 的基础。

因为 Java 使用每请求线程模型已经很久了，大多数处理 I/O 的库都会阻塞。他们可以阻塞，因为他们期望拥有正在运行的线程，并且不会阻塞其他请求。但是现在我们可以使用异步模型，它们就成了一个问题。因为 Java 现在有一个混合模型，所以很难判断在一个几乎异步的系统中何时以及如何使用线程。

我们举一个常见的例子。您有一个接受 HTTP GET 并查询另一个服务的服务。在这种情况下，为了简单起见，上游服务只是 http://www.google.com。下面是 Spring Boot 的[实现:](https://spring.io/projects/spring-boot)

运行这个，curl 得到页面，查看日志:

请注意，返回线程与调用线程相同。线程在这段时间内被阻塞，必须等待，直到请求完成。

那么我们如何疏通呢？在这个特殊的例子中有两种方法:正确的方法和错误的方法。我们先来看错误的方式。这包括创建一个新线程来调用阻塞调用。起初这似乎弄巧成拙，但是它释放了请求线程(在上面的例子中是`reactor-http-nio-3`)来处理更多的请求，这些请求可能不会阻塞或者阻塞的时间更短。

为了实现这一点，我们使用`Mono.fromCallable`而不是`Mono.just`。然后我们使用`subscribeOn`来改变线程:

```
return Mono.*fromCallable*(() ->
    getHttpBlocking("https://www.google.com"))
        .subscribeOn(Schedulers.*boundedElastic*())
```

以下是使用此方法时的日志:

如您所见，输入请求仍然由`reactor-http-nio-x`线程处理，但是阻塞调用和原始请求的后续返回由一个名为`boundedElastic-x`的工作池线程处理。`boundedElastic`调度程序将创建一个线程池，然后将使用它们的任务排队。有关更多信息，请参见:

 [## 调度程序(反应堆堆芯 3.4.6)

### 以 new 为前缀的工厂(例如 newBoundedElastic(int，int，String)返回其风格的新实例，而…

projectreactor.io](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html#boundedElastic--) 

我们什么时候会用这种“错误”的方式？当我们别无选择时。如果库提供了异步选项，我们应该使用它。幸运的是，Spring Boot 为我们提供了`WebClient`类型:

现在我们可以在控制器类中直接返回它，因为返回类型`Mono<String>`是相同的。运行并执行一个查询，您应该在日志中看到:

现在您可以看到请求本身的开始是由线程`reactor-http-nio-3`处理的。但是来自上游请求的返回和原始请求的后续返回是由线程`reactor-http-nio-5`处理的。在等待处理上游请求的过程中，不会浪费任何线程。

如果你使用的是 Spring Boot 的 WebFlux 版本，你应该放弃使用`RestTemplate`，转而使用`WebClient`。否则，你仍然在阻塞，你将不会从 WebFlux 中获得任何优势。注意，如果你正在使用 WebFlux，但是想使用 RxJava 库:我还没有找到`WebClient`的 RxJava 版本，所以你必须转换。Project Reactor 有一个专用的适配器:

其他库可能不提供异步方法，所以您将坚持使用“错误的”方法来处理阻塞方法，将其作为分配给工作池的任务。如果您正在处理一个新项目，这可能会影响您在数据库和消息队列等方面的决策，如果它们提供异步库的话。

我希望这篇文章能帮助您让您的服务平稳高效地运行。您可以在这里找到本文中使用的所有代码:

[](https://github.com/rkamradt/blockingnono/tree/v0.1) [## rkamradt/blockingnono

### 与我关于反应式 Java 的文章配套的代码。通过在…上创建帐户，为 rkamradt/blockingnono 开发做出贡献

github.com](https://github.com/rkamradt/blockingnono/tree/v0.1)