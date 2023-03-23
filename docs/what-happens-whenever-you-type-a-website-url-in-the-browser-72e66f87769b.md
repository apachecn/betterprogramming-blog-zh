# 每当您在浏览器中键入网站 URL 时会发生什么？

> 原文：<https://betterprogramming.pub/what-happens-whenever-you-type-a-website-url-in-the-browser-72e66f87769b>

## 了解网站 DNS 解析背后的逻辑，再也不会被逗乐了！

![](img/b05d9edc4135239b7c7a311059f2eef7.png)

格伦·卡斯滕斯-彼得斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

所以，每当我们在浏览器上输入任何 URL，你有没有想过实际上会发生什么？尽管我们已经习惯了这样一个事实，即无论何时我们想在谷歌上搜索任何东西，我们都可以通过键入 google.com 来实现，我们可以寻找我们的查询。

现在问题来了，这到底意味着什么？如果我们分析 google.com 的 URL，结果是 Google + com，所以我们可以说 Google 是一个唯一的标识符，但是 com 到底是什么？我们通常会遇到一千个其他的应用程序。com 扩展名。不要担心，这篇文章会给我们一个很好的关于解析 URL 的含义的想法。

只是给你一个概述，整个网址被称为域名，这让我们很容易记住网站的地址。让我们详细讨论每件事。所以请坐好，放松，准备阅读整篇文章。

```
**Table of content**
· [How DNS works?](#6b6d)
· [How DNS is resolved?](#f8b5)
· [Why DNS Resolution is quick?](#8c63)
· [Top Level Domain: Bit more](#00d5)
· [What the heck is .xyz?](#8bfd)
· [Final Thoughts](#52c4)
· [References](#be03)
```

# DNS 是如何工作的？

