# 用 Cypress 拦截 GraphQL 请求

> 原文：<https://betterprogramming.pub/intercepting-graphql-requests-with-cypress-99984d6d500c>

## 编写端到端测试不一定是痛苦的

![](img/a073a1c30cb96683f704188dbfa1ff0b.png)

照片由[香农·波特](https://unsplash.com/es/@cifilter?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

为我们的 web 应用程序构建一套集成测试可能是一件痛苦的事情！在真实的浏览器环境中，在与其他服务隔离的情况下，很难创建一套完整的测试来彻底测试我们的应用程序的代码覆盖率。

我们可能遇到的一些问题包括认证测试用户、与第三方服务交互以及在测试环境中与我们自己的后端交互。Cypress 是构建 e2e 测试最流行的框架之一，它帮助我们处理了许多这样的问题。

根据我的经验，cypress 提供的最有用的功能之一是`cy.intercept()`，它用于拦截网络请求并模拟对你的应用程序的响应。这使得您的应用程序可以独立运行，并像在生产中一样运行，发出网络请求并接收预期的(尽管*被嘲笑*)响应，以继续运行和测试。这里有一个简单的例子，展示了网络请求 REST API 的实际情况…

# GraphQL 请求怎么办？

然而，这种模式有一个问题。应用程序发送的请求使用`cy.intercept()`的前两个参数进行匹配——使用的 HTTP 方法和请求发送到的 uri。如果你的应用程序使用 GraphQL，*每一个*网络请求都将匹配前两个参数，因为*每一个* GraphQL 请求都将是对`<server-uri>/graphql`的 POST 请求！

解决这个问题需要一点技巧。我们不是将一个`StaticResponse`对象作为第三个参数传递给`cy.intercept()`，而是传递一个回调函数，它允许我们更仔细地查看被拦截的请求。首先，假设我们有这个获取练习的查询，我们将这个操作命名为`AllExercises`

在我们的 cypress 规范中做一点欺骗，我们可以完成同样的目标，拦截这个请求并发送回一个模拟的响应…

注意，我们仍然拦截每个对`localhost:4000/graphql`的 POST 请求。但是通过使用回调函数作为`cy.intercept()`的第三个参数并检查请求对象，我们实际上只对*关心的请求做了*任何事情。我们检查请求是否满足比方法和 URI 更具体的条件(在本例中，检查请求的`operationName`是否为`"AllExercises"`)，然后使用`req.reply()`发回适当的模拟响应。

如果我们正在测试的页面产生了许多 GraphQL 请求，这种技术可能会有点混乱。我们可能会被大量的 if 语句和`cy.intercept()`函数中的许多模拟响应对象所困扰。那会很快变丑的！这里有一个我发现的技术，它帮助我保持代码的整洁和可读性，同时仍然处理拦截许多需要不同模拟响应的不同 gql 请求的问题…

通过将模拟响应映射到它们各自的`operationName`,我们可以大大简化拦截。当然，根据您测试的内容，这并不总是有效，但是如果您只是需要确保您的应用程序获得它需要的数据，这可能是一个很好的简单方法。

我们大多数人不喜欢为我们的应用程序编写 e2e 测试。模仿对 GraphQL 服务器的响应可能特别痛苦。希望这种使用 cypress 的 intercept 函数，并将模拟响应映射到 GraphQL 操作名的技术可以帮助减轻痛苦！