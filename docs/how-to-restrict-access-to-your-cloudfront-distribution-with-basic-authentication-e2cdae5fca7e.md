# 如何使用基本身份验证限制对您的 CloudFront 发行版的访问

> 原文：<https://betterprogramming.pub/how-to-restrict-access-to-your-cloudfront-distribution-with-basic-authentication-e2cdae5fca7e>

## 使用 AWS Lambda@Edge

![](img/65344c5bfb2be78c4499ab099b6ed8d9.png)

照片由[沙哈达特·拉赫曼](https://unsplash.com/@hishahadat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄。

在开发 web 应用程序时，为了获得更好的性能，您可能会决定通过 CDN 提供登录页面和每个静态文件。CloudFront 是由 AWS 提供的 CDN，它允许你从不同的来源提供内容，这些来源被称为源，如 S3 或负载平衡器。应用程序的静态文件或动态数据将通过这些来源提供给用户。

# 这些问题

但是在应用程序的开发过程中，您需要在 AWS 上托管。除了在 AWS 上试验不同的服务，您还需要创建一个只能由特定用户访问的测试环境。

除此之外，正如您可能知道的，Google 会不断地抓取 web 并为用户搜索查询索引网站，所以您可能不希望您的暂存环境被 Google 索引，从而对全世界开放。

# 解决方案

在这种情况下，您需要一种方法来保护通过 CDN 提供的内容，您可以通过一种称为基本身份验证的身份验证方法来实现这一点。它是 HTTP 协议中内置的一种身份验证方案，允许用户通过发送带有特殊的`Authorization`报头的请求来访问受保护的内容，该报头包含用于登录的用户名和密码的`base64`编码版本。

下面提供了一个例子:

```
Authorization: Basic ZGVtbzpwQDU1dzByZA==. 
```

这是当您尝试访问基本授权保护的源时得到的授权对话框:

![](img/ef13f0b1d3276aad608a08d4793405dc.png)

# 输入λ@ Edge

没有直接在 CloudFront 上应用基本授权的直接方法。但是，这可以通过利用 Lambda@Edge 来实现，该特性允许您通过添加不同的头或者检查其他请求头的存在和有效性，在请求之前或响应 CloudFront 发行版之后执行逻辑。

在我们的例子中，您需要的是:“检查对分发的请求是否包含`Authorization`头，如果包含，检查`base64`编码值是否与编码的用户名-密码组合相同。”

下图说明了整个请求生命周期:

![](img/72b91e40c660c513615bad6b04c627ec.png)

一旦向 CloudFront 分发端点发出请求，Lambda@Edge 将尝试调用 Lambda 函数来分析请求，提取`Authorization`头，并尝试将头的值与使用`base64`编码的预定义用户名-密码组合进行匹配。

如果头验证成功，请求就会通过——要么从缓存中返回数据，要么从源中检索数据并为其提供服务。否则，它返回一个状态代码为 401 的`Unauthorized`响应。

# λ授权功能

要实现这一点，首先需要创建一个`Lambda`函数，如下所示:

为了简单起见，您可以定义用户名和密码，并将其存储在内存中。但是如果您希望有多个用户可以连接到这个发行版，您可以将`Lambda`函数连接到 DynamoDB，在那里您可以存储用户及其密码。

主要的逻辑存在于头验证中，在这里验证`Authorization`头是否存在，以及它的值是否与`basicAuthentication`值匹配。`basicAuthentication`是计算完用户名-密码组合的`base64`编码值后存储的变量。

如果检查失败，`Lambda`通过附加`www-authenticate`头返回一个定制的`401 Unauthorized`响应，通知客户端所需的认证方法的类型。

第二步是允许这个函数在每次请求分发时被触发。您需要发布您的`Lambda`功能的新版本(操作-`Lambda`菜单上的>发布新版本)并复制`Lambda ARN`及其版本。`Lambda ARN`应该是这样的:

```
arn:aws:lambda:us-east-1:ACCOUNT_NUMBER:function:basic_auth:1
```

然后，您需要通过将`Lambda`函数与查看器请求相关联来编辑您的 CloudFront 发行版的行为，如下图所示:

![](img/63d2943fa19e4141235654a49d464e34.png)

这个行为将指示 Lambda@Edge 为每个对分发的请求触发`Lambda`函数。

# 角色信任策略

然而，Lambda@Edge 在默认情况下不能触发该函数，因为它没有任何权限这样做。当您创建一个`Lambda`函数时，系统会要求您为`Lambda`创建一个执行角色或者使用一个现有的角色。该角色定义了可以执行该功能的主体。默认的执行角色可以由`Lambda`服务来承担，并且具有将日志写入 CloudWatch 的简单权限。

在我们的例子中，您需要 Lambda@Edge 来承担这个角色并执行这个函数。因此，您需要为该执行角色指定以下信任策略:

```
{ "Version": "2012-10-17", "Statement": [{ "Effect": "Allow", "Principal": { "Service": [ "lambda.amazonaws.com", "edgelambda.amazonaws.com" ] }, "Action": "sts:AssumeRole" }]}
```

有了信任策略，您的发行版的所有内容都设置为只有受信任的用户才能访问。

当您想要执行以下操作时，可以对网站或应用程序应用基本身份验证:

*   限制某些用户的访问。
*   限制对试运行或测试环境的访问。
*   保护你的网站不被谷歌索引。

# 结论

感谢您阅读这篇文章。我希望您发现在您的项目中实现它是有用的。