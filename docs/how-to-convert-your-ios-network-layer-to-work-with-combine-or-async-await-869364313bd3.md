# 如何转换您的 iOS 网络层以使用 Combine 或 Async/await

> 原文：<https://betterprogramming.pub/how-to-convert-your-ios-network-layer-to-work-with-combine-or-async-await-869364313bd3>

## 使网络层适应不同的接口

![](img/e3e978ad4b89dfe9cc83cee84ca7a7a7.png)

照片由 [Luana Azevedo](https://unsplash.com/@azevdoluana?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

今年，Swift 引入了新的`async`和`await`关键字。有了它们，我们现在有三种方法来执行网络操作:

*   通过使用旧的回调模型
*   通过使用`Combine`
*   通过使用`async-await`

在某些情况下，我们很早就对一种技术做出了决定，并试图在我们应用程序的所有模块中使用它:例如，如果我们从`Combine`开始，我们可能希望在任何地方都使用`Combine`。

这种方法具有一致性的明显优势:我们不必考虑如何实现某些东西，当探索代码库时，加入项目的人不会对多种技术感到惊讶。

一致性的代价是灵活性。我们希望在每种情况下都使用最好的工具。我们可以拥有要求我们对事件流使用`Combine`的模块，以及其他可以对一次性操作使用回调的模块。

在今天的文章中，我们将探讨如何将网络层从一种技术转换为另一种技术。网络层只是一个例子:每当你需要将一个 API 从一种技术转换到另一种技术时，我们可以利用这些技术。

# 使用回调

我们用于异步操作的第一项技术是基于回调的技术。我们访问一个`[URLSession](https://developer.apple.com/documentation/foundation/urlsession)`对象，并使用`[dataTask(with:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsession/1407613-datatask)`方法获得一个`[URLSessionDataTask](https://developer.apple.com/documentation/foundation/urlsessiondatatask)`对象。可以通过调用`.resume()`方法来启动任务。

典型的实现如下所示:

在这段代码中，我们有一个通用的`CallbackNetworkService`，我们可以用它来对后端执行`GET`请求。

可以通过传递一个`URLSession`对象来配置服务。

`get<T: Decodable>(url:callback:)`方法创建`URLRequest`对象，它调用`dataTask(with:callback:)`方法，然后在任务上调用`resume()`。

大多数响应处理代码都致力于管理错误。

1.  我们检查网络是否返回了错误。
2.  我们检查后端是否返回了错误。
3.  我们验证我们有一些数据要解码。
4.  我们试图解码数据。如果解码成功，我们用对象调用`callback`。
5.  否则，我们会通知调用者发生了解码错误或未知错误。

## 使用回拨消费

一旦我们有了网络服务，我们就想使用它。假设我们想在一个`Profile`模块中检索一个用户的数据。

`Profile`模块需要一个具有以下接口的服务:

我们编写代码连接应用程序所有模块的复合根可以创建一个简单的`[Adapter](https://en.wikipedia.org/wiki/Adapter_pattern)`来连接两个接口。当两个接口共享相同的技术时，代码非常简单。

适配器得到一个`CallbackNetworkService`作为`init`参数，它符合`CallbackUserService`。它通过创建`URL`并调用`networkService.get`方法来实现`getUser(id:callback:)`。

假设两个回调共享相同的签名，我们可以将`UserService`的回调作为`NetworkService`的回调来传递。通常，我们可能需要转换由`NetworkService`获得的`Result`并手动调用`UserService`回调。

## 用联合收割机消费

现在想象一下`Profile`模块正在使用 Combine。那样的话，`UserService`界面就不一样了，看起来是这样的。

该方法只接受一个用户标识符并返回一个发布者。

在执行网络请求之前，`CallbackToCombineAdapter`必须创建一个发布者。代码如下所示:

对于我们将在本文中看到的所有适配器，适配器的设置都是相同的。我们定义一个符合服务协议的类型，我们传递一个`networkService`，我们通过创建`URL`来实现协议一致性。我们不打算在下面的适配器中讨论这一部分。

当实现协议时，我们创建一个可以执行网络的发布者。我们创建一个`Deferred`发布者:它接受一个返回发布者的闭包。当收到订阅时，执行闭包。我们需要这样做，因为有一些发布者一返回就发布，比如`Just`或`Fail`发布者，但是对于这个用例，我们需要等到有东西订阅了发布者才发布网络请求。

然后，我们创建一个`Future`:一个最终将发布一个值的发布者。发布者的主体实际上通过调用`networkService`来执行网络请求。`Future`发布者给了我们一个`promise`，它共享了`NetworkService`回调的相同签名，因此我们可以将它用作`networkService`本身的回调。

## 使用异步 Await 消费

在`Profile`模块使用异步 await 的情况下，我们需要将网络服务转换为以下接口。

可将`networkService`转换为`AsyncAwaitUserService`的适配器代码如下:

为了实现适配器，我们需要将`networkService.get`方法包装在`withCheckedThrowingContinuation`闭包中。这个 API 允许我们将基于回调的异步 API 转换成异步等待 API。`continuation`是一个对象，它让我们`resume`用一个值来执行，或者让我们通过抛出一个错误来中断执行。

根据使用情况，我们可以使用其他版本的`withXXXContinuation` API:

*   `withCheckedThrowingContinuation`:可以在身体可以抛出错误的时候使用。名字的`checked`部分告诉我们，这个函数将执行一些检查，以确保每个分支都调用延续一次，并且只调用一次。
*   `withCheckedContinuation`:当机体不能抛出错误时可以使用。名称的`checked`部分告诉我们，该函数将执行一些检查，以确保每个分支调用 continuation 一次，且仅一次。
*   `withUnsafeThrowingContinuation`:可以用在身体会扔错误的时候。名称的`unsafe`部分意味着 API 不做任何事情来确保每个执行分支调用一次延续。
*   `withUnsafeContinuation`:可以用在身体不能投出错误的时候。名称的`unsafe`部分意味着 API 不做任何事情来确保每个执行分支调用一次延续。

# 使用联合收割机

现在让我们假设已经使用`Combine`实现了网络层。在这种情况下，使用的`URLSession`方法是`dataTaskPublisher`，它不需要调用`resume()`。

该代码实现了与前面的网络服务相同的功能。我们创建一个`dataTaskPublisher`，传递一个`URLRequest`。我们尝试将结果映射到`Data`，对其进行解码。在这样做的同时，我们还验证后端没有引发任何错误。最后，我们将`erase`转换为`AnyPublisher`。

让我们来看看如何消耗它。

## 使用回拨消费

第一个接口是`CallbackUserService`。适配器看起来像这样。

与以前的适配器的主要区别是我们需要处理`Combine`订阅。为此，适配器需要是一个`class`，并且它需要拥有一个`Set<AnyCancellable>`来存储订阅。

当收到发布的值时，通过订阅发布者并调用带有`.success`值的回调来实现`getUser`方法。如果我们收到一个失败的完成，我们用一个`.failure`值调用回调。

> **注意:**这是一个简单的实现，目的是让例子简短易懂。另一个更通用的实现可以收集接收到的值，并在成功完成时调用回调。

## 用联合收割机消费

当客户端也在使用 Combine 时，适配器准备发布器并转发它。

在这种情况下，适配器不必处理订阅。`CombineUserService`实现准备 URL 并将发布者返回给调用者。

## 使用异步等待进行消费

最后，让我们看看如何将组合服务转换为异步等待服务。

同样对于这个转换，我们需要使用`withCheckedThrowingContinuation` API。下面的代码显示了如何使用它:

这个适配器必须处理订阅，所以它是一个拥有自己的一组`Cancellable`的`class`。`continuation`API 用于将订阅打包给发布者。

一旦我们有了`continuation`对象，我们就可以模仿回调实现，当发布者发送一个值时使用`continuation.resume(returning:)` API，当我们收到一个带有错误的`completion`时使用`continuation.resume(throwing:)` API。

# 使用异步等待

这个包的最后一项技术是新的 async-await API。它可以简化我们的代码，而且，[从 Xcode 13.2](https://www.swiftbysundell.com/articles/making-async-system-apis-backward-compatible/) 开始，它已经被移植到 iOS 13 上。

`AsyncAwaitNetworkService`看起来是这样的

我们使用`URLSession`的`data`方法来发出请求。如果请求失败，我们`throw`一个错误。否则，我们得到我们的数据和`URLResponse`对象。然后，我们检查状态代码，以了解后端是否返回了错误。如果一切正常，我们尝试解码对象。

让我们用各种`Profile`的服务来消费它。

## 使用回拨消费

第一个适配器将 async-await 范式转换为回调模型。为了在非异步函数中执行一些异步等待函数，我们需要使用`[Task](https://developer.apple.com/documentation/swift/task)` API。

让我们来关注一下`Task` API。我们用它来包装网络服务`async`的`get`方法的调用。当该方法完成时，我们可能有一个有效的`User`，我们用一个`success`值调用回调。

在出现错误的情况下，我们捕获它，并使用错误本身调用回调。

## 用联合收割机消费

`AsyncAwaitToCombineAdapter`和回调的非常相似。我们仍然需要产生一个发布者，并且我们必须使用`Task` API 将异步等待代码转换成可以被`Combine`使用的东西。

代码如下所示:

代码与`CallbackToCombineAdapter`的结构相同。我们创造`Deferred`和`Future` `Publishers`进入`Combine`世界。一旦我们有了来自`Future`发布者的`promise`，我们就使用`Task` API 来执行异步工作。`Task`的主体与`AsyncAwaitToCallbackAdapter`的主体非常相似:当异步函数完成时，我们使用承诺来发布结果。

## 异步等待消费

最后一个适配器很简单。我们有相同的技术，代码非常直观。

唯一要做的事情是调用`networkService.get`方法，使用`try await`关键字。

# 结论

在今天的文章中，我们探讨了可以用来执行网络请求的各种技术。我们讨论了基本的回调模型、使用 Combine 的实现和使用 async-await 的实现。

然后，我们讨论了如何将它们从一种技术转换成另一种技术。通过转换网络服务，我们探索了各种帮助我们完成任务的苹果 API，如`Deferred`和`Future`发布者，以及`Task`和`Continuation` API。

知道如何将界面从一个世界转换到另一个世界是很重要的。我们已经在`URLSession`和网络请求的上下文中看到了这些技术，但是它们可以应用于我们可能遇到的每一个异步 API。