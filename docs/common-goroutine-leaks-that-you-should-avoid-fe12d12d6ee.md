# 您应该避免的常见 Goroutine 泄漏

> 原文：<https://betterprogramming.pub/common-goroutine-leaks-that-you-should-avoid-fe12d12d6ee>

## 永远不要在不知道它将如何停止的情况下开始一场战争。

![](img/a91df99881470a12ce4ca3a9653f0163.png)

路易斯·金特罗在[的照片](https://www.pexels.com/photo/photo-of-gray-faucet-2339722/)

用 Go 编写的最大好处之一是能够在轻量级线程中并发运行代码，也就是 Goroutines。

然而，权力越大，责任越大。

虽然很方便，但 Goroutines 很容易引入错误，如果不小心处理，这些错误几乎不可追踪。

**Goroutine 泄露**就是其中之一。它在后台悄悄生长，最终可能在你不知情的情况下削弱你的应用程序。

因此，这篇文章是关于什么是 Goroutine 泄漏以及如何防止泄漏的。

我们开门见山吧！

# 什么是 Goroutine 泄漏？

![](img/d245c8710645d4b9251ec969746e86fd.png)

照片由[壁纸获取](https://wallpaperaccess.com/plumber#google_vignette)

当创建一个新的 Goroutine 时，计算机在堆中分配内存，并在执行完成后释放它们。

Goroutine 泄漏是一种内存泄漏，发生在 Goroutine 没有被终止，并且在应用程序的生命周期内一直挂在后台的时候。

让我们看一个简单的例子。

当处理程序返回时，Goroutine 继续生活在后台，阻塞并等待数据通过通道发送——这是永远不会发生的。

因此，引入了 Goroutine 泄漏。

在本文中，我将带您了解两种很容易导致 Goroutine 泄漏的常见模式

*   被遗忘的发送者
*   被遗弃的接收者

Jacob Walker 在他的文章[中非常好地解释了这些模式，我推荐你去看看。在这篇文章中，我将用更多的例子来重申它们。](https://www.ardanlabs.com/blog/2018/11/goroutine-leaks-the-forgotten-sender.html)

让我们开始吧！

# 1.0 被遗忘的发送者

![](img/8ee52ff3ec08d3daecdf18ebb3d63779.png)

照片由 [Engin Akyurt](https://www.pexels.com/@enginakyurt) 在[像素](https://www.pexels.com/photo/person-sitting-outdoors-3368246/)上拍摄

当发送方由于没有接收方在通道的另一端等待接收数据而被阻塞时，就会出现被遗忘的发送方。

虽然乍一看这似乎微不足道，但在以下两个场景中很容易被忽略。

## 上下文使用不当

在上面的例子中，我们模拟了一个标准的 web 服务处理程序。

我们定义了一个在 10ms 后发出超时的上下文，然后是一个异步发出网络调用的 Goroutine。

`select`语句等待多通道操作。它一直阻塞，直到**它的一个**案例可以运行并执行该案例。

如果在网络调用完成之前超时，那么`case <- ctx.Done()`将运行，处理程序将返回一个错误。

当处理程序返回时，不再有任何接收者等待接收数据。`forgottenSender`将被阻塞，等待有人接收数据，这永远不会发生！

这是一个漏洞。

## 错误检查后接收器的放置

这是另一个典型的场景。

在上面的例子中，我们定义了一个处理程序，并生成了一个新的 Goroutine 来异步进行网络调用。

在等待调用返回的同时，我们继续其他验证逻辑。

正如您所看到的，当`continueToValidateOtherData`返回一个导致处理程序返回的错误时，就会发生泄漏。

没有人在等待接收数据，forgottenSender 将被永远阻止！

## 解决方案:被遗忘的发送者

**使用缓冲通道。**

如果你回想一下，被遗忘的发送者是因为对方没有接收者。阻塞问题的罪魁祸首是无缓冲通道！

无缓冲通道是一种一旦消息发出就需要接收者的通道，否则发送者将被阻塞。它是在没有给通道分配容量的情况下声明的。

通过向通道添加特定的容量，在本例中为`1`，我们将缓解所有提到的问题。

发送方可以在不需要接收方的情况下将数据注入信道。

# 2.0 被放弃的接收者

![](img/ae9964d6f215985735160d996f275e05.png)

由[杰斯温·托马斯](https://www.pexels.com/@jeswin)在[像素](https://www.pexels.com/photo/person-sitting-on-bench-under-tree-1280162/)上拍摄的照片

顾名思义，被抛弃的接收者是完全相反的。

当接收方由于另一端没有发送方发送数据而被阻塞时，就会发生这种情况。

因为没有发送者发送数据，所以线路 3 永远被阻塞。

让我们来看两个经常被忽略的常见场景。

## 发送方未关闭通道

在上面的例子中，处理程序接收一段字符串，创建一个通道，并将数据插入到通道中。

然后，处理程序通过一个 Goroutine 产生一个工人。工作进程被期望处理数据，并且一旦通道中的所有数据被处理完就终止。

然而，即使所有的数据都被消耗和处理，工人也永远不会到达`line 6`！

通道虽然空了，但它没有关闭！工人继续认为将来可能会有**传入数据**。因此，它永远坐着等待。

这是 Goroutine 再次泄漏的地方。

## 错误检查后发送方的位置

这非常类似于我们之前的一个例子。

在上面的例子中，处理程序首先生成一个 Goroutine worker 来处理和使用一些数据。

然后，处理程序从数据库中查询记录，然后将记录注入通道供工作人员使用。

如果数据库出错，处理程序会立即返回。将不再有任何发送方向通道传递数据。

因此，工人被遗弃了。

## 解决方案:被放弃的接收者

在这两种情况下，接收器都处于挂起状态，因为它们“认为”会有来自通道的输入数据。因此，他们永远阻塞和等待。

解决方案是一个简单的一行程序。

```
defer close(ch)
```

当你产生一个新的通道时，推迟通道的关闭总是一个好的做法。

它确保当数据发送完成或函数退出时通道关闭。

接收器可以判断通道是否关闭，并相应地终止。

# 结论

![](img/72dd33166149dae362c883058b43cdc7.png)

照片由[像素](https://www.pexels.com/photo/shallow-focus-photography-of-luigi-plastic-figure-209679/)上的 [Pixabay](https://www.pexels.com/@pixabay) 拍摄

这就是关于 Goroutine 泄漏！

虽然它不像其他 Goroutine 错误那样强大，但这种泄漏仍然会极大地消耗应用程序的内存。

记住，权力越大，责任越大。

保护我们的应用程序免受 bug 的责任在你和我——开发人员身上！

回头见，再见！

# 参考

*   [雅各布·沃克的《戈罗廷泄密——被遗忘的寄信人》](https://www.ardanlabs.com/blog/2018/11/goroutine-leaks-the-forgotten-sender.html)
*   [雅各布·沃克的《戈鲁廷泄密——被遗弃的接收者》](https://www.ardanlabs.com/blog/2018/12/goroutine-leaks-the-abandoned-receivers.html)