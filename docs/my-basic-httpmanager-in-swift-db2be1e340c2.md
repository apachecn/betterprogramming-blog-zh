# 我在 Swift 中的基本 HTTPManager

> 原文：<https://betterprogramming.pub/my-basic-httpmanager-in-swift-db2be1e340c2>

## 使网络请求变得更加容易

![](img/06fa82ae81e0165b19b2e06e1a400c14.png)

[drmakete 实验室](https://unsplash.com/@drmakete?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/network?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

为了在 Swift 中将网络代码从视图和视图控制器中分离出来，我通常使用一个简单的 HTTPManager。

这篇文章是关于这是如何工作的，以及如何在你自己的项目中实现，也许，最重要的是，如何测试。

难度:初级|简单| **普通** |挑战

# 先决条件

*   有理由打网络电话
*   能够产生一句“你好，世界！”iOS 应用程序(本[文章](https://medium.com/@stevenpcurtis.sc/your-first-swift-application-without-a-mac-79598ad839f8)中的指南)
*   Swift 中的协议(指南[此处为](https://medium.com/swlh/for-loops-in-swift-628a6a2b2ea7))
*   能够使用相关类型(此处的指南[为](https://medium.com/@stevenpcurtis.sc/protocols-with-associated-types-in-swift-eec850af3c02))
*   闭合装置(此处为导向装置
*   雨燕的`Result`型(此处为导向
*   依赖注入测试(此处为指南
*   使用`Codable`(此处导向[为](http://A type alias for the Encodable and Decodable protocols))
*   仿制药(指南[此处](https://medium.com/better-programming/generics-in-swift-aa111f1c549))
*   测试时处理异步调用(指南[此处为](https://medium.com/@stevenpcurtis.sc/result-type-in-swift-1b4e2a84f2c2))

# 术语

*   类:定义公共属性和方法的对象。
*   数据任务:检索 URL 内容的任务。
*   错误:程序产生错误结果而导致的问题。
*   基础:包含定义基本应用功能的数据类型、集合和服务的框架。
*   框架:一种与应用程序、团队成员或更远的地方共享代码包的方式。
*   HTTPManager:我创建并选择用来将网络代码从视图/视图控制器中分离出来的类。
*   模拟:对对象的方法调用的虚假响应，允许检查特定的方法调用或属性。
*   模型:存储数据和操作数据的逻辑的地方。也许模型对象或网络代码存储在这里。把这看作是应用程序的本质。
*   网络:共享资源的一组计算机或计算机设备。
*   协议:适合某项功能的方法、属性和需求的蓝图。
*   结果类型:表示成功或失败的值，包括每种情况下的关联值。
*   URL:统一资源定位器。网页或资源的地址。

# HTTPManager

这都是关于将`HTTPManager`分离成与视图和视图控制器分离的代码，以使这些功能可测试和可重用。

这是一件好事，因为从互联网下载是大多数应用程序都需要做的事情，而且你肯定希望有一套经过良好测试的功能来完成这些基本但(相当)棘手的测试任务。

我不希望读者在我们进行的过程中在 Xcode 中输入这么多代码。为了帮助解决这个问题，我创建了一个 Git 存储库(在 GitHub 上是[)。](https://github.com/stevencurtis/BasicHTTPManager)

请继续阅读…

# HTTPManager

## HTTPManager 的使用

HTTPManager 类用于将任何特定应用程序中的网络代码与表示代码分开。

## 创建一个协议

如果我们知道`HTTPManager`被建议做什么，我们就可以建立一个经理会遵守的`protocol`。

现在，符合一个`protocol`的原因是双重的。

*   我们可以在编写`class`之前定义`HTTPManager`的功能，并确保我们的管理器符合这个`protocol`。
*   我们可以交换`HTTPManager`的实现来增强我们的测试过程。

在这种情况下，我实现了一个接口，其中`HTTPManager`将从`URL`获得结果，并返回 Swift 的`Result type`。在这种情况下，我没有指定灵活的错误类型，只是使用了 Swift 的`Error`类型。

协议中的`associatedtype`允许我们稍后在代码中用具体类型填充会话。

## 符合协议

这意味着我们必须通过扩展来强制`HTTPManager`符合`protocol`类。

```
extension HTTPManager : HTTPManagerProtocol {}
```

精彩！然后我们可以对`HTTPManager`进行编码。

## 定义 HTTPManager

简单的`HTTPManager`使用简单的`dataTask`将`URL`的内容下载到你的设备上。

我已经在模拟器上多次运行相同的代码，所以我将缓存策略设置为`reloadIgnoringLocalCacheData`，以便每次都下载`URL`的内容。

如果状态码在`200`和`299`范围内(响应成功)，`Result Type`返回数据。

现在，当然，如果由`dataTask`处理的`URL`返回无效响应，我们返回我们的`error`(它已经有一个选项`invalidResponse(Data?, URLResponse?)`，这很好)。

`HTTPManager`类本身被`protocol`的关联类型所绑定，这意味着`URLProtocol`实例可以被生产代码中的`URLSession`或测试代码中的`URLSessionMock`所替代。

从某种意义上说，除了缓存被设置为忽略 60 秒超时的本地缓存之外，其余大部分代码都是样板文件。

## 调用 HTTPManager

我们从视图控制器中取出`HTTPManager`。为了提高可测试性，我们有一个单独的`downloadData`函数。

`downloadData`函数有一个受`HTTPManagerProtocol`约束的参数(由于相关联的类型),并返回带有来自`URL`的数据的`result type`,在`HTTPManager`中执行 get。

当然，它使用`URLSession`，并且在生产代码中使用`URLSession.shared`(它是`URLSession`的单例实例)。

# URLSession

`URLSession`发送和接收请求，本质上是为了 API 下载数据。

## 符合协议

以类似于为`HTTPManager`创建协议的方式，`URLSession`可以遵循协议来启用依赖注入。

该协议在以下代码段中定义:

然后我们让`URLSession`符合它。

```
extension URLSession: URLSessionProtocol {}
```

现在，由于`URLSession`是一个`foundation`类，我们不需要实现它，然而，我们将在本文后面实现一个`mock`类。

# URLSessionDataTask

`URLSessionDataTask`将符合一个协议，这指定了任何符合协议的东西都需要实现 resume 方法。

```
protocol URLSessionDataTaskProtocol {
    func resume()
}
```

结果是:

```
extension URLSessionDataTask: URLSessionDataTaskProtocol {}
```

# 端点模型

现在我们使用`Codable`创建一个`model`来允许我们解码数据，所以我们开发了一个`EndPointModel`来做这件事(然后被`httpManager`调用)。

# 实例化 HTTPManager

我们需要实例化`URLSession`和`HTTPManager`。在这里，我们通过我们的`URL`:

这使得实际运行这个项目并看到它从 URL 下载成为可能:

```
https://docs.google.com/uc?export=download&id=0Bz-w5tutuZIYeDU0VDRFWG9IVUE
```

现在，选择这个`URL`只是因为这个特定的文件相当大。

嘿，你。为什么不下载 Git 链接，看看它是如何从 [GitHub](https://github.com/stevencurtis/BasicHTTPManager) 运行的。

# 测试经理

使用依赖注入来模拟代码的不同部分有点复杂，从这里开始，我将使用 gists 来展示所有内容的实现，因为这样更容易阅读。

为了检查响应(不是来自网络，而是我们的响应),我创建了一些 JSON 响应；这里没有技巧，只是一些带有预期响应的格式正确的 JSON 字符串。

## URLSessionMock

为了符合`URLSessionProtocol`，`URLSessionMock`需要实现`dataTask(with url:)`和`dataTask(with request:)`。

注意每一个`dataTask`方法都返回`URLSessionDataTaskMock`，它是用于`URLSessionProtocol`中相关类型的对象。

在实践中，实现简单地返回`URLSessionDataTaskMock`，它本身必须实现`resume()`，以及通过`completionHandler`给出的大部分数据，因此，逻辑并不太复杂，但是代码(嗯)可以更简单。

如果有一个错误(您希望返回)，您可以在实例上设置它(我知道这不是很优雅)，如`HTTPManagerTests`所示。

## **URLSessionDataTaskMock**

`resume()`的实现不做任何事情，只是返回在`URLSessionDataTaskMock`的初始化器中设置的闭包。

## HTTPManagerTests —避免进行网络调用

可以通过注入`URLSessionMock`来测试`HTTPManager`，因此我们*而不是*进行网络调用。

测试要返回的特定数据的机制并不复杂，但是我们当然需要测试[异步调用](https://medium.com/@stevenpcurtis.sc/how-to-test-asynchronous-functions-using-expectation-2c9183fd99c9)并处理[结果类型](https://medium.com/@stevenpcurtis.sc/result-type-in-swift-1b4e2a84f2c2)，尽管我们确实需要将数据类型解码为字符串。

## 视图控制测试

因为`HTTPManager`是从 viewcontroller 中的一个单独的方法实例化的，所以它可以被测试。

现在，很清楚(希望如此),如果您使用 MVVM 或类似的，您可能不会从视图控制器中进行实例化，但是，本教程有一些限制——所以我们从那里进行实例化。

我们能够注入 a `HTTPManager`，我们再次知道它调用了 mock `URLSession`，所以我们不会进行网络调用。

下面是`HTTPManagerMock`的定义:

总之，我的测试覆盖率已经达到了 76.5% ，也就是说*几乎*足够好了(取决于你在哪里工作)。我认为我可以做得更好——我认为你可以做得更好——我对你接近 100%测试覆盖率的方法很感兴趣。

# 结论

拥有一个能够从一个`URL`下载的`HTTPManager`显然是一件好事——拥有一个经过良好测试和良好记录的可以重用的`class`真的很重要。

您将会用到它，因此锁定和加载这个类并准备好使用它是非常值得的。

我希望这个教程已经给了你这样一个类的能力。

# Git 链接

我不期望任何人根据前面的解释来编码所有的东西。因此，我提供了一个已经完成的工作的 Git 链接！

[](https://github.com/stevencurtis/BasicHTTPManager) [## stevencurtis/BasicHTTPManager

### 基本的 HTTPManager。在 GitHub 上创建一个帐户，为 stevencurtis/BasicHTTPManager 的开发做出贡献。

github.com](https://github.com/stevencurtis/BasicHTTPManager) 

# 扩展你的知识

*   苹果有一份`[dataTask(with:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsession/1410330-datatask)`的文档。
*   苹果对`[downloadTask(with:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsession/1411511-downloadtask)`有进一步的文档。
*   [HTTP 响应状态码](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)。