# 如何在 Swift 中实现 SSL 锁定

> 原文：<https://betterprogramming.pub/how-to-implement-ssl-pinning-in-swift-7c4e8f6ee821>

## 无论你使用苹果的 API 还是 Alamofire，都要在你的应用中防止中间人攻击

![](img/f43fbe953070d25bea0970299fb6196e.png)

照片由[阿尔莫斯·贝托尔德](https://unsplash.com/@almosbech?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/fire?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

> *隐私——就像吃饭和呼吸一样——是生活的基本要求之一。*
> 
> 凯瑟琳·内维尔

几乎每个 iOS 应用程序都与服务器通信以检索信息。有时这些信息是敏感的，您不希望任何人能够访问它们(例如，他们登录应用程序的用户名和密码)。

在 iOS 9 的发布会上，苹果推出了 *App 传输安全*，通过采用 HTTPS 协议和[传输层安全(TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) ，强制应用使用安全的网络连接。这是什么意思？这意味着我们的应用无法通过不安全的连接与服务器通信。实际上，可以绕过这个限制，但是为了避免潜在的攻击，不建议这样做。

所以我的数据是安全的，对吗？答案是否定的！即使 TLS 保护了传输的数据，攻击者也更容易使用黑客或自签名证书设置*中间人攻击*。这意味着他们可以捕捉进出你的应用程序的数据。

在本文中，我将解释如何防止这种攻击，以确保您的应用程序数据安全。我们将涵盖以下主题的一些基本知识:

*   [*对称*](https://en.wikipedia.org/wiki/Symmetric-key_algorithm) 和 [*非对称*](https://en.wikipedia.org/wiki/Public-key_cryptography) 加密。
*   [认证机构](https://en.wikipedia.org/wiki/Certificate_authority)。

# 传输层安全性

首先，我们需要了解 TLS 和中间人攻击的工作原理，了解如何预防它们。TLS 允许您通过安全网络传输数据，遵循三个阶段:

*   客户端通过发送包含受支持的 TLS 版本和用于加密的[密码套件](https://en.wikipedia.org/wiki/Cipher_suite)的消息来启动与服务器的连接。服务器用选择的密码组和一个[数字证书](https://en.wikipedia.org/wiki/Public_key_certificate)进行响应。客户端验证这些数字证书是真实的(即由认证机构发布)。
*   如果验证成功，客户端将生成一个预主密钥，该密钥使用证书中包含的服务器公钥进行加密。服务器用它的私钥解密这个秘密。两者都使用这个秘密来生成主密钥。
*   两者都使用明文加密，并通过使用主密钥和对称加密来解密密文。

但是，什么是证书呢？证书是服务器身份的证明。它只是一个文件，包含拥有证书的服务器的信息，遵循 [X.509 标准](https://en.wikipedia.org/wiki/X.509)。客户端只信任能够提供由可信证书颁发机构之一签名的有效证书的服务器，否则，连接将中止。

为了验证它，应用程序验证:

*   截止日期。
*   数字签名。

# 中间人

中间人是攻击者秘密监听并最终改变双方通信的一种攻击。使用这种攻击来拦截应用程序发送到服务器的数据比你想象的要容易。您所需要的只是一个 SSL 代理服务器。

SSL 代理在客户机和服务器之间执行加密和解密(两个方向的密钥不同)。查尔斯就是一个例子。它位于你的应用程序和互联网之间，你可以检查甚至中途更改数据，以测试你的应用程序如何响应。

*   您的应用程序使用 Charles ProxyCertificate 公钥加密数据，而不是服务器的。Charles 证书是有效的，因为开发者可以将它安装在他的设备中。
*   Charles 能够用其私钥解密所有数据，然后使用服务器的公钥与服务器通信。

这意味着任何开发者都可以读取你的应用程序通过网络发送的所有数据！

下面是[如何设置查尔斯](http://Follow this tutorial to know how to setup Charles.)。

# SSL 固定

幸运的是，有一种简单的方法可以防止这种攻击，通过一种叫做 SSL 固定的技术。

这种技术再次验证服务器证书，即使在 SSL 握手之后。开发人员在客户端应用程序中嵌入一系列可信证书，并在运行时将它们与服务器证书进行比较。在服务器证书和本地副本不匹配的情况下，简单地中止连接。

这意味着应用程序将检查查尔斯的证书，即使它是可信的，它也将不同于嵌入在应用程序中的本地证书。

一个缺点是，一个固定的证书有一个截止日期，这需要应用程序更新，除非开发人员也在应用程序中固定未来的证书。实际上，不需要固定整个证书，您可以只使用散列的公钥(这是一个更好的方法，因为我们可以在未来的证书中重用相同的散列密钥)。

# 锁定 Swift

评估信任分两步走。

*   验证证书的数字签名。你的应用可以依赖任何嵌入在 [iOS](https://support.apple.com/en-us/HT204132) 中的根证书，或者你可以提供自己的根证书。
*   根据信任策略测试证书。该策略指示证书的特定字段或扩展应该如何被信任。

所有这些活动都是通过使用一个或多个证书和策略对象准备的`[SecTrust](https://developer.apple.com/documentation/security/sectrust)`对象的实例来实现的。

## 准备证书

Apple 提供了`[SecCertificate](https://developer.apple.com/documentation/security/seccertificate)`来代表 X.509 证书。如果您将证书添加到捆绑包中，您可以使用以下命令检索它:

```
**let** filePath = Bundle.main.path(forResource: name, ofType: type)!**let** data = **try**! Data(contentsOf: URL(fileURLWithPath: filePath))**let** certificate = SecCertificateCreateWithData(**nil**, data **as** CFData)
```

[在这里](https://developer.apple.com/documentation/security/certificate_key_and_trust_services/certificates/getting_a_certificate)您还可以找到更复杂的解决方案，例如，使用钥匙链和身份识别。没有“最佳”的解决方案，因为它主要取决于问题和你的应用程序需要的安全级别。我建议你仔细阅读并全部尝试，然后决定哪一个适合你的需要。

## 准备一项政策

最好的选择通常是使用预定义的策略之一。例如，您可以将 X509 证书的标准基本策略用于:

```
let policies = SecPolicyCreateBasicX509()
```

这对你的应用程序来说可能足够了，但是如果你需要更多的灵活性，你可以用`[SecPolicyCreateSSL(_:_:)](https://developer.apple.com/documentation/security/1392592-secpolicycreatessl)`创建你自己的 SSL 策略。

## 秘方

现在是时候验证证书和策略了。苹果用一个`[SecTrust](https://developer.apple.com/documentation/security/sectrust)`对象方便了所有这些操作。

```
var optionalTrust: SecTrust?
let status = SecTrustCreateWithCertificates(certArray as AnyObject,
                                            policy,
                                            &optionalTrust)
guard status == errSecSuccess else { return }
let trust = optionalTrust!
```

`SecTrustCreateWithCertificates(_:_:_:)`根据提供的证书和策略创建信任管理对象。

您可以使用`SecTrustEvaluateWithError(_:_:)`来验证一个信任对象。从文档中:

> 此方法评估证书的有效性，以针对特定用途建立信任，例如，创建数字签名或建立安全套接字层连接。该方法根据包含在信任管理对象中的一个或多个策略，通过验证证书的签名加上其证书链中的证书的签名，直到锚证书，来验证证书。
> 
> 如果信任管理实例缺少验证叶证书所需的一些证书，`SecTrustEvaluateWithError(_:_:)`搜索证书:
> 
> -在用户的钥匙串中。
> 
> -在您以前通过调用 SecTrustSetAnchorCertificates(_:_:)提供的任何证书中。
> 
> -在系统为此目的提供的一组钥匙链中。
> 
> -通过网络，如果用于构建链的证书中存在某些扩展。

你现在唯一需要做的就是用你的策略评估你在网络通话中收到的`SecTrust`，并将证书与应用程序中的证书进行比较。

让我们来看一个例子:

这是一个简单的例子，但是还有许多其他参数和对象来处理所有可能的`SecTrust`验证。我建议你仔细阅读苹果公司提供的所有文档，并在尝试将其用于你的应用程序之前进行测试。

[这里是](https://developer.apple.com/documentation/security/certificate_key_and_trust_services)主链接。

# 用 Alamofire 5 固定

如您所见，使用普通 API 相当复杂。我推荐使用`Alamofire`，它是一个外部库，提供了更高级别的 API，让您的生活更加简单。

一切都基于`ServerTrustEvaluating`协议，该协议提供了一种执行任何类型的服务器信任评估的方法。Alamofire 包括许多不同类型的信任评估程序，例如:

*   `DefaultTrustEvaluator`:允许您控制是否验证挑战提供的主机。
*   `RevocationTrustEvaluator`:检查收到的证书的状态，确保它没有被撤销。
*   `PinnedCertificatesTrustEvaluator`:如果其中一个固定证书与其中一个服务器证书匹配，则认为服务器信任有效。
*   `PublicKeysTrustEvaluator`:如果固定的公钥之一与服务器证书的公钥之一相匹配，则认为服务器信任有效。

你只需要为每个 API 选择使用哪个`ServerTrustEvaluating`:

```
let evaluators: [String: ServerTrustEvaluating] = [
    "cert.example.com": PinnedCertificatesTrustEvalutor(),
]

let manager = ServerTrustManager(evaluators: serverTrustPolicies)
```

然后与他们建立一个会话:

```
let serverTrustManager = ServerTrustManager(evaluators: evaluators)**self**.session = Session(serverTrustManager: serverTrustManager)
```

你可以在这里阅读完整的文档[。](https://github.com/Alamofire/Alamofire/blob/master/Documentation/AdvancedUsage.md#security)

# 如何下载证书

如果你想做一些测试或者你需要验证一个公共的 API，你可以下载证书并把它绑定到你的应用上。

打开您的终端，写下以下内容:

```
openssl s_client -connect **<url>**:443 </dev/null \
  | openssl x509 -outform DER -out **<filename>**.der
```

这会将证书下载到您当前的文件夹中。

我希望你喜欢这篇文章！如果你有任何疑问，请随时联系我。