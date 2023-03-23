# 通过 Swift 在 iOS 中使用 GraphQL

> 原文：<https://betterprogramming.pub/using-graphql-in-ios-using-swift-d2144ed54e89>

## 如何在您的 Swift 应用中使用 GraphQL

![](img/10154e7747f3d022e1b3cb3c45229d74.png)

照片由[埃米尔·佩龙](https://unsplash.com/photos/xrVDYZRGdw4)在 [Unsplash](https://unsplash.com/) 上拍摄

GraphQL 是常用的 REST API 方法的替代方案。当使用 [GraphQL](https://graphql.org/) 时，您只能获得服务器的一个端点。通过该端点，您需要指定您需要的确切数据。它由脸书于 2012 年创建，并于 2015 年开源。

在 Swift 应用程序中使用 GraphQL 并不难。最简单的方法就是使用 [Apollo iOS](https://github.com/apollographql/apollo-ios) 客户端，最近有很多很棒的改进和 bug 修复。Apollo iOS 是一个强类型的缓存 GraphQL 客户端，适用于 iOS、macOS、iPadOS、watchOS 和 tvOS。它是用 Swift 编程语言写的。

# GraphQL 与 REST

REST API 为每种特定类型的信息公开一个端点。例如:

*   `/visitors` —所有访客列表
*   `/visitors/ID/` —带 ID 的具体访客详细信息

想象一下，你想让一个访问者说出他们的名字。首先，你需要得到访客名单，按名字搜索。一旦找到特定的访问者 ID，就可以从另一个端点获得其余的访问者详细信息。

当您使用 GraphQL 时，只需向服务器发出一个请求就可以做到这一点。您只需要指定您希望在声明性 GraphQL 语法中获得哪种数据:

```
{
    allVisitors(name: "John Appleseed") {
      id
      name
      reasonOfVisit
      isServed
      signInTime
      feeedback
      ...
    } 
  }
```

这只是一个简单的例子。实际上，只需对服务器进行一次调用，就可以获得多种级别的数据。

# 在 iOS 中使用 Swift 设置 GraphQL

您可以在 iOS、iPadOS、macOS、watchOS 和 tvOS 应用程序中使用 GraphQL。最简单的方法是使用 [Apollo iOS SDK](https://github.com/apollographql/apollo-ios) ，这是一个用 Swift 编程语言编写的强类型缓存 GraphQL 客户端。

## 装置

要设置 Apollo 客户端，您需要[安装 Apollo 框架](https://www.apollographql.com/docs/ios/installation/#installing-the-apollo-framework)并[生成一个模式文件](https://www.apollographql.com/docs/ios/installation/#adding-a-schema-file-to-your-target-directory)。该文件描述了如何从服务器获取信息并将其添加到 Xcode 应用程序目标。

然后，您[可以创建](https://www.apollographql.com/docs/ios/installation/#creating-graphql-files-with-your-queries-or-mutations) `.graphql`文件，其中包含您希望如何从服务器获取或更改数据的查询或变更。之后，您需要在 Xcode 构建过程中添加[代码生成](https://www.apollographql.com/docs/ios/installation/#adding-a-code-generation-build-step)构建步骤，以生成类型安全的 Swift 代码，用于与您的服务器通信。

构建您的 Xcode 应用程序目标，然后[添加一个生成的](https://www.apollographql.com/docs/ios/installation/#adding-the-generated-api-file-to-your-target) API 文件来使用它。

## 与服务器通信

一旦安装过程完成，您就可以设置`ApolloClient`实例并指向您的 GraphQL 服务器。

```
import Apollo let graphQLClient = ApolloClient(url: URL(string: "http://localhost:8080/graphql")!)
```

然后，您可以使用`graphQLClient`获取数据并处理查询结果。

```
let allVisitorsQuery = AllVisitorsQuery(name: "John")
  graphQLClient.fetch(query: allVisitorsQuery) { result in
    switch result {
    case .failure(let error):
      print("Something bad happened \(error)")
    case .success(let graphQLResult):
      guard let john = graphQLResult.data?.visitor else {
        return
      }

      print(john)  
  }
```

这只是一个简单的例子。在接下来的一些帖子中，我们将研究如何改变数据，甚至使用 Websockets 订阅。

# GraphQL 的利与弊

每种技术都有其优点和缺点。您需要理解是否值得放弃 REST API。

## 赞成的意见

GraphQL 使实现、更改和维护您的应用程序变得更快。您不需要与后端沟通每个更小的细节，甚至不需要创建新的端点。

当您在 Swift 应用程序中使用 GraphQL 时，使用 Apollo iOS 框架很有帮助。它支持 iOS、iPadOS、watchOS、macOS 和 tvOS。您可以使用 Swift 类型安全和其他强大的功能，包括结果类型等等。

最近，Apollo iOS 框架出现了许多重大更新，更多的 bug 正在修复。越来越多的公司不仅在 web 上，而且在他们的移动应用程序上转向 GraphQL。

## 骗局

使用 GraphQL 并不那么简单，尤其是如果您习惯了 REST API 方法。转换你的思维和适应一个新的过程需要一些时间。它还在服务器和您的应用程序之间添加了一个额外的层，您需要在未来维护它。

使用 Apollo iOS SDK，您需要非常小心地使用[缓存](https://www.apollographql.com/docs/ios/watching-queries/)。有几种方法可以处理缓存以及何时根本不缓存。只是为了确保在安装之前查看官方文档，或者如果您遇到任何数据不一致的问题。

Apollo 在 Swift 中使用 GraphQL 时帮助很大，但是有一个缺点是可选性。如果您可以自由地决定想要从服务器获得什么样的数据，这就带来了可选性的权衡。这是一个小问题，但是当您构建模型并从服务器获取数据时，您需要处理这个问题。

# 总结

GraphQL 是一种与服务器通信的现代方式。它可以取代常用的 REST API 方法。使用 GraphQL，您可以使用声明性语法通过一个端点与后端进行通信。

借助 Apollo 框架，在 iOS、iPadOS、watchOS、macOS 和 tvOS 的 Swift 应用程序中使用 GraphQL 非常简单。它得到了 GraphQL 社区的官方支持，最近，它得到了很好的维护和支持。

## 链接

*   官方 iOS GraphQL [文档](https://www.apollographql.com/docs/ios/)和[库](https://github.com/apollographql/apollo-ios)
*   [博文](https://troubled.pro/2019/02/graphql.html)
*   大书呆子牧场系列文章。[第一部分](https://www.bignerdranch.com/blog/using-graphql-in-production-ios-applications-part-1/)、[第二部分](https://www.bignerdranch.com/blog/using-graphql-in-production-ios-applications-part-2/)和[第三部分](https://www.bignerdranch.com/blog/using-graphql-in-production-ios-applications-part-3/)
*   斯德哥尔摩[演讲](https://www.youtube.com/watch?v=ArMgdV-VwJ8)
*   Swift 峰会[演讲](https://www.skilled.io/u/swiftsummit/interfacing-with-graphql-in-swift)