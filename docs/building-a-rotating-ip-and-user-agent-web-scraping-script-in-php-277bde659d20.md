# 用 PHP 构建一个旋转的 IP 和用户代理 Web 抓取脚本

> 原文：<https://betterprogramming.pub/building-a-rotating-ip-and-user-agent-web-scraping-script-in-php-277bde659d20>

## 强大、先进、现代的网络抓取

![](img/a72da05439cbbbb041b7f1c6ba16a2fa.png)

李·坎贝尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

# 旋转用户代理

> “用户代理[请求头](https://developer.mozilla.org/en-US/docs/Glossary/request_header)是一个特征字符串，它让服务器和网络对等点识别应用程序、操作系统、供应商和/或发出请求的[用户代理](https://developer.mozilla.org/en-US/docs/Glossary/user_agent)的版本。”― [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)

为了达到这个目标，我们将从包含有效用户代理字符串列表的文件中随机选择一个有效的用户代理。

首先，我们需要[得到这样一个文件](https://user-agents.net/download)。其次，我们必须阅读它并随机抽取一行。这可以通过以下函数实现:

# 旋转出口仪表板

为了实现 IP 轮换，我们将使用代理服务器。

> “代理服务器基本上是另一台计算机，作为处理 internet 请求的中枢。通过连接这些服务器中的一个，你的计算机将你的请求发送到服务器，然后服务器处理你的请求并返回你想要的。此外，通过这种方式，它可以充当您的家用机器和互联网上其他计算机之间的中介。”― [我的 IP 是什么？](https://www.whatismyip.com/what-is-a-proxy/)

使用代理时，我们发出请求的网站看到的是代理服务器的 IP 地址，而不是我们的。这使我们能够匿名抓取目标网站，而没有被禁止或阻止的风险。

使用单一代理意味着 IP 服务器可以被禁止，中断我们的脚本。为了避免这种情况，我们需要构建一个代理池来路由我们的请求。相反，我们将使用 [Tor](https://www.torproject.org/) 代理。如果你不熟悉 Tor，强烈推荐阅读下面这篇文章:[Tor 到底是怎么工作的？](https://skerritt.blog/how-does-tor-really-work/)

> “Tor 会将您的流量通过至少 3 台不同的服务器，然后再发送到目的地。因为三个中继中的每一个都有单独的加密层，所以监视你的互联网连接的人不能修改或读取你发送到 Tor 网络的内容。你的流量在 Tor 客户端(在你的电脑上)和世界其他地方之间是加密的。”— [Tor 的官方文件](https://support.torproject.org/about/how-is-tor-different-from-other-proxies/)

首先，我们需要设置 Tor 代理。强烈建议遵循以下基于操作系统的指南:

*   Windows → [如何在 Windows 上安装 Tor 并创建 Tor 隐藏服务](https://miloserdov.org/?p=1839)
*   macOS → [如何在 macOS 上安装 Tor 教程](https://deepdarkweb.github.io/how-to-install-tor-on-macos-tutorial/)
*   Linux → [在 Linux 中设置 Tor 代理和隐藏服务](https://www.devdungeon.com/content/setting-tor-proxy-and-hidden-services-linux)

现在，我们有一个 Tor 服务监听端口 9050 上的 [SOCKS4 或 SOCKS5](https://surfshark.com/blog/socks-proxy) 连接。这项服务在启动时以及 Tor 认为将来或现在可能需要更多时创建一个电路。

一个电路每次第一次使用，都会被标记为*脏*。默认情况下，脏电路使用十分钟。为了将这个时间间隔减少到允许的最低值(10 秒)，我们需要在我们的`torrc`配置文件中添加这一行:

```
[MaxCircuitDirtiness](https://2019.www.torproject.org/docs/tor-manual-dev.html.en) 10
```

# **使网页抓取脚本健壮**

我们刚刚看到了如何使我们的请求看起来随机，但这可能还不够。我们的请求仍然可能被拒绝，为了防止这种情况发生，我们需要实现一个重试逻辑。每次尝试失败后，我们将调用 [sleep()](https://www.php.net/manual/en/function.sleep.php) 函数等待几秒钟。这样，我们应该防止同样的错误再次发生。事实上，在这段时间内可能会产生一个新的电路。这将使我们的脚本更加健壮，并且可以通过以下方式轻松实现:

# 把所有的放在一起

我们现在要展示一个工作示例，它的目标是通过国家和地区搜索[新冠肺炎·疫情](https://en.wikipedia.org/wiki/COVID-19_pandemic_by_country_and_territory)维基百科页面来检索新冠肺炎的统计数据，并将它们保存在一个. csv 文件中。我们还没有提到如何解析 HTML 页面来检索所需的数据。这可以通过利用特定的库来实现。我们使用了用于 PHP 的[简单 HTML Dom 解析器](https://github.com/voku/simple_html_dom)，它可以通过[Composer](https://getcomposer.org/) 安装，代码如下:

```
composer require voku/simple_html_dom
```

这是工作示例的完整代码:

# 进一步阅读

[](https://codeburst.io/how-to-build-an-api-to-perform-web-scraping-in-spring-boot-e8bfaaa4622e) [## 如何在 Spring Boot 建立一个 API 来执行网页抓取

### 动态抓取网页以检索公共数据

codeburst.io](https://codeburst.io/how-to-build-an-api-to-perform-web-scraping-in-spring-boot-e8bfaaa4622e) [](https://levelup.gitconnected.com/web-scraping-made-simple-with-octoparse-9a966d888414) [## Octoparse 简化了网页抓取

### 轻松提取数据并将其存储在 Google Sheets 文档中

levelup.gitconnected.com](https://levelup.gitconnected.com/web-scraping-made-simple-with-octoparse-9a966d888414) 

# 结论

他的文章的源代码可以在我的 [GitHub 资源库](https://github.com/Tonel/web-scraping)中找到。

[](https://github.com/Tonel/web-scraping) [## 托内尔/网络抓取

### https://medium . com/@ antozanini/building-a-rotating-IP-and-user-agent-web-scraping-script-in-PHP-277 bde 659d 20…

github.com](https://github.com/Tonel/web-scraping) 

就这些了，伙计们！我希望这能帮助你用 PHP 构建一个旋转的 IP 和用户代理 web 抓取脚本。

如果你有任何意见或建议，请告诉我。