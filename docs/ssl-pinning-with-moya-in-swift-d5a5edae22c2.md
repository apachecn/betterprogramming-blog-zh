# 使用 Swift 中的 Moya 进行 SSL 锁定

> 原文：<https://betterprogramming.pub/ssl-pinning-with-moya-in-swift-d5a5edae22c2>

## 通过 Moya 使用 Alamofire 的 SSL-pinning 实现

![](img/88ab85c3d2f224930a8988e07a91ccaf.png)

[钳工](https://unsplash.com/@benchaccounting?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)对[去毛刺](https://unsplash.com/search/photos/computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍照

一些应用程序(例如，处理金钱的应用程序)需要比通常的 iOS 应用程序多一点的安全性。

这包括在[钥匙链](https://developer.apple.com/documentation/security/keychain_services)上存储敏感数据，确保 [HTTP 请求在`Info.plist`设置中不被允许](https://cocoacasts.com/app-transport-security-has-blocked-my-request)，当然还有 SSL 锁定。

无论你是直接使用`URLSession`还是 [Alamofire](https://github.com/Alamofire/Alamofire) ，在你的 iOS 应用中找到如何实现 SSL 锁定的教程都相对容易。

只需谷歌一下或者[在这里参考我最喜欢的一个](https://medium.com/@andersongusmao/certificate-pinning-technique-on-ios-af0f0813e88)(这个很不错，因为它给了你一些理论背景，也向你展示了如何测试你的实现)。

在这篇文章中，我想告诉你在使用[摩亚](https://github.com/Moya/Moya)时要做什么，因为我在谷歌上搜索时不容易找到答案。

# 通过 Moya 使用 Alamofire 的 SSL Pinning 实现

正如大多数人所知，Moya 是建立在 Alamofire 之上的一个网络抽象层。

因此，我们可以配置`Alamofire.SessionManager` (类型别名为`Manager`)，Moya 用它来发出请求，这样就可以了。

这个管理器是提供者初始化器的可选参数，我们必须确保每个提供者都接收到正确的管理器，否则我们可能会有一些 SSL 固定不起作用的请求。

接下来让我们看看下面的实现。

使用 Moya-Alamofire 组合和更多代码来实现 SSL 锁定，您只需要使用这些代码。

正如您所看到的，提供者初始化(1)非常简单，您只需要为管理器添加一个额外的参数，这里我使用了`AlamofireSessionManagerBuilder().build()`。

这是一个[构建器模式](https://en.wikipedia.org/wiki/Builder_pattern)，这样，我几乎使用了构建器提供的默认模式`SessionManager`。

我创建的构建器(2)默认在初始化器(3)上配置 SSL 固定。有一个示例函数(4)展示了如何实现其他一些管理器行为，比如增加上传文件的超时时间。

如果您希望有一个非 SSL 固定的管理器，并为上传做好准备(即具有更长的超时)，您可以像这样使用它:

```
AlamofireSessionManagerBuilder(includeSSLPinning: false).prepareForFileUpload().build()
```

最后，`build()`方法(5)将输出构造好的`SessionManager`，考虑之前已经设置的配置。

顺便提一下，我公司的 SSL 证书覆盖了无限数量的子域，格式为`*.mycompany.com`，其中`*`代表任何子域。

如果你仔细看看代码，你可能会看到我手动添加了`firstsubdomain.mycompany.com`和`secondsubdomain.mycompany.com`。

这是因为，当您以这种方式配置 Alamofire 时，它使用其默认的`SessionDelegate`,其中有以下内容:

```
**let** host = challenge.protectionSpace.host**if let** serverTrustPolicy = session.serverTrustPolicyManager?.serverTrustPolicy(forHost: host), **let** serverTrust = challenge.protectionSpace.serverTrust {//omitted code}
```

那里的主机变量将得到你的整个域，所以，在我的例子中，它在某些情况下是`firstsubdomain.mycompany.com` ，在其他情况下是`secondsubdomain.mycompany.com`。因此，我必须添加两个子域名。

在我看来，这是使用 Alamofire 的`SessionManager`的局限性之一，你可以在这篇文章的结尾找到另一种可能的方法，指出一种可能的解决方案。

## **为什么要使用构建器？**

因为，如果您想向您的会话管理器添加额外的配置(正如我用`prepareForFileUpload`方法举例说明的)，这样做相当容易。

## **为什么 SSL-pinning 配置是在初始化器上完成的？**

你完全可以在构建器上有一个方法，比如`configureSSLPinning()` ，每次都要调用它！

我只是认为这可能会导致程序员忘记调用它，并在可能发生中间人攻击的地方有特定的请求。

如果您不喜欢传递布尔值，您可以做的(也许应该做的)是始终在`init()`上配置 SSL-pinning，然后创建一个可以在以后调用的`disableSSLPinning()` 方法。

# **另一种可能的方法——通过 Moya 实现 URLSession 的 SSL-Pinning**

我不是真的涉及这个话题，只是想让你知道这也是可能的。我的做法是给经理注入一个新的`SessionDelegate`:

```
SessionManager(delegate: CustomSessionDelegate())
```

这个`CustomSessionDelegate`将不得不从 Alamofire 的`SessionDelegate`继承，并重写(至少)以下方法:

```
urlSession( **_** session: URLSession, didReceive challenge: URLAuthenticationChallenge, completionHandler: **@escaping** (URLSession.AuthChallengeDisposition, URLCredential?) -> Void){
```

如果有人实现了这个，一定要让我知道，我会在这里链接它。如果以后我自己这样做，我一定会更新文章。

我希望这对正在使用 Moya 并试图实现 SSL pinning 的人有所帮助。如果你发现任何错误或想提出改进建议，请告诉我。