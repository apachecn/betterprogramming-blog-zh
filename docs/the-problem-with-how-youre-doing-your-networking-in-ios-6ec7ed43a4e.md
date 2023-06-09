# 使用 Swift async/await 在 iOS 中构建强大的网络层

> 原文：<https://betterprogramming.pub/the-problem-with-how-youre-doing-your-networking-in-ios-6ec7ed43a4e>

## 你在 iOS 中联网的问题和新的解决方法

![](img/5c06959e9e3e0362f087096bbc606d0f.png)

来自[的图标插图哎哟！](https://icons8.com/illustrations/)

很有可能，你正在你的 iOS 应用中做一些联网工作，从一些 REST API 中获取一些项目，向你亲爱的用户展示有趣的东西。典型的基本材料，对吧？

您使用一个`URLSession`实例，启动一个数据任务，解码您得到的 JSON，然后嘣，您就有了您的数据。已经挺好很容易了，还有更好的方法吗？你为什么想让它变得更好？会出什么问题呢？

我喜欢 Swift 的一点是，语言本身对减少开发人员的错误帮助很大。例如，考虑期权。这种语言迫使你去思考得到一个零值的场景，这是无法回避的。你得到一个编译器错误，你的应用程序不会事件构建。

Swift 中“以前的”网络呼叫方式也是如此。怎么会？

# 问题是

想象以下场景:

您希望创建一个函数，该函数命中某个 API，并在闭包内收到值时通知调用者。

很基本的东西，对吧？号码

您能发现上面代码中的问题吗？你可能会，也可能不会。很简单，我们忘记了在我们的`guard`块中调用回调。

我们的函数永远不会在出现问题时调用回调，而我们亲爱的用户将永远等待这个回调被调用，但它永远不会。你心爱的用户很可能会抛弃你。呀。

你可能会说这是一个非常基本的问题，你不会犯这个错误。或者你可以说你为你的项目编写单元测试(如果你这样做了，太棒了！)并且您可以在早期发现这个问题。

我同意，但是有时候你调用了 300 个不同的 API 端点，却忘记了在一个 API 中调用回调，并且你的测试没有在早期发现它；恭喜你！你向你亲爱的用户交付了一个错误的版本。祝苹果公司尽快批准你的新版本！最好希望你破坏了应用程序一小部分的功能，并且没有导致整个应用程序崩溃。

也可能有一些其他问题，比如回调地狱。当基于闭包的回调被误用时，你会有一种被称为回调地狱的东西，它非常丑陋并且难以维护:

突然间，网络代码似乎不再那么单纯了。

有一些方法可以缓解这些问题，例如使用像 [Combine](https://developer.apple.com/documentation/combine) 和 [RxSwift](https://github.com/ReactiveX/RxSwift) 这样的反应式框架，但是到目前为止，还没有帮助解决这些问题的语言支持。

# 输入 Swift 5.5

随着 iOS 15 和 Swift 5.5 在 WWDC 21 中的引入，苹果为 Swift 增加了一些真正体面的并发性。如果你有在 Android 中使用 Kotlin 协程的经验，它们非常相似。

使用 Swift 的新并发特性，您将不再有上述任何一个问题，这太好了！我们的目标应该是拥有最小化软件失败并提高可读性的过程，并且拥有防止失败发生的语言特性总是令人赞赏的。除此之外，你的代码将会更紧凑，你将会有更少的嵌套，你的代码将会更简洁。赢得胜利的 Swift 5.5！

让我们看看在 Swift 中引入新功能后代码会是什么样子(代码可以改进很多，但我们使用这个版本是出于教育目的):

让我们一个接一个地看一下变化:

## **变更#1:追加** `**async**` **关键字**

如果仔细观察函数声明，可以看到括号旁边添加了一个`async`关键字。这是什么意思？这本质上是一种让 Swift 知道我们正在声明一个函数的方式，在这个函数内部的某个地方，我们将(可能)调用另一个`async`函数。这个解释并不准确，但对本文来说还是不错的。

好的，所以一个`async`函数是一个调用其他`async`函数的函数，但是，一个`async`函数的核心是什么呢？

这就是 [Swift 关于并发性的文档](https://docs.swift.org/swift-book/LanguageGuide/Concurrency.html)所说的:

> Swift 中的一个异步函数可以**放弃它正在**上运行的线程，当第一个函数被阻塞时，让另一个异步函数在那个线程上运行。

这个解释可能看起来有点吓人，但实际上不是。让我们看看这是什么意思。

假设您在 swift 中有一个普通的函数，没有所有的`async`东西或任何闭包。代码被一行一行地执行，一切都按预期运行。

执行 print 语句只需要很少的时间。非常少。它们不需要 1 秒或 2 秒来执行。他们很高兴在几分之一秒内被处决。

现在，假设您添加了一个计算量很大的函数，并且/或者需要几秒钟才能运行。这可能是一个开销很大的数据库调用，一个“同步”网络调用，或者像下面这样简单的调用:

根据您电脑的规格，此功能可能需要几秒钟才能运行。当这个函数在一个线程中运行时，没有其他代码可以同时运行。这个函数**阻塞了**线程，这意味着同一线程上的所有其他命令都应该等待这个函数完成它的工作，然后才能开始运行。

试着在某个`UIViewController`的`viewDidLoad`中运行这个功能，你会看到应用程序死机了。原因是，所有动画、点击处理和 UI 相关的东西都在主线程上处理，一个线程一次只能处理一件事情。

早在 Swift 早期，就有网络调用的同步功能，它会阻塞线程，直到请求失败或成功。将该代码放在主线程上意味着应用程序将根据请求成功所需的时间而冻结。Apple 不赞成使用这个函数，现在你可以在回调闭包中发出请求并得到结果。

现在，随着 Swift 中`async`的加入，我们可以像编写简单的同步代码一样编写代码，并具有异步行为。两全其美。

当我们使用`async`函数时，函数是**暂停**而不是**阻塞**。当您有暂停功能时，可以暂停该特定功能的执行，然后在稍后的时间继续执行。Kotlin 的编译器(这是一种支持异步函数的语言)使用名为 Continuation *的东西，将您编写的挂起函数转换成类似于 [*闭包的实现。* Swift 也有延续，所以很可能编译器也做同样的事情。在某种意义上，闭包不是你写的，而是编译器为你实现的。](https://stackoverflow.com/a/52925057/7108375)*

## **修改#2:** **在第 6 行增加** `**await**` **关键字**

如果你问`await`是什么，友好地说，就是`async`的伙伴。每当你想使用一个声明为`async`的函数时，你需要在调用那个函数时使用`await`。它们携手并进。就像你有一个标有`throws`的投掷函数时使用`try`关键字一样。苹果开发者自己，在`URLSession`中创建了一个新的`async`函数`data(from:)`。正如我们之前看到的，`async`的搭档是`await`。我们什么时候用`await`？当我们想使用一个`async`函数时。由于`data(from:)`是一个`async`函数，我们需要在调用该函数时使用`await`关键字。

## **修改#3:** **在第 6 行增加** `**try**` **关键字**

苹果在新版 SDK 中加入的`data(from:)`函数，是一个投掷函数。这不是一个新概念，就像你以前遇到过的其他投掷功能一样。唯一的问题是，当一个函数既抛出又是一个`async`函数时，你需要同时使用`try`和`await`，格式为`try await`。

# 最终代码

代码现在使用起来简单多了，但是还可以改进。以前，我们的函数会返回一个可选的`String`和一个可选的`Error`。有了 Swift 的新特性，我们的`async`函数可以是一个抛出函数，这并不容易做到，因为我们之前使用的是闭包。

现在，我们可以重写代码，如下所示:

能够从这个特定函数中抛出的好处是，这个函数要么返回一个`String`，要么抛出一个`Error`。我们不再需要处理由于语言限制而被迫使用的额外可选值。

现在的问题是，如何使用我们编写的函数？与同步函数相比，异步函数使用不同的堆栈框架，所以如果不做额外的工作，就不能从非异步函数调用异步函数。

async 函数应该在名为 a `Task`的上下文中调用，这是一个很大的话题，我们不会详细讨论。但是，启动并运行的最简单方法之一是在一个`Task.init`块中调用您的`async`函数。例如，如果您想在非异步上下文中使用`async`函数，比如在您的`UIViewController`中使用`viewDidLoad`，您可以这样做:

不过有一个特殊的问题，那就是你只能在运行 iOS 15 或更高版本的设备上这样做，这意味着你必须等待一两年才能完全采用这种方法。

# 结论

关于 Swift 的新并发功能，还有很多需要学习的地方，我们还仅仅触及了表面，但可以预计，在几年内，这将成为 Swift 处理并发的主流方式。你可以通过[这个链接](https://www.youtube.com/watch?v=m7V6zHxSWVw)观看我在 YouTube 上关于异步/等待问题的视频。

有关 Swift 新的并发功能的其他一些资源:

*   [关于并发性的 Swift 文档](https://docs.swift.org/swift-book/LanguageGuide/Concurrency.html)
*   [WWDC 并发视频](https://developer.apple.com/news/?id=2o3euotz)
*   [swift ui 中的异步/等待](https://www.raywenderlich.com/25013447-async-await-in-swiftui)

我希望你喜欢这篇文章！你可以在推特上关注我。