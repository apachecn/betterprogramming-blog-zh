# 了解 gRPC

> 原文：<https://betterprogramming.pub/understanding-grpc-60737b23e79e>

## 以及 REST 和 RPC 架构之间的区别

![](img/2616cf860e845258d2da012be6d9deff.png)

本文的目标是对 gRPC 有一个高层次的了解。它还将解释 gRPC 与 web 应用程序通信所遵循的现有协议和架构之间的相似之处和不同之处。

# **什么是 gRPC？**

gRPC 是一个开源的远程过程调用框架，用于服务之间的高性能通信。这是一种连接用不同语言编写的服务的有效方式，支持负载平衡、跟踪、健康检查和身份验证。默认情况下，gRPC 使用协议缓冲区来序列化结构化数据。一般来说，gRPC 被认为是微服务架构 REST 协议的更好替代方案。gRPC 中的“g”可以归功于最初开发这项技术的谷歌。

在深入了解 gRPC 的更多细节之前，我们先来看看微服务架构。

# 微服务与整体服务

整体架构是设计应用程序的传统方式。它包含一个不可分割的代码库，为客户端用户界面、服务器端应用程序和数据库服务。从事该项目的所有开发人员都将把代码贡献给同一个存储库。我最喜欢的一个与整体建筑相关的类比是把它想象成一个工作室公寓。一个单间会根据需要分成各种空间。

整体架构的优势在于，由于只有一个单元，日志记录、性能监控和缓存等操作都可以轻松完成。此外，开发、测试、调试和部署都很简单。

但是随着应用程序的增长，维护、扩展甚至理解都变得很困难。此外，它可能变得非常复杂，以至于代码中的一个小变化都会影响整个应用程序。

monoliths 的另一个重要缺点是它是对单一技术的严格承诺。采用新的框架或语言可能需要整个系统重写。

进入微服务架构！

如果单体建筑是一个工作室公寓，那么微服务建筑可以被认为是一个有许多房间的房子。这意味着整个应用程序将被细分成多个更小的应用程序或服务。

这给了开发团队选择最适合他们需求的技术的灵活性，并且可以让他们独立地扩展他们的服务。微服务应用中的任何故障只会影响特定的服务，而不会影响整个应用。

这些服务可以独立开发、维护和部署，它们通过称为 API(应用程序编程接口)的已定义方法相互通信。

微服务之间通过 HTTP 的通信可以通过多种方式完成。最广泛使用的方法是遵循 REST 协议。gRPC 是执行这种通信的另一种方式。它旨在克服微服务通信中 REST 的局限性。

# REST 架构

REST 是一个使用 HTTP 协议的 web 架构。它被广泛用于 web 应用程序的开发。简而言之，REST 是一种客户机-服务器关系，后端数据通过 JSON/XML 等简单表示提供给客户机。正如罗伊·菲尔丁所描述的，REST 代表具象状态转移。REST 是一种协议，它并不强制执行任何规则来决定如何在较低的级别上实现它。它为高级架构实现提供了指导原则。

为了使任何应用程序真正 RESTful，必须遵循六个架构约束:

1.  统一接口:意思是 API 接口必须呈现给 web 应用中的资源给 API 的消费者。
2.  客户端-服务器:客户端和服务器必须相互独立，客户端应该只知道对资源的 URIs。
3.  无状态:服务器不得存储任何与客户端请求相关的内容。客户端负责维护应用程序的状态。
4.  可缓存:资源必须是可缓存的。
5.  分层系统:架构必须是分层的，也就是说架构的组件可以在多个服务器中。
6.  按需编码:客户端必须能够获得可执行代码作为响应。这是一个可选约束。

基于 REST 的 Web 服务被称为 RESTful web 服务。在这些应用程序中，每个组件都是一个资源，这些资源可以通过一个公共接口使用 HTTP 标准方法来访问。基于 REST 的架构中通常使用以下四种 HTTP 方法:

*   `GET` —对资源的只读访问。
*   `POST` —创建新资源。
*   `DELETE` —删除资源。
*   `PUT` —更新现有资源/创建新资源。

# RPC 体系结构

