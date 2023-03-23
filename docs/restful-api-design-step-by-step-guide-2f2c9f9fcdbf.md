# RESTful API 设计—逐步指南

> 原文：<https://betterprogramming.pub/restful-api-design-step-by-step-guide-2f2c9f9fcdbf>

## 构建更好的 API 的(某种程度上)权威指南

![](img/0d3c1a225e266c2392c236afc5ee4136.png)

马里乌斯·马萨拉尔在 [Unsplash](https://unsplash.com/search/photos/connectivity?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

作为软件开发人员，我们大多数人在日常生活中使用或构建 REST APIs。API 是系统之间的默认通信方式。Amazon 是 API 如何有效用于通信的最好例子。

在本文中，我将讨论如何更好地设计 RESTful APIs 以避免常见错误。

# “杰夫·贝索斯”(成功的关键)任务

你们中的一些人可能已经知道了[杰夫·贝索斯对亚马逊开发者的](https://en.wikipedia.org/wiki/Jeff_Bezos)命令。如果你从来没有机会听说过它，以下几点是它的症结所在。

1.  所有团队将从此通过服务接口公开他们的数据和功能。
2.  团队必须通过这些接口相互交流。
3.  不允许其他形式的进程间通信——不允许直接链接，不允许直接读取另一个团队的数据存储，不允许共享内存模型，不允许任何后门。唯一允许的通信是通过网络上的服务接口调用。
4.  他们用什么技术并不重要。HTTP、Corba、Pubsub、自定义协议——都没关系。贝佐斯不在乎。
5.  所有的服务接口，无一例外，都必须从头开始被设计成可外部化的。也就是说；团队必须计划和设计能够向外部世界的开发人员公开接口。没有例外。
6.  任何不这样做的人都会被解雇。

最终，这被证明是亚马逊成功的关键。亚马逊可以构建可扩展的系统，以后也可以像亚马逊网络服务一样提供这些服务。

# 设计 RESTful APIs 的原则

现在让我们理解设计 RESTful APIs 时应该遵循的原则。

## 保持简单

我们需要确保 API 的基本 URL 是简单的。例如，如果我们想为产品设计 API，它们应该设计成这样:

```
/products
/products/12345
```

第一个 API 是获取所有产品，第二个是获取特定产品。

## 使用名词而不是动词

很多开发人员都会犯这个错误。他们通常忘记了我们有 HTTP 方法来更好地描述 API，并最终在 API URLs 中使用动词。例如，获取所有产品的 API 应该是:

```
/products
```

并且如下图所示*不是*

```
/getAllProducts
```

目前为止我看到的一些常见的 URL 模式。

## 使用正确的 HTTP 方法

RESTful APIs 有各种方法来指示我们要用这个 API 执行的操作类型。

*   **GET** —获取一个资源或资源集合。
*   **POST** —创建资源或资源集合。
*   **上传/修补** —更新现有资源或资源集合。
*   **删除** —删除现有资源或资源集合。

我们需要确保对给定的操作使用正确的 HTTP 方法。

## 使用复数

这个话题有点值得商榷。有些人喜欢用复数名称保存资源 URL，而有些人喜欢用单数名称。比如说—

```
/products/product
```

我喜欢用复数形式，因为这样可以避免混淆我们是在讨论获取单个资源还是一个集合。它还避免了添加额外的东西，如将所有附加到基本 URL，如`/product/all`

有些人可能不喜欢这样，但我唯一的建议是在整个项目中保持统一。

## 使用参数

有时我们需要一个 API，它应该讲述更多的故事，而不仅仅是 id。这里我们应该利用查询参数来设计 API。

*   `/products?name=’ABC’`应该优先于`/getProductsByName`
*   `/products?type=’xyz’`应优先于`/getProductsByType`

通过这种方式，你可以在设计中简单地避免长 URL。

## 使用正确的 HTTP 代码

我们有大量的 HTTP 代码。我们大多数人最终只用了两个——200 和 500！这当然不是好的做法。以下是一些常用的 HTTP 代码。

*   **200 OK** —这是最常用的 HTTP 代码，用于显示所执行的操作是成功的。
*   **201 CREATED** —这可以在使用 POST 方法创建新资源时使用。
*   **202 已接受** —这可用于确认发送到服务器的请求。
*   **400 错误请求** —这可以在客户端输入验证失败时使用。
*   **401 未授权/ 403 禁止** —如果用户或系统未被授权执行某项操作，可使用此选项。
*   **404 未找到** —如果您正在寻找某个资源，而该资源在系统中不可用，则可以使用该选项。
*   **500 内部服务器错误** —这不应该被显式抛出，但如果系统出现故障，这种情况可能会发生。
*   **502 错误网关** —如果服务器从上游服务器接收到无效响应，可以使用此选项。

## 版本控制

API 的版本控制非常重要。许多不同的公司以不同的方式使用版本。有些使用版本作为日期，有些使用版本作为查询参数。我通常喜欢把它放在资源的前面。例如:

```
/v1/products
/v2/products
```

我也想避免使用`/v1.2/products`，因为它意味着 API 会频繁地变化。还有，点(。)可能不容易在 URL 中看到。所以保持简单。

保持向后兼容性总是一种好的做法，这样，如果您更改了 API 版本，消费者就有足够的时间转移到下一个版本。

## 使用分页

当您公开一个可能返回大量数据的 API 时，必须使用分页，如果没有进行适当的负载平衡，使用者最终可能会关闭服务。我们需要时刻记住，API 的设计应该是完全可靠的。

这里推荐使用`limit` 和`offset` 。比如`/products?limit=25&offset=50`。还建议保留默认限制和默认偏移。

## 支持的格式

选择 API 的响应方式也很重要。大多数现代应用程序应该返回 JSON 响应，除非您有一个仍然需要获得 XML 响应的遗留应用程序。

## 使用适当的错误信息

保留应用程序发送的一组错误消息并使用正确的 id 进行响应总是一种好的做法。例如，如果您使用脸书图形 API，在出现错误的情况下，它会返回如下消息:

```
{
  "error": {
    "message": "(#803) Some of the aliases you requested do not exist: products",
    "type": "OAuthException",
    "code": 803,
    "fbtrace_id": "FOXX2AhLh80"
  }
}
```

我也见过一些例子，人们返回一个带有错误信息的 URL，它告诉你关于错误信息的更多信息以及如何处理它。

## OpenAPI 规范的使用

为了让你公司的所有团队都遵守某些原则，使用 [OpenAPI 规范](https://swagger.io/resources/open-api/)会很有用。OpenAPI 允许你首先设计你的 API，并以一种更简单的方式与消费者分享。

## 结论

很明显，如果你想更好地交流，API 是必由之路。但是如果它们设计得很糟糕，那么可能会增加混乱。所以把你最大的努力投入到设计好，剩下的只是实现。

# 感谢您的阅读

如果你遇到了更好的 API 设计方法，请在评论区分享。欢迎所有反馈！

```
Hey, if you enjoyed this story, check out [Medium Membership](https://deshpandetanmay.medium.com/membership)! Just $5/month!*Your membership fee directly supports me and other writers you read. You’ll also get full access to every story on Medium.*
```