# 使用 URLProtocol 为网络层编写集成测试

> 原文：<https://betterprogramming.pub/writing-integration-tests-for-your-network-layer-using-urlprotocol-1de8d310193a>

## 在您的 iOS 应用中利用集成测试的力量

![](img/21b3f0204ea488fa2c81a7b717897df4.png)

[Vojtech Bruzek](https://unsplash.com/@vojtechbruzek?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 什么是集成测试？

这是当所有模块一起工作时运行测试用例的地方。在现实世界中，没有一个模块可以独立运行。不同模块之间总是有数据流。这就是为什么即使你所有的单元测试用例都通过了，错误还是会发生。因此，除了单元测试之外，集成测试也很重要。

# 为什么要进行集成测试？

大多数应用程序都与某种后端进行通信。具体来说，网络层负责与后端通信，来自该层的数据驱动应用程序。因此，这一层的正常运行非常重要，不仅是独立运行，而且与其他模块一起运行。

# 核心思想是什么？

核心思想是完全控制从网络层向上发送的响应(`data`)、状态码(`HTTPURLResponse`)和错误(`error`)。通过控制这些参数，我们可以模拟不同的场景，并检查我们的数据解析逻辑(解码响应)、业务层逻辑(`View` 模型或表示器)和所有其他模块是否按预期运行。

我们还可以检查网络层向我们的`URLSession`实例发出的网络请求，并检查它们是否符合预期。

***免责声明*** *:为了保持本文代码的简单，我有意避免了最佳实践。*

# 什么是 URLProtocol？

这里我们使用一个共享的`URLSession` 实例来进行网络调用。通常，系统负责执行这个网络调用并返回结果。

但是系统也给你机会自己执行请求并返回结果。使用`URLProtocol`就是这样做的。

让我们开始吧:

1.  创建一个`URLProtocol` 子类并实现上面给出的强制方法
2.  在 AppDelegate 的`application (_:didFinishLaunchingWithOptions:)`方法中向系统注册你的`URLProtocol` 子类。
3.  将 URLProtocol 子类的类型分配给`URLSession`的`protocolClasses` 属性。不要创建子类的实例，因为这是由系统自动完成的。

现在，每当`URLSession` 被请求发出网络请求时，它会首先检查`protocolClasses` 数组中的对象，并通过调用`canInit(with: request) — > Bool`询问它们是否要处理请求。通过从子类返回 true，我们将有机会处理请求。

# 我们的示例应用程序结构

我们有一个应用程序，用户可以看到一系列的职位[字符串]。让我们称之为用户提要。我们这里有三个模块:

## 1.网路层

它使用`URLSession`的共享实例发出网络请求，并使用 JSONDecoder 对响应进行解码。

## 2.视图模型

它支持获取和搜索文章，使用网络层。

## 3.视图控制器

它有一个视图模型的实例，并且符合`ViewModelDelegate` 协议。它调用视图模型的函数，并通过委托回调接收回复。

我们可以编写单元测试用例来单独验证每个模块的行为，但是如果当它们都开始相互交互时，它们开始意外地行为，那该怎么办呢？这就是为什么我们将编写一些集成测试来验证它们的行为。

在我们开始编写测试之前，让我们完成我们的`URLSessionProxy`，如下所示:

不要关闭文章🤣。让我来分析一下到底发生了什么:

## `startLoading()`

在我们同意通过在`func canInit(with request: URLRequest) -> Bool`中返回 true 来处理一个 URL 请求之后，这个方法被系统调用。我们应该开始处理请求，并在完成后通知客户端。

## 请求处理程序

我们将使用这个模型对象为请求设置`response`、`data`或`error` 。

## URLSessionProxy 的静态属性

我们有一些静态属性可以用来配置我们的类。它们需要是静态的，因为系统负责创建实际的实例，而我们无权访问这个实例。

1.  `contactedURLs: [URL]`

这存储了系统传递的 URL 列表。这个数组对于检查应用程序访问的 URL 列表很有用。

2.`shouldHandleRequest: ((URLRequest) -> Bool) = { _ in true }`

您可以使用这个闭包来决定是否要处理特定的请求。这是在`func canInit(with request: URLRequest) -> Bool`内部调用的。

3.`handleRequest: ((URLRequest) -> RequestHandler)`？

这是在`func startLoading()`内部调用的。您可以使用这个闭包来决定如何通过返回一个`RequestHandler`的实例来响应一个`URLRequest` 。

我们现在都准备好了！让我们开始编写测试。

# 测试 1:在 viewDidLoad()上触发了用户馈送 API 调用

期望:`URLSessionProxy`的`contactedURLs` 数组包含用户提要 API Url。

我们配置我们的`URLSessionProxy` 来返回`RequestHandler` 实例的一个默认实例，因为我们在这个测试中并不关注那个部分。然后，我们调用`view` 模型上的`viewDidLoad()`来开始这个过程。

我们一直等到我们的期望实现，然后利用我们的`contactedURL`在`URLSessionProxy` 上的属性来检查我们的应用程序是否如预期的那样联系了用户提要 API。

# 测试 2:用户可以在他们的 Feed 上看到帖子

期望:在调用`viewDidLoad()`时，视图模型在其委托上调用`reloadPosts()`后包含模拟帖子。

我们设置了以下内容:

1.  创建一个`view`模型委托并实现对委托方法调用的期望，这样我们就知道是时候检查模拟帖子[String]的视图模型了
2.  配置`URLSessionProxy` 以返回模拟响应
3.  调用视图模型上的 `viewDidLoad()`来启动流程

当期望实现时，我们检查我们的视图模型是否包含我们的模拟帖子。这个测试将使我们确信网络层和视图模型能够正常地协同工作，向用户显示帖子。

# 测试 3:搜索帖子对用户来说工作正常

期望:使用正确的参数调用搜索 API

我们配置我们的`URLSessionProxy` 来捕获搜索`URLRequest` 并将其保存到我们的局部变量中。

然后我们调用视图模型上的搜索方法，然后等待直到我们的`URLRequest` 被捕获。

然后，我们检查`URLRequest` 的不同部分，检查是否所有参数都符合预期。

这只是你能用`URLSessionProxy`做的一些皮毛。希望到现在为止，您已经对如何在您的项目中使用这种技术有了一个概念。

已完成的项目在这里[可用。随意探索！还有一个基于这种技术的](https://github.com/KarthikShiva97/URLSessionProxy)[开源库](https://github.com/WeTransfer/Mocker)。