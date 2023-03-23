# 编写安全 REST API 的 10 个基本技巧

> 原文：<https://betterprogramming.pub/10-essential-tips-for-writing-secure-rest-api-e297990d48c5>

## REST APIs 通过一组通用的无状态操作来访问和操作数据。让我们让它们变得健壮

![](img/eb7c93fbb9c352811bb50009e19d6923.png)

Nubelson Fernandes 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

应用程序编程接口(API)无处不在。大多数现代 web 应用程序公开了客户端可以用来与应用程序交互的 API。

它们使软件能够与软件的其他部分——内部的或外部的——一致地通信，这是可伸缩性的一个关键因素，更不用说可重用性了。

像任何技术一样，REST APIs 也带来了自己独特的安全挑战。剩下的问题是如何减少这些漏洞。

今天我们将讨论以下主题:

1.  什么是 REST API？
2.  REST API 和应用程序安全性的重要性
3.  描述保护 REST APIs 的 10 大最佳实践

如果你想[使用 Spring Security、JWT 和 JPA](https://medium.com/dev-genius/securing-spring-boot-rest-api-with-spring-security-jwt-and-jpa-64ec45fb25e0) 来保护 Spring boot Rest API，有很多方法可以做到。

# 什么是 RESTful API？

*   应用程序编程接口(API)是各种软件组件之间的一组明确定义的通信方法。
*   REST APIs 通过一组通用的无状态操作来访问和操作数据。
*   这些操作是 HTTP 协议不可或缺的一部分，代表了基本的创建、读取、更新和删除(CRUD)功能，尽管不是以清晰的一对一方式
*   它更像是一种设计和交流的方法，而不是单一的工具或编程库。
*   RESTful 编程是无状态的，它提供了一个统一的接口，通常使用基于 HTTP 的 URL 和查询参数，这些参数对用户隐藏了后端基础设施。
*   响应通常以简单的基于 JSON 的键/值对的形式返回，但是 API 可以支持任何类型的内容，包括 XML、测试文档甚至图像。然后，前端软件可以以适合用户的格式提供数据。
*   一个好的 API 通过提供所有的构件使得开发一个计算机程序变得更加容易。
*   REST APIs 是现代基于云的软件经济的粘合剂。它们可以提供简单明了的信息，甚至是来自大型数据集的信息。
*   例如，你的软件可以查询亚马逊的 API 来获取产品价格，或者从谷歌地图获取地理位置。
*   或者，它可以查询需要大量计算能力的复杂后端服务。你想要一个用描述性词语标记照片或识别某人面孔的服务吗？微软基于 REST 的 API 可以做到这一点。

# API 和应用程序安全性的重要性

*   企业使用 API 来连接服务和传输数据，因此被黑客攻击的 API 会导致数据泄露。
*   破损、暴露或被黑的 API 是重大数据泄露的幕后黑手。他们将敏感的医疗、财务和个人数据公之于众。
*   也就是说，并非所有数据都是相同的，也不应该以相同的方式保护。如何实现 API 安全性将取决于传输的数据类型。
*   多种类型的数据可以通过不同的 API 进行传输。任何 API 安全性的实现都需要特定于您的 API 和正在传输的数据。
*   API 注入是黑客向易受攻击的软件程序中注入恶意代码的攻击。最常见的是 SQL、NoSQL 和命令注入。
*   包含不可信数据的命令或查询被发送到解释器。解释器被骗去执行非预期的命令或在没有有效授权的情况下访问数据。
*   尽量避免使用外部解释器。这是防止 API 注入的最简单的方法。
*   确保您的 web 应用程序仅使用其功能所需的权限运行。这样，黑客就无法利用 web 应用程序拥有的管理权限。
*   您需要确保您的应用程序端点(用于访问接口的 URL)不容易受到通过接口或绕过接口的攻击。

# 安全 REST APIs 的最佳实践

坚如磐石的身份验证和授权机制是 REST API 安全性的开始，但不是结束。在开发过程中，还需要考虑其他安全最佳实践。

通过使用签名、加密、配额、节流、令牌、API 网关等。，您的 API 安全性会大大提高。

## 1.永远用 HTTPS

总是使用 TLS 和一个已经建立的安全框架，并且有一个大的社区作为后盾。

安全 REST 服务必须仅提供 HTTPS 端点。这保护了传输中的身份验证凭证，例如密码、API 密钥或 JSON Web 令牌。它还允许客户端对服务进行身份验证，并保证传输数据的完整性。

通过始终使用 SSL，身份验证凭证可以简化为随机生成的访问令牌。令牌在 HTTP 基本身份验证的用户名字段中传递。它使用起来相对简单，而且你可以免费获得很多安全特性。

## 2.确保适当的访问控制

非公共 REST 服务必须在每个 API 端点执行访问控制。单一应用程序中的 Web 服务通过用户认证、授权逻辑和会话管理来实现这一点。

这对于现代架构有几个缺点，现代架构遵循 RESTful 风格组成多个微服务。

您可以实现通用过滤器，其中所有的身份验证和授权都可以确保。在访问非公共 API 之前，您必须确保请求者有足够的权限/授权来访问此 API。

## 3.想想 JWT 和奥厄斯的结合

对于身份验证，您可以使用 JSON Web 令牌(JWT)，对于授权，您可以使用 OAuth2。

REST APIs 也使用 JavaScript Object Notation (JSON ),这是一种文件格式，使得通过 web 浏览器和 API 传输数据更加容易。通过使用 HTTP 和 JSON，REST APIs 不需要存储或重新打包数据，这使得它们比 SOAP APIs 快得多。

虽然基本的 auth 对于大多数 API 来说已经足够好了，但是如果正确实现的话，它也是安全的——但是您可能也想考虑 OAuth。

OAuth 2.0 授权框架使第三方应用程序能够获得对 HTTP 服务的有限访问，或者通过协调资源所有者和 HTTP 服务之间的批准交互来代表资源所有者，或者通过允许第三方应用程序代表其获得访问。

## 4.输入参数验证

在到达应用程序逻辑之前，在第一步就验证请求参数。验证基于参数的查询输入。进行严格的验证检查，如果验证失败，立即拒绝请求。

攻击者可以更改 URL 参数、JSON 请求数据和 XML 输入。Fuzzers 可以通过细微的输入变化打击 REST APIs，所以在这里要注意。

*   不要信任输入参数/对象。
*   验证输入，如长度、范围、格式和类型。
*   通过在 API 参数中使用数字、布尔值、日期、时间或固定数据范围等强类型来实现隐式输入验证。
*   用正则表达式约束字符串输入。
*   拒绝意外和/或非法内容。
*   利用您特定语言的验证和/或清理库或框架。
*   定义适当的请求大小限制，并拒绝超过限制的 HTTP 响应状态 413 请求实体太大的请求。
*   考虑记录输入验证失败。假设有人每秒执行数百次失败的输入验证，那他一定不怀好意。

## 5.将允许的 HTTP 方法列入白名单

通过将允许的 HTTP 方法(例如 GET、POST 和 PUT)列入白名单来提供另一层防御。默认情况下，阻止那些您可能永远不希望有人通过公共 API 访问的内容(例如，删除)。

类似地，针对特定操作对单个用户进行身份验证。例如，您可能允许外部用户进行单个查询，但不能导出所有数据。

*   应用允许的 HTTP 方法的允许列表，例如`GET`、`POST`、`PUT`。
*   用 HTTP 响应代码`405 Method not allowed`拒绝所有与允许列表不匹配的请求。
*   确保调用方被授权在资源集合、操作和记录上使用传入的 HTTP 方法

## 6.如果可能，使用白名单中的 IP

对于服务器到服务器的通信，您可以使用白名单中的 IP。如果请求者 IP 属于您的白名单 IP 列表，那么您可以允许他们。

如果你的 API 消费者是移动应用，那么这个过程是不可能的，因为他们没有永久的 IP 地址。

## **7。使用配额和节流**

对 API 的调用频率设置配额，并跟踪其历史使用情况。对某个 API 的调用越多，可能表明它正在被滥用。也可能是编程错误，比如无限循环地调用 API。制定节流规则来保护您的 API 免受峰值和拒绝服务攻击。

## 8.记录所有失败的请求并使用工具进行分析

您可以记录所有失败的请求，并寻找识别持续攻击的模式。此外，发送带有适当 HTTP 状态代码的相关错误消息，以帮助开发人员连接到您的服务。

通过日志分析器定期分析失败的日志，对于严重的错误，如禁止访问，确保他们发送电子邮件或短信给管理员或相关人员。

## **9。使用 API 网关**

API 网关充当 API 流量的主要实施点。一个好的网关将允许你认证流量以及控制和分析你的 API 是如何被使用的。

## 10.保持简单

保护一个 API/系统的安全程度。每次你让解决方案变得不必要的复杂，你也可能留下一个漏洞。

# **结论**

感谢阅读这篇文章。希望你喜欢这个。

如果想通过 docker 学习使用 spring cloud 的微服务实现，[这篇文章可以帮助](https://medium.com/dev-genius/microservice-implementation-using-spring-cloud-with-docker-netflix-stack-76a367a5cf05)。

# **资源**

1.[https://restfulapi.net/security-essentials/](https://restfulapi.net/security-essentials/)
2。[https://developer . okta . com/blog/2019/09/04/securing-rest-API](https://developer.okta.com/blog/2019/09/04/securing-rest-apis)
3 .[https://www.toptal.com/nodejs/secure-rest-api-in-nodejs](https://www.toptal.com/nodejs/secure-rest-api-in-nodejs)
4。[https://docs . Microsoft . com/en-us/azure/architecture/best-practices/API-design](https://docs.microsoft.com/en-us/azure/architecture/best-practices/api-design)
5 .[https://www.neuralegion.com/blog/api-security/](https://www.neuralegion.com/blog/api-security/)