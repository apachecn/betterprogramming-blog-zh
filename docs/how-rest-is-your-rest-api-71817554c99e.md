# 你的 REST API 怎么样？

> 原文：<https://betterprogramming.pub/how-rest-is-your-rest-api-71817554c99e>

## Richardson 成熟度模型认可 REST APIs 的四个阶段

![](img/93c31b7be1d01f64d34ff2d82a89825f.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com/s/photos/growing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

大多数软件应用程序都有某种 API。我们需要一种与其他系统交流的方法。

最流行的解决方案之一是 REST API。根据开发人员的经验和系统需求，REST API 可以通过几种不同的方式实现。思考 API 设计以及如何充分利用它是值得的。

REST 代表代表性状态转移。我们可以将数据传输到另一个系统，并期望以无状态的方式接收响应。它允许多个系统之间通过网络进行统一通信。

我们知道将我们的请求发送到哪里，以及会得到什么样的回应。REST 也是技术不可知的。通过 REST 进行通信的应用程序彼此之间只需要知道很少的信息。他们不必关心细节，API 接口就足够了。

REST APIs 有四个成熟度级别。该模型由 Leonard Richardson 创建，描述了 REST 标准的实现情况。

# 0 级

这不是推荐的实现，而且相当麻烦。我们没有利用休息的好处。我们直接告知应该执行哪个操作，并将我们的请求发送到单个端点。它是一种远程过程执行。

我们需要获得一个 ID 为`“1234”`的植物。如果我们正在处理一个 0 级 REST API，我们将创建一个类似下面的调用。

```
POST /plantsService HTTP/1.1{
   "method": "fetch",
   "params": {
       "id": "1234"
    }
}
```

作为回报，我们会收到预期的信息或错误。

```
HTTP/1.1 200 (OK){
    "result": {
        "id": "1234",
        "name": "Fern",
        "color": "Blue"    
    },
    "error": null,
}
```

# 一级

我们不会对每个呼叫都使用一个端点。根据不同的用例，资源可以在不同的路径下使用。

我们可以打电话给`/plants`列出所有可用的工厂，打电话给`/plants/1234`获得单个工厂的详细信息，或者打电话给`/plants/new`添加新产品。

如果我们想添加一棵具有给定名称和颜色的新植物，我们可以发出以下请求。

```
POST /plants/new HTTP/1.1{
   "name": "Fern",
   "color": "Green"
}
```

作为回报，我们会收到确认或错误。

```
HTTP/1.1 201 (CREATED){
    "id": "12345"
}
```

由于我们使用不同的端点来获取和修改数据，我们可以引入缓存。

负责数据检索的请求可以通过使用适当的 HTTP 头来缓存(例如`Cache-Control`)。这可能是巨大的性能优化。

# 第二级

在 REST API 成熟度的第二个层次上，我们使用 HTTP 动词来传达我们的意图。根据使用的动词类型，将触发不同的操作。

我们对资源组使用不同的 URL。给定一个 ID 为“1234”的工厂，我们将发送:

*   向`/plants/1234`发出的获取数据的`GET`请求。

```
GET /plants/1234 HTTP/1.1
```

*   一个`PATCH`请求`/plants/1234`更新它的值。

```
PATCH /plants/new HTTP/1.1{
   "color": "Yellow"
}
```

*   一个`DELETE`请求`/plants/1234`删除它。

```
DELETE /plants/1234 HTTP/1.1
```

唯一的区别是 HTTP 请求的类型。有五个最常用的 HTTP 动词:

*   `GET` —检索资源。
*   `POST` —创建资源。
*   `PATCH` —更新资源。
*   `DELETE` —删除资源。
*   `OPTIONS` —显示有关资源的更多信息。

# 第三级

我们已经达到了安息荣耀的最高境界。这完全是关于超媒体作为应用程序状态(HATEOAS)的引擎。

我们现在也能够知道其他资源的位置。我们不仅用数据来回应，还用在哪里寻找其他信息的信息来回应。

这使得发现额外的资源成为可能。它还使得引入资源 URL 的更改变得更加容易，因为这些更改是在响应中提供给客户端的。

如果我们正在使用 3 级 API，并且我们想要查询所有工厂，我们将发送一个请求。

```
GET /plants HTTP/1.1
```

我们将接收包含剩余资源的 URL 的分页数据。

# 结论

Richardson 成熟度模型是一个非常有用的工具，可以探索 REST APIs 的概念，并发现它们在不同层面上的潜在优势。如果我们正在设计或使用一个真正的 RESTful API，这对于评估来说是非常好的。

感谢阅读！