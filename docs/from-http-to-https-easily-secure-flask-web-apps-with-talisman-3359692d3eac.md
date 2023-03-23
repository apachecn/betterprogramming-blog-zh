# 从 HTTP 到 HTTPS —使用 Talisman 轻松保护 Flask Web 应用程序

> 原文：<https://betterprogramming.pub/from-http-to-https-easily-secure-flask-web-apps-with-talisman-3359692d3eac>

## 利用 Python 的 Talisman 库建立 HTTPS 协议以增强 web 应用程序的安全性

![](img/daaf3c516abdcba89d12c5e3544143f1.png)

乔恩·摩尔在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在像 [Heroku](https://devcenter.heroku.com/articles/ssl) 这样的云解决方案上将你的 Python 项目(例如 ML 模型、网站)部署为 Flask web 应用之后，你可能会注意到它加载了一个不安全的 HTTP 连接，尽管已经配置了 [SSL/TSL](https://ahrefs.com/blog/what-is-https/#how-tls-works) 证书。

这种不安全的请求会带来安全隐患，因为恶意行为者很容易破坏客户端和服务器之间的通信。

本文探讨了如何使用 Talisman 库将所有传入的应用程序请求重定向到 HTTPS，而不是 HTTP。

## *目录*

> *(1)* [*关于 HTTPS*](#11d2) *(2)*[*什么是护身符？*](#d364) *(3)*[*基本用法*](#4cb5) *(4)*[*高级选项*](#aebc)

# 关于 HTTPS

HTTPS(安全超文本传输协议)是 HTTP 协议的安全版本，因为它通过 [SSL/TLS 协议](https://www.ssl.com/faqs/faq-what-is-ssl/)添加了一个额外的加密、认证和完整性层。

HTTPS 使信用卡号和登录凭证等敏感数据在互联网上安全传输成为可能。因此，HTTPS 正迅速成为所有网站的标准协议，无论它们是否涉及敏感数据的交换。

例如，谷歌 Chrome 已经开始将非 HTTPS 网站标记为不安全，而苹果要求 iOS 应用程序的 API 通信使用 HTTPS。

# 什么是护身符？

[Talisman](https://github.com/GoogleCloudPlatform/flask-talisman) 是一个 Python Flask 扩展，它配置 Flask web 应用程序，使其免受常见 web 安全问题的影响。

Talisman 由 [Google 云平台社区](https://github.com/GoogleCloudPlatform/community)开发，它通过在 web 应用程序中启用和设置 HTTP 安全头来规定 HTTP 通信的安全相关细节。

HTTP 安全头通过在 web 应用程序运行时限制浏览器和服务器内的行为来提供额外的安全性。

Talisman 的一个关键特性是强制所有 web 应用程序连接到 HTTPS，这将是本文的重点。

![](img/ea7aadd2b32166585d2bd32f5a9c3a68.png)

照片由[弗兰克](https://unsplash.com/@franckinjapan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 基本用法

我们首先用下面的命令安装 Talisman(*flask-Talisman*):

```
pip install flask-talisman
```

一旦完成，我们导入库，并在我们的主 Python 脚本中用 Talisman 包装我们的 Flask 应用程序。

只用一行代码，我们就成功地为 Flask web 应用程序实现了 Talisman 的安全优势。

以下是关于护身符包装纸的一些重要注意事项:

*   参数`force_https`的默认值为*真*，这意味着所有请求(与调试无关的)将被自动定向到 HTTPS。因此，没有必要显式设置参数。
*   您可能会注意到，在示例中，参数`content_security_policy`被设置为 *None* 。原因是当内容安全策略保留为默认值时，许多用户遇到了问题(例如，HTTPS 重定向失败)。这个策略是高级 Talisman 选项的一部分，我们将在下面探讨。

# 高级选项

内容安全策略(CSP)是高级 Talisman 选项的一部分，可将您的 web 应用程序的安全性提升到一个新的水平。

默认的 CSP 选项非常严格，会阻止加载与应用程序不在同一个域中的任何资源。因此，大多数网络应用程序需要改变这个策略，这也是我们在上面的例子中设置`content_security_policy=None`的原因。

CSP 允许您定义 web 应用程序上明确允许的特定资源类型(例如，图像、视频、代码、网站等。).

假设我们有以下需求:

1.  允许从任何地方加载图像
2.  将音频或视频媒体仅限于两个受信任的提供商(`kenneth.com`和`leung.com`)
3.  仅允许来自托管可信代码的特定服务器的可执行脚本(`userscripts.trusted.com`)

下面是将上述内容转换为 Talisman 设置的 Python 代码:

# 包装它

我们已经看到如何利用 Talisman 来实现重要 Flask web 应用程序安全性的适当 HTTP 头。

除了简单的主要用途(对于许多用例和 POC 项目来说已经足够了)，Talisman 还提供了高级的粒度设置来满足不同级别的安全需求。

更多信息和细节，请查看 Talisman [GitHub 页面](https://github.com/GoogleCloudPlatform/flask-talisman)和 [Mozilla 内容安全政策页面](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy)。

同时，享受为 Flask web 应用程序实现 Talisman 的乐趣！

*免责声明:web 应用程序安全没有灵丹妙药。Talisman 可以提供帮助，但是安全性不仅仅是设置几个标题。任何面向公众的 web 应用程序都应该有一个全面的安全方法。*

# 资源

*   [SSL —什么是 HTTPS](https://www.ssl.com/faqs/what-is-https/)
*   [烧瓶-talisman GitHub](https://github.com/GoogleCloudPlatform/flask-talisman)
*   [Mozilla —内容安全政策](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy)

```
**Want to Connect With the Author?**Check out my [GitHub](https://github.com/kennethleungty) to stay in the loop of more exciting tech and data science-related content.
```