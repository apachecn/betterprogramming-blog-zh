# 在 iOS 中执行 GraphQL 查询的最简单方法

> 原文：<https://betterprogramming.pub/the-easy-way-to-perform-graphql-queries-in-ios-47b11ea2508e>

## 使用原始的 HTTP 请求很容易进行 GraphQL 查询

![](img/99e60075ec1b88f4d6afffdbb62e89dd.png)

Joshua Sortino 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了在 iOS 应用程序中与 GraphQL 服务器通信，有一些第三方库，如 Apollo iOS 客户端和 AWS AppSync。但是它们也给你的项目引入了不必要的代码。

例如，Apollo iOS 客户端通过使用 SQLite 数据库来规范化查询结果以构建客户端缓存。AWS AppSync 用一些额外的特性包装了 Apollo 客户端，以处理 AWS 身份验证。

当我的项目与第三方库绑定时，我总是感到犹豫。一些问题是:

*   图书馆的安全性如何？
*   如果维护者停止维护库怎么办？
*   维护人员修复一个问题并发布补丁版本需要多长时间？
*   你会告诉你的客户因为第三方的原因你不能解决问题吗？

幸运的是，有一种简单、轻量级的方法可以通过使用原始 HTTP 请求来执行 GraphQL。

本文演示了如何针对[星球大战 GraphQL API](https://swapi.graph.cool) 逐步构建它。

# 1.查询操作

![](img/8467ab70988ab556831fa24db59c65f9.png)

照片由[梅尔](https://unsplash.com/@melindapack?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

有三种类型的 GraphQL 操作:`query`、`mutation`和`subscription.`

`query`操作是从 API 中检索数据。根据 GraphQL API 的不同，可以通过`GET`或`POST` HTTP 方法来执行。

对于本文消耗的[星球大战 GraphQL API](https://swapi.graph.cool) ，使用的是`POST`方法。

以下是标准 GraphQL POST 请求的示例表单。该请求使用`application/json`作为`Content-type`。表单是它的 JSON 编码的主体。

杨梦的 GraphQL POST 请求的 JSON 编码的正文格式

让我们从`query`字符串开始。

![](img/6f81e6ecd11b8fb00a4ccf47316d81f2.png)

由[马腾·德克斯](https://unsplash.com/@maartendeckers?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 2.查询字符串

由杨梦查询正文字符串

*   它通过使用过滤器定义查询字符串来获取电影。
*   字符串中的关键字`query`定义了您打算做的操作类型。
*   `allFilms`是操作名称。这不是必需的，但是有意义且明确的名称可以帮助您识别操作。
*   `$filter`是查询接受的变量之一，后面是类型`Filter`。我们可以传递一个不同的变量，而不是在查询中构造它。
*   `fields`块描述了我们正在询问的特定字段，其形状与我们从 GraphQL API 中得到的相同。

![](img/ba397289f433f1aa12d7a72406773d4e.png)

吉尔·海耶在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 3.可编码变量

作为[星球大战 GraphQL API](https://swapi.graph.cool) 状态的文档，查询`allFilms`的扩展是:

星球大战图表 API 查询所有电影

它们都是可选的。出于演示的目的，我们在这里使用`Filter`。

杨梦的《身体的变量》

还有一些`Filter`的可选属性。我们只挑选`director`来简化演示代码。

`Filter`和`Variable`结构采用`Encodable`协议来准备 JSON 编码的主体。

![](img/f8d411fe5405285713bbc6e522509398.png)

照片由[萨曼莎·加德斯](https://unsplash.com/@srosinger3997?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 4.构建 HTTP 请求

让我们打破这一点:

*   结构`Payload`包含`variables`和`query`。它也采用`Encodable`协议。
*   `request`是用星球大战 API URL 构建的`URLRequest`的实例:[https://swapi . graph . cool](https://swapi.graph.cool)。
*   `payload`是 JSON 编码的，并被添加到`request`的主体中。
*   `POST`星球大战 GraphQL API 要求的方法被使用。
*   我们使用`application/json`作为 Http 头字段`Content-Type`和`Accept`的值。
*   `request` 只是用`URLSession`的共享实例触发。
*   响应的数据是 JSON 格式的，可以使用`JSONSerialization`进行反序列化。

![](img/ae844a6a623ebdd728aa1735242ec61b.png)

Anton Sukhinov 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

本文描述了与 GraphQL 服务进行通信的更轻量级的方法。通过使用原始 HTTP 请求，不需要与任何第三方库耦合。干净快捷的代码可以完全在开发者的控制之下。

在下一篇文章中，我将向您展示如何使用原始 HTTP 请求执行 GraphQL 变异。敬请期待！

![](img/b3a84da3ccff2a459e489e20d7a1b4d1.png)

照片由 [MusicFox Fx](https://unsplash.com/@musicfox?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 资源:

所有第三方库、GraphQL APIs 和样本 Swift 代码均可在以下链接中找到:

*   [Shopify GraphQL API](https://shopify.dev/concepts/shopify-introduction)
*   [Yelp GraphQL API](https://www.yelp.com/login?return_url=%2Fdevelopers%2Fgraphiql)
*   [阿波罗-iOS](https://github.com/apollographql/apollo-ios)
*   [AWS AppSync SDK iOS](https://github.com/awslabs/aws-mobile-appsync-sdk-ios/)
*   [GraphQL 查询操场](https://gist.github.com/ericleiyang/96c631134a1ccd161f5d32ffb895e2a6)