DNS 代表域名系统，可以说是互联网的主干。像 www.google.com[、](https://www.google.com,)、[、www.medium.com](http://www.medium.com,)、[www.facebook.com](http://www.facebook.com)这样的网站称为域名，以达到这些网站的内容。

![](img/383c24b31f5cb9037cc408cdac0ecf59.png)

托马斯·詹森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

实际上，在后台，它基于互联网协议地址(通常称为 IP 地址)运行，这些地址是数字的复杂组合，通常是 IPv4 (10.123.123.1x)或 IPv6。每台服务器都有一个唯一的 IP 地址，我们只需在 Google Chrome 或 Firefox 等浏览器上输入这个地址就可以搜索服务器，或者创建一个到服务器的 ssh 连接。

因此，每当我们将服务器的 IP 地址注册到域名(例如，将 1.2.3.4 映射到[www.apoorv.com)](http://www.apoorv.com))时，就会在顶级域名级别维护一个条目，即。点击这里。每当有人试图在他们的浏览器上点击我们的域名时，它将被用来映射 DNS 和 IP 地址。

整个过程中涉及到各种服务器。从发出请求到显示结果的延迟是如此之低，以至于我们没有观察到任何延迟。

# DNS 是如何解析的？

每当我们在浏览器中输入域名时，都会发生一系列的跳转。最终目标是找到部署我们的应用程序源代码的服务器的 IP 地址。

把它当成一个超市，所以当你问服务台在哪里可以找到特定品牌的香水时，助理会指引你去香水专柜。之后，你询问具体的品牌柜台，在那里你可以找到你要找的香水。

类似的活动发生在 DNS 上:我们的请求发送到根 DNS 名称服务器，根 DNS 名称服务器将我们的请求重定向到 TLD 服务器(abc.com-> com 是顶级域名/TLD 服务器)。

然后，TLD 服务器以与所请求的域相链接的 IP 地址作为响应。这个特定的 IP 地址将负责呈现应用程序。

# 为什么 DNS 解析很快？

请记住，DNS 解析是快速的，但是您仍然需要改进往返，直到 TLD 解析缓存实现。一般来说，缓存是一种用于缓存特定数据的机制，它将确保数据被快速提供。换句话说，数据是根据请求提供的输入来记忆的。

类似地，浏览器/操作系统为任何特定的请求做这项工作。因为他们维护着一个字典，根据域名存储 IP 地址。如前所述，这有助于减少到顶级域服务器的往返行程。减少了等待时间。我们可以设置一个 TTL(即生存时间),在此之后缓存将被清除。

你可以在谷歌 chrome 浏览器中检查 chrome://net-internals/#dns，你也可以在那里清除你的 dns 缓存。

# 关于顶级域名的更多信息

[互联网名称与数字地址分配机构(ICANN)](https://www.icann.org/resources/pages/welcome-2012-02-25-en) ，是负责维护整个互联网标准的非营利性组织。

![](img/6880da7c58d4905360a1d24de5a79505.png)

[克瓦利菲克](https://unsplash.com/@kvalifik?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

所以，顶级域名是你整个域名的延伸，比如 abc.org，等等。org 是 TLD。其他受欢迎的顶级域名如下:

*   。这些是政府机构使用的。
*   **。这些 TLD 被组织使用。**
*   **。com** :的。com 被广泛使用，它们被用于商业目的。您遇到的大多数网站都使用。com 扩展名。
*   **。net** :这个 TLD 是网络用的。
*   **。博客**:。博客是博客网站的 TLD。

还有其他各种选择，比如。商业。我等等。但是上面那些都是比较受欢迎的。

# 到底是什么？Xyz？

在各种网站上，你可以找到顶级域名(即 xyz ),它现在变得非常流行。该顶级域名于 2014 年开始公开使用。xyz 这个名字背后的理论是，它似乎代表 X、Y 和 Z 代。

由于一些高级域名有点贵，xyz 成为了一个更便宜的选择。购买这种 TLD 大约是每年 0.99 美分，定期更新也要收取类似的费用。

Daniel Negari 是 xyz 对服务器用户可用性的幕后推手。他声称这是一个竞争性的选择和市场创新。

关于 xyz 的一些统计数据包括:

*   2014 年 6 月 2 日:TLD 向公众开放。
*   2015 年 11 月。xyz 的域名注册量达到了 150 万。
*   2016 年 1 月:成为第六大注册 TLD。
*   2016 年 6 月。xyz 攀升至全球顶级域名注册最多的第四位。

有趣的事实: **Alphabet，谷歌的母公司**，使用域名 abc.xyz，使其成为使用 TLD 的最大公司之一。

根据 XYZ.com 首席执行官的说法，由于域名空间混乱，他的目标是为用户提供更多选择。他的主要目标是让 xyz 成为用户注册任何博客、商业网站等的首选。

自从。com TLD 已经被占用，无论何时任何人计划购买一个特定名称的新域名，它已经被占用。因此，按照他的说法，是时候重置互联网，允许用户购买他们选择的域名了。

# 最后的想法

因此，即使你是一名软件开发人员或互联网的普通用户，你也应该了解域名是如何工作的。如果我们用一般的术语来说，代码部署在一个服务器上，这个服务器有一个唯一的 IP 地址。

所有的网站，像 facebook.com、medium.com、google.com 等。，都有一些唯一的 IP 地址与之相关联，就像我们的电话号码。而且很难记住。把它与记住你新朋友的名字和记住他们的联系电话的斗争相比较。当然，他们的名字更容易掌握。

只是为了让我们的生活更容易，我们有一个域名注册的 IP 地址。阅读本文后，您已经了解了域名的定义、不同的顶级域和 DNS 解析步骤。现在，我们有足够的信心理解域名，如果有人问我们，我们将能够解释其内部工作。

为了让更多热心的人了解这个话题，我添加了一些参考资料。

# 参考

1.  [https://www.youtube.com/watch?v=JMBvSprSTeI](https://www.youtube.com/watch?v=JMBvSprSTeI)
2.  https://en.wikipedia.org/wiki/.xyz
3.  【https://www.cloudflare.com/en-in/learning/dns/what-is-dns/ 
4.  [https://www . dream host . com/academy/understanding-different-species-of-domains/](https://www.dreamhost.com/academy/understanding-different-kinds-of-domains/)

```
**Want to Connect With the Author?**Subscribe to my [newsletter](https://www.mindroast.com/newsletter) for the latest curated content.
```