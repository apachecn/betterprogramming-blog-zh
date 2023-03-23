# 提升你的 RESTful API: 4 个必须知道的最佳实践

> 原文：<https://betterprogramming.pub/4-best-practices-of-restful-api-you-should-know-d3c752debd88>

## 使用这些技巧来打造一个可靠的 RESTFUL 设计

![](img/db3b4099e2368e12317bfe82be5176cd.png)

照片由[香农·波特](https://unsplash.com/@cifilter?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/web?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

REST 是一种广泛用于构建 API 的架构风格。它以直观、灵活和易于学习而闻名，这使得它成为开发人员的热门选择。

然而，尽管 REST 很简单，但它也可能被误解，并不总是被使用它的人完全理解。虽然大多数开发人员可能都有一些 REST 方面的经验，但是很少有人真正认为自己是使用 REST 的专家。

在本文中，我们将讨论 REST API 的一些最佳实践:

向前跳:

*   [选择 RESTful 端点的正确粒度](#64fa)
*   [不要基于数据库表构建您的 API](#6ccd)
*   [使用 HATEOAS 数据，让您的客户端更加智能](#7ad4)
*   [以一致的模式处理错误](#391b)

# 选择 RESTful 端点的正确粒度

粒度是对 API 公开的资源范围的相对度量。细粒度 API 公开少量(或单个)资源，而粗粒度 API 在单个请求中返回来自许多资源的信息。

这里有一个例子:

```
# coarse-grained
/v1/user/1/blog
{
 “posts”: […],
 “comments”: […],
 “profile”: { “name”: “user 1” }
}
# fine-grained
/v1/user/1/posts
/v1/user/1/comments
/v1/user/1/profile
```

选择正确的粒度非常重要。太细粒度的 API 将导致喋喋不休的 API 调用、性能问题、复杂的架构和增加的维护成本。在上面的例子中，我们需要发出三个请求:`/user/{userID}/posts`、 `/user/{userID}/comments`和`/user/{userID}/profile` 来获取用户博客的信息。相比之下，单次调用`/user/{userID}/blog`将提供相同数量的信息。

另一方面，过于粗粒度的 API 将缺乏灵活性，存在可伸缩性问题，并且难以重用。

当优先考虑性能时，首选粗粒度 API。对于`Get` 请求，使用粗粒度 API 使得通过一次调用获得所有需要的信息成为可能。这意味着 web 服务器上的负载减少了，带宽延迟也更小了。

细粒度 API 的典型用例包括移动应用和`Post/Put`请求。

对于 API 粒度，没有放之四海而皆准的规则。设计选择是几个经常冲突的因素之间的平衡，包括以下因素:

*   响应负载的大小
*   API 调用的次数
*   可维护性和可重用性
*   可量测性
*   业务需求

在现实世界的项目中，您会发现结果通常是细粒度和粗粒度 API 的混合。底线是做出决策是为了满足业务需求和其他技术考虑。一个设计良好的 API 将足够细粒度以暴露必要的细节，但又不会粒度太细以至于不能使用。

# 不要基于数据库表构建 API

Restful API 是围绕资源设计的。资源可以松散地链接到业务实体，而不是数据库表。

基于内部数据库表镜像 API 端点是很容易的。例如，对于以下数据结构

```
Orders
---------
id (integer)
customer_id (integer)
status (string)

OrderItems
---------
id (integer)
order_id (integer)
product_id (integer)
```

基于这些表构建 API 可能很有吸引力，比如

```
GET /orders
POST /orders
GET /orders/{id}
...

GET /orders/{id}/items
POST /orders/{id}/items
GET /orders/{id}/items/{item_id}
...
```

但是这种方法有几个问题。首先，它暴露了太多关于底层数据库结构的信息。我们在 API 接口和数据库实现之间建立了紧密的耦合。这只会使 API 难以维护。因此，我们不应该向客户公开(泄露)内部数据库结构。

其次，它要求客户端发出多个请求来检索订单的所有信息(例如，订单细节和订单项目)。这可能是低效和缓慢的。

更好的方法是设计一个更粗粒度的 API，在一个请求中提供所有必要的信息:

```
GET /orders?customer_id={customer_id}&status={status}
POST /orders
GET /orders/{id}
...
```

当设计一个 REST API 时，我们如何将领域概念从底层数据存储中分离出来？

关键是摆脱数据库驱动的思维模式，抵制快速进入实现细节的诱惑。以下是一些供思考的建议:

*   从业务领域的角度考虑 REST API，而不是数据库。收集业务需求以建立 API 的边界上下文。问很多问题，比如，我们为什么需要 API？将提供哪些新功能？用户是谁？有哪些用例？
*   将 API 接口与底层实现细节隔离开来。一个好的 API 设计是一个稳定的 API，它可以在实现改变时持续很长时间。例如，数据库模式将会发展或用不同的数据库重新实现。如果 API 是用适当的抽象设计的，这些内部变化不会影响 API 接口。
*   单一责任原则。一个好的 REST API 应该专注于一个业务领域，并把它做好。创建一个管理多个职责的 API 会使它变得不必要的复杂和难以维护。

# 消费 HATEOAS 数据，让您的客户端变得更加智能

HATEOAS(作为应用程序状态引擎的超媒体)是 REST API 的一个接口，它在 API 响应中提供了到相关端点的可导航链接。REST 的一个原则是，API 应该是自描述的，任何消费者都应该能够使用提供的链接发现 API 并与之通信。

HATEOAS 是一种表达当前资源和其他资源之间关系的直观方式。下面是一个用户博客 API 端点`/v1/user/1/blog`的示例响应。如您所见，帖子和评论的相关链接嵌入其中。

```
{
 “id”: 1,
 “name”: “John Duggan’s Blog”,
 “links”: [
  {
   “href”: “1/posts”,
   “rel”: “posts”,
   “type” : “GET”
   }, 
  {
   “href”: “1/comments”,
   “rel”: “comments”,
   “type” : “GET”
   }
 ]
}
```

有了 HATEOAS，客户端可以发现它可以继续执行的下一个动作。与客户端中硬编码的 URL 链接相比，HATEOAS 链接使客户端代码更加动态和灵活。

从架构的角度来看，HATEOAS 允许一个松散耦合的系统。当客户机使用 HATEOAS 实现动态链接时，服务器可以在不更新客户机代码的情况下更改 URL。

在 Martin Fowler 关于 REST Richardson 成熟度模型的[文章中，当实现 HATEOAS 以实现可发现性时，REST API 被认为是第三级。不幸的是，尽管 HATEOAS 被认为是 REST 设计中必不可少的一部分，但它在现实世界的项目中经常被遗忘。](https://martinfowler.com/articles/richardsonMaturityModel.html#level3)

虽然不是每个 REST API 项目都有 HATEOAS 的用例，但是如果用在正确的地方，它会是一个非常强大的特性。我处理过的一些好的用例包括:

*   Workflow App: HATEOAS 返回的链接可以代表当前的状态，即链接的集合代表下一步可用的所有可能的动作。好处是服务器管理所有的业务逻辑，而客户机变得被动。
*   管理用户权限:HATEOAS 链接是传递资源权限的一种自然方式。例如，客户端应用程序将在加载新屏幕时发送 Get 请求，Get 响应中的链接表示当前用户允许的操作。因此，客户端可以相应地对用户权限做出反应(显示/隐藏按钮等)。).

## 用一致的模式处理错误

在 REST API 中，处理错误和处理成功的响应一样重要。有了可靠的错误处理，开发人员会发现故障排除更容易，应用程序也更容易维护。

一个可靠的错误处理框架必须有一个全局错误处理程序来捕获和处理错误。根据错误的性质，我们应该使用适当的 HTTP 状态代码，并将错误映射到可读的信息性错误消息。全局错误处理程序将确保错误得到处理，并返回一致而简洁的错误消息。

该键具有项目的标准错误响应结构。错误响应应该至少包含“代码”和“消息”键-值对，并且应该能够容纳多个错误。下面是一个简单的例子:

```
{
 “errors”: [
   {
   “code”: “10003”,
   “message”: “invalid address field”
   },
   {
   “code”: “10004”,
   “message”: “birthday field is requried”
   }
   ]
 }
```

请注意，代码和消息字段不应是直接来自服务器的技术错误代码和消息。由于来自 API 的响应是向客户端公开的，所以它们需要被翻译成用户友好的、可读的消息，并且您不希望泄漏不必要的技术细节。

当然，你可以根据自己的需要添加更多的属性。一些常见属性包括:

*   `severity`:可以是`error` | `warning` | `info`
*   `suberrorcode`:低级错误代码
*   `traceId`:识别错误来源的 id
*   `correclationId`:复杂流日志中要跟踪的内部 ID。
*   `stacktraces`:发生错误时的活动堆栈帧。通常在生产中应该禁用它。使用该字段时，注意不要暴露敏感信息。

一个有用的参考是[RFC 7807](https://www.rfc-editor.org/rfc/rfc7807):HTTP API 的问题细节。它提供了关于如何在 HTTP 响应中携带机器可读的错误细节的指南。

客户端可以使用一致的错误响应消息结构来有效地对错误做出反应。也能让开发者的日子更轻松。

# 摘要

REST APIs 相对容易上手，尤其是在使用成熟的框架时。只需几个步骤，您就可以创建一个简单的“hello world”服务。然而，构建一个可靠的 REST API 并不总是简单的，因为没有严格的规则可循。相反，它需要平衡技术考虑、约束和业务需求来创建设计良好的 API。不容易，但为了能带来的好处，值得。

希望你能从这篇文章中学到一二。

编码快乐！