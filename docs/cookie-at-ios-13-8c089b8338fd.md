# iOS 13 中的 Cookies

> 原文：<https://betterprogramming.pub/cookie-at-ios-13-8c089b8338fd>

## 如何在您的 iOS 应用程序之间传递数据，以及如何与嵌入在 WKWebViews 中的网页传递数据

![](img/1fe03990a93befaccdb126de613ac8b6.png)

照片由[莎伦·皮特韦](https://unsplash.com/@sharonp?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

很长一段时间以来，Cookies 一直在 iOS 世界中扮演着重要的角色。它们在应用程序中传输和存储状态信息。本文描述了通过将 cookie 设置为 WKWebView，在原生 iOS 应用程序和嵌入式网页之间共享登录会话的方法。

本地 iOS 应用程序和嵌入式网页之间共享授权的能力需求很高。用户只需登录该应用一次，无需再次输入用户名和密码即可访问网络。利用 cookies 的优势给用户带来的巨大便利，超级刺激。

# HTTPCookie

`HTTPCookie`包含 cookie 的属性。它是由一个使用`HTTPCookiePropertyKey`作为键的键-值对字典定义的。

下面初始化包含身份验证令牌的 cookie:

杨梦对饼干的定义

# `HTTPCookieStorage`

iOS 中有两种不同的 cookie 容器:`HTTPCookieStorage`和`WKHTTPCookieStore`。

`HTTPCookieStorage`是基础框架中的 singleton，用于为`URLSessionTask`设置 cookies。每个应用都有自己的共享`HTTPCookieStorage` singleton，不会与其他应用共享。每个存储的 cookie 由一个`HTTPCookie`类的实例表示。

为了在`HTTPCookieStorage`中设置和检索 cookies，我们使用以下代码:

杨梦的 HTTPCookieStorage

# `WKHTTPCookieStore`

`WKWebView`的每个实例都有自己的 cookie 存储容器，命名为`WKHTTPCookieStore`。

一旦用户登录到 iOS 应用程序，我们就从服务器获取 auth 访问令牌。在`WKWebView`使用以下内容将 auth 令牌跨本机应用共享到嵌入式网页:

杨梦的 WKHttpCookieStore

# WKWebsiteDataStore

> `WKWebsiteDataStore`对象代表被选择的网站使用的各种类型的数据。数据类型包括 cookies、磁盘和内存缓存，以及持久数据，如 WebSQL、IndexedDB 数据库和本地存储。— [苹果开发者文档](https://developer.apple.com/documentation/webkit/wkwebsitedatastore)

`WKWebsiteDataStore` 包含一个`default`数据存储器和一个`nonPersistent`数据存储器。通过使用`nonPersistent`，不会有数据写入文件系统。这对于在 web 视图中实现隐私浏览非常有用。

管理与特定`WKWebsiteDataStore`相关联的 HTTP cookies 的`WKHTTPCookieStore`对象，如下面的代码所示:

`WKHTTPCookieStore by Eric Yang`

# WKWebViewConfiguration

`WKWebViewConfiguration`对象是用来初始化 web 视图的属性集合。它包含 web 视图使用的网站数据存储，这是`WKWebsiteDataStore`的一个实例。

```
// WKWebViewConfigurationlet configuration = WKWebViewConfiguration()configuration.websiteDataStore = websiteDataStore// WKWebView with non persistent data storelet webView = WKWebView(frame: CGRect.zero, configuration: configuration)
```

# WKWebView

从 iOS 8.0 开始`WKWebView`取代`UIWebView`。它们之间的主要区别在于,`WKWebView`的实例在一个独立于 iOS 应用的进程中运行，因此它可以利用原生 Safari JavaScript 优化。它使得`WKWebView`加载网页的速度比`UIWebView`快得多，效率也高得多。

它可以用默认配置进行初始化:

```
let webView = WKWebView()
```

或者通过`WKWebViewConfiguration`的定制实例:

```
let webView = WKWebView(frame: CGRect.zero, configuration: configuration)
```

# 结论

文章描述了 iOS 中 cookies 的不同配置和存储。它演示了如何通过使用 cookies 在 iOS 应用程序和嵌入在`WKWebView`的网页之间传递身份验证信息。

# 资源

上面提到的所有代码都可以在[烹饪游乐场](https://gist.github.com/ericleiyang/c4c13e3b9cc572988d17a65a2d9fb811)找到。