# 如何使用 GraphQL 进行微服务通信

> 原文：<https://betterprogramming.pub/graphql-for-microservices-communication-d40e04956818>

## GraphQL 无疑是微服务之间最好的通信系统吗？

![](img/83154b8d7136ca299a1403181f289d84.png)

照片由 [kazuend](https://unsplash.com/@kazuend?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

到目前为止，我们都很清楚松散耦合的可独立维护、可测试、可部署的微服务越来越受欢迎。但是这种非常流行的架构也总是带来一个众所周知的问题——服务之间的通信。这就是为什么在设计微服务架构时，每个架构师都会提前考虑通信媒介。

在本文中，我将重点关注一种可以在服务之间使用的通信类型——graph QL。这里我举了两个基本的微服务的例子，它们解决了单个的业务问题，以及 GraphQL 是如何在这些服务之间进行通信的。GraphQL 也可以用作 APIGateway，我们将在以后的博客中讨论。

# **使用 GraphQL 通信的优势**

在基于 GraphQL 的通信中，服务之间会发出 HTTP POST 请求。该请求包含查询表达式，该表达式定义了要在响应中返回的数据。通过这种方式，我们可以准确地获取我们需要的数据，不会出现数据提取不足或过量的情况。

GraphQL 可用于在单个请求中聚合来自多个来源的数据。

GraphQL 不像 REST APIs 那样有不同的端点或者不同的版本，只有一个版本和一个端点/graphql。

因为我们预先声明了字段的类型，所以通过捕捉与类型相关的错误对我们有好处。

# **实施**

用户服务负责与`User`和`Post`相关的查询和变异，服务包括与用户的`Posts`相关的查询和变异。我相信服务名称和我在这里试图实现的目标是不言自明的，并且非常适合微服务架构。

用户`typeDef`包含 id、姓名、电子邮件。

帖子`typeDef`包含 id，`postDescription`，userId。这里，userId 充当用户和文章之间的链接字段。

如果用户服务需要来自 Posts 服务的部分信息，则处理请求，反之亦然，在这种情况下，服务之间将发生通信，GraphQL 将在单个请求中聚合来自不同源/服务的数据。

在这种情况下，最简单的例子是获取一个特定用户的帖子详细信息——我们需要使用下面的代码片段中显示的 user-id 查询 Posts 服务。

查询中的动态参数——在本例中，`userID`可以作为变量在 HTTP post 请求的主体中传递。

# **结论**

GraphQL 无疑是微服务之间最好的通信系统吗？当然不是。

在根据业务需求、响应类型应该是同步还是异步等来决定在服务之间使用哪种通信介质之前，需要考虑多个因素。

与 REST 的流行相比——graph QL 的社区正在成长，它还有很长的路要走。但是正如我之前讨论的那样，GraphQL 有其自身的优势，考虑到所有因素，如果 GraphQL 可以用于您的微服务通信，那为什么不呢！

你可以在 [GitHub 库](https://github.com/aindrila-choudhuri/graphql-microservices-communication)中找到完整的源代码。