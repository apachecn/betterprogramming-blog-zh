# GraphQL 的神话和误解

> 原文：<https://betterprogramming.pub/graphql-myths-and-misconceptions-40d2d6db9b3b>

## 消除围绕 GraphQL 的困惑

![](img/03cd4656583543f86adc699173f798cf.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1699510) 的 [TeeFarm](https://pixabay.com/users/TeeFarm-199315/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1699510)

那么，告诉我，为什么我和我团队中的一名工程师就 [GraphQL](https://graphql.org/) 的好处进行了一场辩论，他试图通过说“你喜欢它只是因为它对前端开发人员有好处”来结束这场辩论

老实说，我有点不高兴，因为我认为这是我职业生涯中为数不多的一次，另一个只知道服务器端开发的工程师看不起我的意见，因为我喜欢构建用户界面。

稍加思考后，我意识到他是对的。GraphQL 真的很适合客户端。它对服务器端也很好，但这不是我们谈话的重点。

当比较 GraphQL 和 REST 时，可以观察到一个典型的 REST 服务是为服务器优化的，而 GraphQL 服务是为客户端和服务器端开发优化的。

换句话说，GraphQL 服务旨在让客户满意和高效。

这不是我第一次进行这样的讨论，所以我很乐意继续讨论我在 GraphQL 福音之旅中遇到的一些关于 GraphQL 的误解。

# 1.您正在暴露您的整个数据库

"但是 William，使用 GraphQL，你就把你的整个数据库暴露给客户去做他们想做的任何事情."

您没有暴露整个数据库。我曾经和一些人交谈过，他们讽刺地将 GraphQL 描述为一种暴露整个数据库的方式，允许客户从您的数据库中查询他们想要的任何内容。

这通常被描述为一个骗局，看起来 GraphQL 基本上是您前端的一个数据库连接器。这个定义有几个问题。

## **你可以拥有一个没有数据库的 GraphQL 服务**

正如 GraphQL 服务不必基于图形数据库一样，您也可以拥有没有数据库的 GraphQL 服务。它可以用于执行计算和处理的服务或其他服务的代理。

## **你设计自己的模式**

由于您设计了自己的 GraphQL 模式，该服务将只公开您希望通过您的模式访问的内容。

## **您可以限制用户查询的复杂性**

因为 GraphQL 查询可以嵌套和批处理，所以有一些工具可以让您对查询的复杂性实施一些限制。

例如，有像 [GraphQL 查询复杂性](https://github.com/slicknode/graphql-query-complexity)这样的工具，通过限制查询的复杂性和防止过度嵌套的查询来帮助防止不良行为者。

# 2.GraphQL 不安全

GraphQL 服务就像你创造的一样安全。需要明白的一点是，GraphQL 只是你的应用程序的一部分；查询层。

这是 GraphQL 的创建者在与 Airbnb 等其他公司交谈时重申的一件事，以了解 GraphQL 在野外是如何使用的。

当脸书开始使用 GraphQL 时，他们已经有了良好构建的安全和数据提取层，GraphQL 可以融入其中，所以他们没有发现在 [GraphQL 规范](https://spec.graphql.org/June2018/)中强调这些事情的必要性。

他们认为他们在脸书拥有的所有层和工具都是显而易见的，其他开发者和公司都可以使用。这使得社区很难就如何处理 GraphQL 应用程序上的身份验证和授权达成共识。

好消息是，我们在 REST 服务中喜欢的许多安全方法和应用程序也可以以类似的方式用于保护 GraphQL 服务。

这里有一些实现安全性的方法(有些非常类似于我们已经为 REST 服务所做的)

*   [JSON Web 令牌](https://jwt.io/introduction/):就像对待 REST 服务一样，通过头部传递这些令牌。
*   API 密钥:您可以实现自己的 API 密钥系统，或者通过 API 网关代理服务来保护应用程序。Yelp 的公共 GraphQL API 就是一个很好的例子。
*   [GraphQL 指令](http://spec.graphql.org/June2018/#sec-Language.Directives):您可以使用 GraphQL 指令作为拦截器来解决对您的图的一部分实施访问权限的问题。 [Apollo GraphQL](https://www.apollographql.com/) 通过实现一个 auth 指令提供了一个很好的例子。

有关使用指令强制访问权限的更多信息:

在上面的例子中，从 [Apollo GraphQL 的文档中，](https://www.apollographql.com/docs/graphql-tools/schema-directives/#enforcing-access-permissions)你可以看到访问是由用户角色使用`@auth`指令控制的。禁用字段只能由`ADMIN`级用户访问，而`canPost`字段只能由`REVIEWER`级角色的用户访问。

# 3.GraphQL 仅适用于移动和网络应用

"如果你没有多个移动和网络客户端，就没有理由使用 GraphQL . "

GraphQL 最初是随着脸书向移动世界的转移而诞生的。它已经被前端和全栈世界广泛采用，这使得一些企业架构师很容易将其视为前端世界的虚假趋势。

使用 GraphQL 进行服务器到服务器的连接没有任何限制。

由于 GraphQL 服务的出色开发经验，一些优秀的开发人员已经为现有的解决方案构建了 GraphQL 实现。

这里有几个例子。

*   GraphQL-Kafka-Subscriptions :这是一个库，使您能够使用 GraphQL 订阅向 [Kafka](https://kafka.apache.org/) 查询和发布主题。
*   [graph QL-Redis-Subscriptions](https://github.com/davidyaha/graphql-redis-subscriptions):订阅 [Redis](https://redis.io/) 消息代理的接口。
*   [回复](https://rejoiner.io/):来自 [gRPC](https://grpc.io/) 微服务的统一 GraphQL 模式。

# 4.HTTP/2 要干掉 GraphQL 了

哦是的。这是一个大的。GraphQL 的一个主要卖点是，它允许客户机在一个 HTTP 请求中批处理和获取多个资源。这很有吸引力，因为发出 HTTP 请求非常昂贵。

有了 HTTP/2，建立 HTTP 连接的成本大大降低了，因此这个卖点的吸引力也就减少了。

那么，这是否意味着一旦所有人都开始使用 HTTP/2，GraphQL 将会死气沉沉，毫无意义？我不这么认为。尽管如此，能够批量查询并不是人们使用和喜爱 GraphQL 的唯一原因。

GraphQL 的整个开发者体验棒极了。拥有类型化模式和查询 API 的标准语言等特性也是开发人员喜欢 GraphQL 的原因。如果您想更深入地了解这一点，请查看这篇写得很好的文章。

[](https://medium.com/@__xuorig__/is-graphql-still-relevant-in-an-http2-world-64964f207b8) [## 在 HTTP2 的世界里，GraphQL 还有用吗？

### GraphQL 提供的不仅仅是减少往返行程。

medium.com](https://medium.com/@__xuorig__/is-graphql-still-relevant-in-an-http2-world-64964f207b8) 

我认为，从长远来看，HTTP/2 实际上有助于提高 GraphQL 的能力和效率，而不是扼杀它。

# 结论

GraphQL 肯定不是完美的，但我认为它在改善开发人员体验和我们如何构建产品方面有很多贡献。

感谢你阅读这篇文章，如果你对此有任何想法或者我遗漏了什么，请告诉我。注意安全，洗手！