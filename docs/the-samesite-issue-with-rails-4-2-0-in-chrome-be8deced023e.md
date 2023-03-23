# Chrome 中 Rails 4.2.0 的“相同站点”问题

> 原文：<https://betterprogramming.pub/the-samesite-issue-with-rails-4-2-0-in-chrome-be8deced023e>

## 带有“SameSite=None”的跨站点请求

![](img/c54a18289619661f61495f9ec130d682.png)

照片由[韦斯·希克斯](https://unsplash.com/@sickhews?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/collections/9969679/technology?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 什么是‘T0’？

`[SameSite](https://web.dev/samesite-cookies-explained/)`是新推荐的保护网站安全的方法。这会让`CSRF`更加安全。`SameSite`属性允许服务器断言 cookie 不应该与跨站点请求一起发送。这种变化提供了一些针对跨站点请求伪造攻击的保护。

简单来说，不包含`SameSite=None`和`Secure`标签的 cookies 将无法被第三方访问。这种行为改变是谷歌于 2020 年 2 月 4 日*推出的[。](https://www.chromestatus.com/feature/5088147346030592)*

让我们来理解 Rails 应用程序中的场景。如果您的 Rails 应用程序提供了使用 cookie 进行身份验证的 API，那么 cookie 的这种新行为将打破这种局面。因为现在`SameSite`的默认值是`Lax`，它不会将 cookies 发送回第三方网站。

# 在 Rails 4.2.0 中，你如何解决这个问题？

为了解决 Rails 中的这个问题，我们需要用`SameSite=None`显式地设置 cookies。为了设置`SameSite=None`，我们需要在 Rails 应用程序的`config/intializers`中创建一个文件`rack.rb`。

如果您想将它附加到应用程序 cookie 密钥，那么您需要用以下代码更新`rack.rb`文件:

您需要用您的应用密钥替换`key name`。在哪里可以找到这个应用程序 cookie 密钥名称？您将从`session_store.rb`文件中获得这个 cookie 密钥名。

就是这样！您的应用程序将在 Chrome 中完美运行。干杯！

# **参考文献**

*   [“WTF 是 Chrome 的 SameSite cookie 更新？”通过 Digiday](https://digiday.com/media/what-is-chrome-samesite/)
*   [“same site cookies 解释”通过 web.dev](https://web.dev/samesite-cookies-explained/)
*   [“使用同站点 Cookie 属性防止 CSRF 攻击”通过 Netsparker](https://www.netsparker.com/blog/web-security/same-site-cookie-attribute-prevent-cross-site-request-forgery/)