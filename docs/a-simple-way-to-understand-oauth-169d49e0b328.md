# 理解 OAuth 的简单方法

> 原文：<https://betterprogramming.pub/a-simple-way-to-understand-oauth-169d49e0b328>

## 当你使用你最喜欢的服务登录时会发生什么？

![](img/18c5005065b93d132169f29a9fca3882.png)

汤姆·陈在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

OAuth 是一个开放标准的授权协议或框架，它为应用程序提供了“安全指定访问”的能力例如，你可以告诉脸书，Medium.com 可以访问你的个人资料或更新你的时间表，而不必向媒体提供你的脸书密码。这在很大程度上最小化了风险:如果介质遭到破坏，您的脸书密码仍然是安全的。

OAuth 不共享密码数据，而是使用授权令牌来证明消费者和服务提供商之间的身份。OAuth 是一种身份验证协议，允许您批准一个应用程序代表您与另一个应用程序进行交互，而无需泄露您的密码。

# SAML 对 OAuth

[SAML(安全断言标记语言)](https://www.varonis.com/blog/what-is-saml/)是一种替代的联合身份验证标准，许多企业将其用于单点登录(SSO)。SAML 使企业能够监控谁有权访问公司资源。

SAML 和 OAuth 有很多不同之处。SAML 使用 XML 传递消息，OAuth 使用 JSON。OAuth 提供了更简单的移动体验，而 SAML 则面向企业安全。最后一点是一个关键的区别点:OAuth 广泛使用 API 调用，这就是为什么移动应用程序、现代 web 应用程序、游戏控制台和物联网(IoT)设备发现 OAuth 为用户提供了更好的体验。另一方面，SAML 在浏览器中放置一个会话 cookie，允许用户访问某些网页——对于短暂的工作日来说很好，但对于每天都必须登录恒温器的情况来说就不那么好了。

## OAuth 示例

![](img/8ca5124b79dc0e54f4158399eb79af0a.png)

照片由[30 天回放](https://unsplash.com/@30daysreplay?utm_source=medium&utm_medium=referral)上的 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

OAuth 最简单的例子是一个网站说“嘿，你想用其他网站的登录信息登录我们的网站吗？”在这种情况下，第一个网站——让我们将该网站称为*消费者*——想要知道的唯一事情是，用户在两个网站上都是相同的用户，并且已经成功登录到服务提供商——这是用户最初登录的网站，而不是消费者。

脸书应用程序是一个很好的 OAuth 用例示例。假设你正在使用脸书上的一款应用，它会要求你分享你的个人资料和照片。在这种情况下，脸书是服务提供商:它有你的登录数据和照片。应用程序是消费者，作为用户，你想用应用程序来处理你的图片。你特别授权这个应用程序访问你的照片，OAuth 在后台管理这些照片。

你的智能家居设备——烤面包机、恒温器、安全系统等。—可能使用某种登录数据来彼此同步，并允许您从浏览器或客户端设备管理它们。这些设备使用 OAuth 所谓的*机密授权*。这意味着他们持有密钥信息，所以你不必一次又一次地登录。

## OAuth 解释道

OAuth 是关于授权而不是认证的。授权是请求允许做某事。认证就是证明你是正确的人，因为你知道事情。OAuth 不在消费者和服务提供商之间传递认证数据，而是充当某种授权令牌。

在研究 OAuth 时，我见过的一个常见的类比是你汽车的代客钥匙。代客钥匙允许代客启动和移动汽车，但不能让他们进入行李箱或手套箱。

OAuth 令牌就像是代客钥匙。作为一个用户，你可以告诉消费者他们可以使用和不可以使用每个服务提供商提供的服务。您可以给每位消费者一把不同的代客钥匙。他们从来没有完整的密钥或任何可以让他们访问完整密钥的私有数据。

# OAuth 如何工作

![](img/1b9606a74fe56a7d3f64269ce9bfdb55.png)

[斯科特·格雷厄姆](https://unsplash.com/@sctgrhm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

OAuth 事务中有三个主要角色:用户、消费者和服务提供者。这三人组被亲切地认为是奥特三角恋爱。

在我们的例子中，卫莱是用户，比特里是消费者，Twitter 是控制卫莱的安全资源(他的 Twitter 流)的服务提供者。卫莱希望比特能够在他的信息流中发布缩短的链接。它是这样工作的:

## **步骤 1:用户表明意图**

*   **卫莱(用户):**“嘿，比特，我希望你能把链接直接发到我的推特上。”
*   **Bitly(消费者):**“太好了！让我去征求许可。”

## **第二步:消费者获得许可**

*   **Bitly:** “我有一个用户希望我发布到他的流中。能给我一个请求令牌吗？”
*   **推特(服务商):**“当然。这里有一个令牌和一个秘密。”

该秘密用于防止伪造请求。消费者使用这个秘密对每个请求进行签名，这样服务提供者就可以验证它确实来自消费者应用程序。

## **步骤 3:用户被重定向到服务提供商**

*   **小点声:**“好的，卫莱。我把你发到推特上，这样你就可以批准了。你带着这个信物。”
*   卫莱:“好的！”

***Bitly 指示卫莱*** ***到 Twitter 进行授权。***

这是可怕的部分。如果 Bitly 是超级黑幕邪恶公司，它会弹出一个看起来像 Twitter 的窗口，但实际上是对你的用户名和密码的网络钓鱼。始终确保验证您被定向到的 URL 实际上是服务提供商(在这种情况下是 Twitter)。

## **第四步——用户给予许可**

*   卫莱:“推特，我想授权比特利给我的这个请求令牌。”
*   **Twitter:** “好吧，只是为了确定一下，你想授权 Bitly 用你的 Twitter 账号做 X，Y，Z 的事？”
*   卫莱:“是的！”
*   Twitter: “好的，你可以回去告诉 Bitly，他们有权限使用他们的请求令牌。”

Twitter 将请求令牌标记为“可以使用”，因此当消费者请求访问时，它将被接受(只要它是使用他们的共享秘密签名的)。

## **步骤 5:消费者获得访问令牌**

*   Bitly:“Twitter，我能把这个请求令牌换成访问令牌吗？”
*   **推特:**“当然。这是您的访问令牌和密码。”

## **步骤 6:消费者访问受保护的资源**

*   “我想把这个链接贴到卫莱的博客上。这是我的访问令牌！”
*   **推特:**“搞定！”

在我们的场景中，卫莱从来不需要与比特分享他的 Twitter 证书。他只是以一种安全的方式使用 OAuth 委托访问。卫莱可以在任何时候登录 Twitter，查看他授予的访问权限，并在不影响其他人的情况下撤销特定应用程序的令牌。OAuth 还允许细粒度的权限级别。你可以给 Bitly 向你的 Twitter 账户发帖的权利，但是把 LinkedIn 限制为只读访问。

# OAuth 1.0 与 OAuth 2.0

OAuth 2.0 是 OAuth 1.0 的完全重新设计，两者不兼容。如果您现在创建一个新的应用程序，请使用 OAuth 2.0。这个博客只适用于 OAuth 2.0，因为 OAuth 1.0 已经过时了。

OAuth 2.0 实现起来更快更容易。OAuth 1.0 使用复杂的加密要求，只支持三个流，并且不可伸缩。

另一方面，OAuth 2.0 对于不同类型的应用程序和要求有六个流，并允许通过 HTTPS 签署秘密。在 2.0 中，OAuth 令牌不再需要在端点上加密，因为它们是在传输过程中加密的。

我想现在你对 OAuth 有所了解了。我希望你觉得这篇文章有趣。