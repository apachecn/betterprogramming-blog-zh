# 多线程 Ruby——同步、竞争条件和死锁

> 原文：<https://betterprogramming.pub/multithreaded-ruby-synchronization-race-conditions-and-deadlocks-f1f1a7cddcea>

## 线程编程很难。神秘的、非确定性的 bug 等问题会困扰你。对此我们能做些什么？

![](img/634449079600bedbf795fa94d415ca59.png)

[叶维克](https://unsplash.com/@yipvick?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/fibers?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

T 即使在 MRI Ruby 上，hreads 也会导致竞争条件和死锁，这会使您的服务崩溃，并且由于它们的不确定性而难以识别。所以，让我们来看看多线程同步和随之而来的弊端。

在本文中，我们将主要讨论并发编程的原子性问题，但首先让我们来看看在谈论并发编程时经常提到的一些弊端。

# 什么是死锁？

![](img/8457ac9df823f1bfb935e55842a99279.png)

如果资源受到同步机制的保护，当两个或多个线程试图访问同一个共享资源时，就会发生死锁。这里有一个例子:

# 为什么首先要保护资源？

[两个字，竞况。如果我们使用线程是因为我们想尽可能地利用我们的 CPU 内核，我们需要让线程以某种方式协同工作。它们可能需要相互通信，或者如上面的例子所示，它们可能需要改变共享对象的状态。然后我们需要确保我们不会丢失数据。](https://en.wikipedia.org/wiki/Race_condition)

有时候，GIL 可以拯救我们，这样我们就不必同步对共享资源的访问。这是 GIL 拯救我们的一个例子:

但这并不总是正确的:

这段代码会根据你的运气产生不同的输出——记住这是不确定的。当我第一次在 IRB 控制台中运行代码时，我只收到了一次“*正在开门*”的消息，但尝试了几次后，我收到消息的频率更高了——一次甚至四次。
这清楚地表明，默认情况下，GIL 不会使您的 Ruby 代码线程安全。

## 其他 Ruby 实现中的同步问题

***JRuby*** 使用 JRuby 我们会更快地陷入这种同步错误。JRuby 没有 GIL，所以共享资源的每一个改变都可以在不同的线程中同时完成，这是我们经常遇到的问题。

***松露红宝石*** 松露红宝石正在试验一些特别的东西。他们想实现一个[自动同步](http://ssw.jku.at/General/Staff/Daloze/thread-safe-objects.pdf)。这里重要的一点是，如果服务使用线程，这将只在*完成*，因此单线程性能保持较高。因此，TruffleRuby 将使我们免于手动同步。

## 死锁只会发生在内存共享的多线程 mmodel 中吗？

最近，围绕以不同方式实现并发性的新语言出现了相当多的讨论。Elixir、Go、Closure、Kotlin 和带有 Akka 框架的 Scala 只是这次出现的几颗宝石。虽然没有线程并发会更容易，但是不同的方法并不能解决所有问题。[这里的](https://medium.com/@elizarov/deadlocks-in-non-hierarchical-csp-e5910d137cc)是一篇博客文章，描述了基于 [CSP](https://en.wikipedia.org/wiki/Communicating_sequential_processes) 的并发方法中的死锁。

# 如何避免死锁

*免责声明*:这里介绍的策略并不完整。我只是提出一些我发现在某些情况下可行的可能性。

## 避免同步的需要

因为死锁很难调试，所以一个解决方案是设计您的服务，使您不必在线程阶段改变状态。在 Ruby 中实现这一点的一种方法是使用[线程局部变量](http://ruby-doc.org/core-2.6.3/Thread.html#class-Thread-label-Thread+variables+and+scope)。在线程局部变量的帮助下，你可以在一个变量中保存一些状态。加入所有线程后，您就可以访问并处理它们了。

下面是一个伪示例:

***这是否意味着线程局部变量将是我们的救星！？***

不，他们不会。首先，有时等待处理直到每个线程都完成是不可行的，甚至是不可能的。你必须意识到线程局部变量是全局可变状态的一种形式。如果您的线程不使用许多对象/方法调用，并且您从不在线程块之外访问该变量，则可以管理与此相关的危险。但是如果你在设计你的应用程序的时候用很多方法来改变线程状态，这将很快变得非常危险。

## 普通 Ruby 中可用的同步策略

[***互斥***](https://en.wikipedia.org/wiki/Mutual_exclusion)互斥——互斥的简称——可以控制对资源的访问。下面是带有互斥锁的门锁的例子:

现在，无论我们多频繁地从各种线程调用我们的类来打开门，它都只会被打开一次。

互斥锁不是免费的。您必须非常小心，确保不会造成死锁或饥饿——如第一个示例所示。

但是使用 ***互斥*** 类，你可以实现一些更复杂的锁机制，比如 [**读者-作者-锁**](https://en.wikipedia.org/wiki/Readers%E2%80%93writer_lock) 和其他同步方法。值得一提的是，这些方法中的许多仍然容易出现死锁、饥饿或其他与并发编程相关的问题。

# 结论

多线程程序很难——有太多的事情要处理！但是，如果我们想从 CPU 中挤出最后一点性能，我们需要了解选项以及与它们相关的风险。

幸运的是，对于我们 Ruby 开发者来说，未来看起来很光明！TruffleRuby 看起来很有前途，对于 CRuby 来说，现在有太多的开发进入并发领域，很难保持最新。对于每个感兴趣的人，我强烈推荐他们看看 GitHub 镜像和那里的拉请求。

[1]虽然现在，在 Ruby 中使用线程进行 CPU 绑定的操作是没有用的，但是使用线程进行 IO 绑定的操作是很有意义的。关于解释，请看[这篇文章](https://medium.com/swlh/scalable-ruby-concurrency-and-parallelism-explained-68b09a7aeb53)。

*不是中等会员？* [*在这里报名*](https://grnt-grdwhl.medium.com/membership) *并支持我的写作过程！*