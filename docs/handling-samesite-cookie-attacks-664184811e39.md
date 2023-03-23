# 相同站点 Cookie 攻击

> 原文：<https://betterprogramming.pub/handling-samesite-cookie-attacks-664184811e39>

## 子域接管对您的 SameSite cookies 意味着什么

![](img/718c15859cc7e84ec10f3406bcd9795e.png)

照片由[帕韦尔·切温斯基](https://unsplash.com/@pawel_czerwinski?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

我不久前发表了一篇文章，讲述 Chrome 如何[让 SameSite 成为 cookies 的默认行为](https://vickieli.dev/csrf/csrf-updates/)以防止[跨站点请求伪造(CSRF)](https://vickieli.dev/csrf/csrf-intro/) 攻击。在那之后，jub0bs 联系了我，告诉我 SameSite 的细微差别是如何让网站变得脆弱的。感谢您让我关注这个问题！

在这篇文章中，我讨论了为什么我们可能被引导去相信 SameSite cookies 比它们实际上更安全，并澄清了我在上一篇文章中犯的一些错误。

本文基于 [jub0bs](https://medium.com/u/e22d4731320f?source=post_page-----664184811e39--------------------------------) 对该课题的研究。关于他对该话题的深入探讨，请到这里:[https://jub0bs . com/posts/2021-01-29-great-same site-confusion/](https://jub0bs.com/posts/2021-01-29-great-samesite-confusion/)。

## Chrome 的网站更新

当攻击者可以从外部域发起伪造的状态更改请求时，就会出现 CSRF 漏洞。通过实现 CSRF 令牌来确保请求的真实性，或者通过使用`SameSite`cookie 来防止 csrf。

大约两年前，Chrome 开始默认设置`SameSite` cookie 属性为`Lax`。此设置告诉客户端浏览器仅在相同站点请求和导致顶级导航的请求期间发送 cookie。

```
Set-Cookie: PHPSESSID=UEhQU0VTU0lE; SameSite=Lax
```

此默认设置有助于防止 CSRF 攻击。由于在跨站点请求期间不会发送敏感的 cookies，攻击者无法从他们的站点发起经过身份验证的请求。后来，其他几个浏览器效仿 Chrome，将`SameSite=Lax`设为默认设置。

然而，到底什么是同站点请求呢？我的第一直觉是，同站点请求是同源请求，这意味着两个页面具有[相同的协议、主机名和端口号](https://blog.shiftleft.io/what-is-the-same-origin-policy-f5e365adad7e)。这是一个错误的假设，具有重要的安全后果。

## SameSite！=相同来源

注意，cookie 属性被命名为`SameSite`而不是`SameOrigin`。

在这种情况下,“站点”的定义是可注册的域，加上 URL 方案。可注册域名定义为最具体的[公共后缀](https://publicsuffix.org/list/public_suffix_list.dat)，加上公共后缀之前的域名部分。

你可以看到`Origin`是一个比`Site`更严格的分类。因此，一个请求可能是同一个站点的，但却是跨源的。

您可以在本文中找到关于 SameSite cookies 的更深入的讨论:[https://web.dev/samesite-cookies-explained/](https://web.dev/samesite-cookies-explained/)。

## 跨来源、相同站点的请求

让我们看看为什么跨来源、相同站点的请求是可能的。首先，看一下这两个 URL:

```
https://foo.**example.com**
https://bar.**example.com** 
```

由于`example.com`不是公共后缀，所以用来定义这些产地的公共后缀是`.com`。站点被定义为“公共后缀加上公共后缀之前的域名部分”，所以这两个域名的站点都是`example.com`。这些页面之间的请求是跨源的，但是是同一站点的。

另一方面，`github.io`算作公共后缀。

```
https://**foo.github.io**
https://**bar.github.io**
```

这两个来源是不同的站点，因为`github.io`是一个公共后缀。“公后缀加一”变成了`foo.github.io`和`bar.github.io`。这些来源之间的请求是跨来源和跨站点的。

另外，请注意，所有这些请求都是 HTTPS。[只有当 URL 方案相同时，请求才是相同站点的](https://web.dev/schemeful-samesite/)。

## `SameSite` Cookie 限制

这种微妙的差异具有安全隐患。SameSite cookies 只能保护 CSRF 免受恶意跨站点请求的攻击，而不能保护同一站点、跨来源的请求。

例如，假设您将 cookies 设置为从`foo.example.com`到`SameSite=Lax`。如果攻击者在`bar.example.com`发现[子域接管](https://0xpatrik.com/subdomain-takeover-basics/)，他们可以从`bar.example.com`发送认证请求。

`foo.example.com`和`bar.example.com`是同一个站点，因为它们都属于`example.com`站点。子域上的 XSS 漏洞使得攻击者能够绕过相同站点的限制发起类似的攻击。

附注*:*`*SameSite*`*cookie 属性决定了哪些页面可以使用这些 cookie 发起请求。而* `[*Domain*](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie)` [*属性*](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie) *则限制了可以发送到的主机 cookies。*

```
**Want to Connect?**What other security concepts do you want to learn about? I’d love to know. Feel free to connect on Twitter [@vickieli7](https://twitter.com/vickieli7).
```