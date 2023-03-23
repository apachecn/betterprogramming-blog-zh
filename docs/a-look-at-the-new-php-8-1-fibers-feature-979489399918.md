# 看看 PHP 8.1 新的纤程特性

> 原文：<https://betterprogramming.pub/a-look-at-the-new-php-8-1-fibers-feature-979489399918>

## 理解它是如何工作的，以及它对 PHP 中并发性的未来意味着什么

![](img/b556258599fb1dd92a3469f95756e4f1.png)

照片由[约根德拉·辛格](https://unsplash.com/@yogendras31?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

PHP 正试图从它的代码库中去除缺乏的特性，而纤程是对这种语言有意义的补充之一。PHP Fibers 将于今年年底在 PHP 8.1 中发布，它将引入一种异步编程(协程)。

[纤程](https://en.wikipedia.org/wiki/Fiber_(computer_science))的概念基本上是指一个轻量级的执行线程(也称为*协程*)。这些看似并行运行，但最终由运行时本身处理，而不是直接推给 CPU。很多主流语言都有自己的实现方式，但原理是一样的:让计算机同时做两件或更多的事情，并等待所有事情完成。

正如人们可能认为的那样，纤程的 PHP 实现并不是真正的异步计算。事实上，引入纤程后，PHP 的核心仍然是同步的。

你可以把 PHP 纤维想象成从一辆汽车换到另一辆汽车。

# 纤维将如何工作？

纤程是单个[最终类](https://www.php.net/manual/language.oop5.final.php)看起来像一辆汽车:它可以立即启动和运行，踩下刹车并等待，然后继续它的行程。

当您使用`callable`创建一个新的纤程实例时，什么都不会发生。直到启动纤程，回调才会像其他普通 PHP 代码一样执行。

```
$fiber = new Fiber(function() : void {
   echo "I'm running a Fiber, yay!";
});$fiber->start(); // I'm running a Fiber, yay!
```

我不是说过纤维是异步的吗？它们是，但只是在你通过在回调中调用`Fiber::suspend()`踩下刹车之前。然后，它将控制权传递给“外部”，但是请记住，这个 Fiber car 仍然活着，并等待恢复。

```
$fiber = new Fiber(function() : void {
   Fiber::suspend();
   echo "I'm running a Fiber, yay!";
});$fiber->start(); // [Nothing happens]
```

既然汽车已经暂停，接下来要做的就是把你的脚从刹车上拿开，为此，我们可以从外面调用`resume()`方法。

这实际上是不真实的异步，但这并不意味着你的应用程序不能同时做两件事。这里真正的事实是纤程函数状态保存在它离开的地方。你象征性地在两辆车之间切换，将每辆车开到一个点。

关于`start()`、`suspend(`和`resume()`的一个巧妙之处是它们接受参数:

*   `start()`方法将把参数传递给`callable`，并将返回`suspend()`方法接收到的任何内容。
*   `suspend()`方法返回`resume()`方法收到的任何值。
*   `resume()`方法返回下一次调用`suspend()`所收到的任何内容。

这使得主线程和纤程之间的通信相对容易:

*   `resume()`用于将数值输入到通过`suspend()`接收的光纤中，以及
*   `suspend()`用于推出`resume()`接收的值。

这使得官方举例方式更容易理解:

如果您执行上面的代码，您将会收到类似这样的内容:

```
Value from fiber suspending: fiber
Value used to resume fiber: test
```

# 我们即将拥有自己完整的网络服务器

面对现实吧，PHP 99%的时间都是和 [nginx](https://www.nginx.com/) / [Apache](https://httpd.apache.org/) 配对的，主要是因为它不是多线程的。PHP 中的服务器是阻塞的，只为某些测试或向客户端显示某些内容而服务。

纤程可能会为 PHP 更有效地使用套接字打开大门，并支持 WebSockets、服务器端事件、池化数据库连接甚至 HTTP/3 之类的东西，而不必求助于编译扩展、用意想不到的特性进行编程、将 PHP 封装到另一个外部运行时或任何其他灾难性的方法。

有些事情可能需要时间来解决，但如果有承诺为其他功能保留一个单一的代码库，而不必花费数天时间来尝试编译和部署，我就同意。

# 你不会直接使用纤维

根据文档，Fibers 只提供了“允许用户代码在 PHP 中实现全栈协同程序或绿色线程所需的最低要求”

换句话说，除非你有非常奇怪的理由直接使用它们，否则你永远不会像在 Javascript 或 Go 上使用协程那样与纤程交互。

一些高级框架(如 [Symfony](https://symfony.com/) 、 [Laravel](https://laravel.com/) 、 [CodeIgniter](https://codeigniter.com/) 和 [CakePHP](https://cakephp.org/) 等等)需要一些时间来理解如何处理纤程，并从开发人员的角度为它们创建一套工具。一些底层框架，像 [amphp](https://amphp.org/) 和 [ReactPHP](https://reactphp.org/) ，已经在其最新开发版本中登上了纤程之船。

![](img/6cc4e59c8ce78d0ed2792b7907876831.png)

[标准](https://xkcd.com/927/) — xkcd

虽然这将使您从更多地考虑纤程而不是您的想法中解放出来，但这意味着每个人都将创造他们自己的并发风格，以及他们所有的优点和警告。

# 一次只能有一根纤维

![](img/b7d7e082eec806a37aad942bea0f5438.png)

由 [Victoire Joncheray](https://unsplash.com/@victoire_jonch?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我将引用来自 [PHP 内部播客#74](https://phpinternals.news/74) 的 Aaron Piotrowski 的话:

> 由于同一时间只能执行一条纤程，因此不会出现两个线程同时访问或写入内存时出现的某些竞争情况

Aaron 还补充说，框架将能够在同一块内存上解决并发和同步问题。

这很好，因为你不需要考虑数据竞争、信号量和互斥体，这些东西地鼠完全理解。但是不管你做什么，你仍然必须同时做两件事情。

# 同时没有频道

由于只有一个纤程同时运行，所以即使你声明了多个，也不存在数据同步的问题。但亚伦说，有可能另一根纤维醒来，重写第一根纤维共享的内容。解决方案之一是使用 Go 的通道样式。

吴镇男·雷森斯问了关于信道的问题，艾伦的回答很简单:除了光纤之外，还必须实现其他东西，但在那之前，如果框架认为信道对于它们所提供的东西是必要的，它们将拥有如何使用信道的最终决定权，就像 amphp 的家伙们一样。

# 与街区里新来的孩子相比

最近几个月，Go 语言越来越受欢迎，尤其是因为它是围绕并发性而构建的。任何事情都可以与`go`关键字同时执行，同步由互斥或[通道](https://tour.golang.org/concurrency/2)完成，这使得它非常容易使用。

```
names := make(chan string)go doFoo(names)
go doBar(names)
```

从这个角度来看，Go 远远领先于 PHP 最初的并发解决方案。如果你需要一个完全多线程的东西，你可能想让你的软件在 Go 中运行，或者如果你想直接使用 CPU 线程，甚至 Rust。

这并不是说 PHP 与任何并发模型都不兼容，但可以肯定的是，它的基础在核心上仍然是同步的，只是牺牲了方便性和更好的可理解性。与 Go 相比，[后者苦于管道过多](https://www.arp242.net/go-easy.html)。

如果你想要一个像 Go 一样的真正的并发模型，那么 PHP 将不得不从头开始重写，但是这将在已经接受多线程的计算世界中打开许多可能性。

希望这将带来更多对特性和独立性的关注，这是我一直非常强调的。