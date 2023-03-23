# MEAN 堆栈中的身份验证和 JSON Web Token，从 Angular 到 Node.js

> 原文：<https://betterprogramming.pub/jwt-json-web-token-in-the-mean-stack-b17c729ac3de>

![](img/99dbc35cf81e7714ea19173072f7a874.png)

[JSON Web Token](https://jwt.io/) 是在 [MEAN](http://meanjs.org/) 栈中实现认证模式的最佳标准。

编写服务器端和客户端逻辑非常容易，但是当您的目标是拥有完整的 RBAC(基于角色的访问控制)身份验证模式时，这可能会有点棘手，因为我们希望保护路由不被不合格的角色访问。

**JWT 是认真的**。它不是最佳实践，也不是建议，更不是口碑。它是真正的 RFC 标准，由 IETF: RFC 7519 定义。

JWT 实现需要服务器端逻辑来签名和生成令牌，客户端逻辑来存储令牌并将其附加到从客户端到 web 服务器的每次通信(通常是 Ajax)中。

## 什么是 JSON Web 令牌？

它是一个普通的字符串，仅此而已，由以下内容组成:

1.  **一个头**，它保存了关于用于签名的算法(HMAC SHA256 或 RSA)和其他元的信息。
2.  **一个有效载荷**，通常是有趣的部分，它包含我们想要存储在令牌中的信息。
3.  **摘要**，这是 JWT 的最后一部分，使其安全且防篡改。

![](img/2d42900f4c0c842c3fa321d5f261754a.png)

JSON Web 令牌示例:头(红色)、有效负载(紫色)和摘要(青色)

请注意，JWT 没有加密，它只是被签名以防止摘要被篡改，因此它总是可以被客户端和服务器端读取(甚至被入侵者读取……)。**切勿**在令牌中存储敏感数据。

没有真正的设计模式来实现 JWT，只有网络上关于如何编码的好主意。这就是为什么我不会花时间在线上评论如何实现它，我只会显示代码片段来签名并通过它进行交流。

# 服务器端部分:创建一个 JWT

这里是一个 JWT 创建和签名实现的[片段，使用 Node.js 和`auth0`节点模块](https://github.com/auth0/node-jsonwebtoken)

“sign”方法的第一个参数是需要放入有效载荷(然后放入令牌本身)的信息。

第二个参数是用于创建摘要的密钥。

第三是期权表示。在本例中，我将令牌的到期日期设置为七天。

# 服务器端部分:检查和验证 JWT

这里是另一个 Node.js 片段，用 [Express.js](https://expressjs.com/) 中间件实现，它拦截所有请求并检查 JWT 的存在。

我们正在使用一个常见的实现，它期望将 JWT 传递到 HTTP 头“authorization”键中。

作为验证例程的结果，如果令牌过期，我们将发送 HTTP 401 Unauthorized，如果报头中缺少令牌，我们将发送 HTTP 400 Bad 请求。

# 客户端部分:通过 AJAX 通信，使用角度拦截器添加 JWT

在这个代码片段中，我们使用了一个[角度拦截器](https://angular.io/api/common/http/HttpInterceptor)，这是一个将令牌附加到每个 HTTP 请求的好方法。

有一些关于如何使用拦截器的注释，所有的注释都是依赖于应用程序上下文的，所以把这个片段看作是一个关于如何添加它的例子。

如您所见，我们使用了带有“Bearer”前缀的名为“authorization”的 HTTP 头，正如服务器所期望的那样。

在本例中，我们在本地存储中存储和读取令牌。像以前一样，这只是一个想法，你可能更喜欢一个`SessionStorage`或其他东西。

# 结论

JSON Web Token 得到了很好的支持。在官方网站的[上，你可以找到几乎任何语言的库和工具来使用它，从](https://jwt.io) [Python](https://www.python.org/) 到。NET apps，以及 [Java](https://www.java.com/) 到 Node.js。

您将需要几个小时来实现一个健壮的身份验证方法，但这绝对是值得的。