RPC 代表远程过程调用。顾名思义，我们可以在远程服务器上调用一个函数/方法。RPC 协议允许以相同的格式获得问题的结果，而不管它在哪里执行。它可以是本地的，也可以是使用更好资源的远程服务器。

RPC 是比 REST 更古老的协议。自 20 世纪 70 年代阿帕网时代以来，它就被用于执行网络操作。术语 RPC 是由 Bruce Jay Nelson 在 1981 年首次提出的。但是正如我们将要看到的，RPC 仍然是相关的，并且以不同的方式在基于 API 的现代应用程序中实现。

想法是一样的。API 是通过定义公共方法构建的。然后用参数调用这些方法。RPC 只是一堆函数，但是在 HTTP API 的上下文中，它需要将方法放在 URL 中，将参数放在查询字符串或查询体中。

RPC APIs 将使用类似于带有主体`{ “id”: 1 }`的`POST /deleteResource`来代替 REST 方法，后者将是`DELETE /resource/1`。

RPC 在物联网设备和其他需要为低功耗设备定制合约通信的解决方案中非常受欢迎，因为许多计算操作可以卸载到另一台设备上。传统上，RPC 可以实现为 RPC-XML 和 RPC-JSON。

gRPC 是基于 RPC 协议创建的最新框架。它利用其优势，试图纠正传统 RPC 的问题。

# 什么是 gRPC 来着？

从我们目前所读到的，我们可以重新定义 gRPC。它是对传统 RPC 框架的改编。那么它与现有的 RPC 框架有什么不同呢？

最重要的区别是 gRPC 使用协议缓冲区作为序列化和通信的接口定义语言，而不是 JSON/XML。协议缓冲区可以描述数据的结构，并且可以从该描述中生成代码，用于生成或解析表示结构化数据的字节流。这就是 gRPC 更适合多语言(用不同技术实现)的 web 应用程序的原因。二进制数据格式允许通信更简单。gRPC 也可以和其他数据格式一起使用，但是首选的是协议缓冲区。要详细了解协议缓冲区，请查看这篇文章。

此外，gRPC 构建在 HTTP/2 之上，它支持双向通信以及传统的请求/响应。gRPC 允许服务器和客户机之间的松散耦合。在实践中，客户机打开与 gRPC 服务器的长期连接，并且为每个 RPC 调用打开一个新的 HTTP/2 流。

# REST 与 gRPC

与使用 JSON(大部分)的 REST 不同，gRPC 使用协议缓冲区，这是一种更好的数据编码方式。由于 JSON 是基于文本的格式，它将比 protobuf 格式的压缩数据重得多。

gRPC 相对于传统 REST 的另一个显著改进是它使用 HTTP 2 作为传输协议。主要由 REST 使用的 HTTP 1.1 基本上是一个请求-响应模型。(其余的也可以用 HTTP2 实现)gRPC 利用了 HTTP 2 的双向通信特性以及传统的响应-请求结构。在 HTTP 1.1 中，当多个请求来自多个客户端时，它们会被逐一处理。这可能会降低系统速度。HTTP 2 允许多路复用，因此可以同时处理多个请求和响应。

我们可以得出结论，当用例涉及使用惯用 API 的多语言通信或大规模微服务通信时，gRPC 是一个很好的选择。

# 参考

[](https://www.n-ix.com/microservices-vs-monolith-which-architecture-best-choice-your-business/) [## 微服务 vs Monolith:哪种架构最适合您的企业？

### Romana Gnatyk *年 10 月 03 日仅在几年前，微服务已成为一种加速发展的…

www.n-ix.com](https://www.n-ix.com/microservices-vs-monolith-which-architecture-best-choice-your-business/) [](https://restfulapi.net/rest-architectural-constraints/) [## REST 架构约束

### REST 是一种通过 HTTP 设计松耦合应用程序的架构风格。RESTful 原则并不强制…

restfulapi.net](https://restfulapi.net/rest-architectural-constraints/) [](https://www.xenonstack.com/insights/what-is-grpc/) [## 采用 GRPC - XenonStack 的优势和最佳实践

### gRPC 是一个高性能、开源的通用 RPC 框架。简而言之，它使服务器和客户端能够…

www.xenonstack.com](https://www.xenonstack.com/insights/what-is-grpc/)