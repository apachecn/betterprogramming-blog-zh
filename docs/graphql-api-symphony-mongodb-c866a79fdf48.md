# 如何用 Symfony 创建一个通用后端

> 原文：<https://betterprogramming.pub/graphql-api-symphony-mongodb-c866a79fdf48>

## 了解使用 GraphQL、Symfony 和 MongoDB 可以做什么

![](img/e67c9c46ea36145167a4190e64f59e5b.png)

安德烈亚斯·M 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

2021 年，没有再发明轮子的空间了。开发是一门艺术，它越来越依赖于挑选和组装策略，而不是从零开始开发。按照这种趋势，仅仅为了读写数据而写代码是一个乱糟糟的时代。这可能就是为什么无头 CMS 解决方案在过去几年里一直在传播的原因，而 SaaS 的采用是迁移到云的最常见的方式。

无论如何，我们经常不得不对抗复杂的业务逻辑或复杂的集成，并且我们需要编写定制代码的灵活性。

在本文中，我们将看到如何使用curl --location --request GET 'localhost/api/graph/test' --header 'Content-Type: application/json' --data-raw '{"query":"query { \r\n entity1{\r\n _id,\r\n title,\r\n amount\r\n }\r\n}","variables":{}}'#get using REST
curl --location --request GET 'http://localhost/api/data/test/entity1' \
--header 'Authorization: Bearer {token}'

嗯，我们已经看到了如何使用测试来使用 API，但是您可以想象集成到 JavaScript 应用程序中也是一样容易的。

# 带什么回家

在本文中，我们已经看到了通过整合 Symfony 框架的一些智能特性来实现通用后端是多么容易。

特别是，我们已经看到了如何将数据库访问封装到一个服务中，在这个服务中，我们可以集中数据访问逻辑并实现事件监听器和日志记录。此外，这种特性可以使用 API 公开，实现一个简单的 REST API 引擎。对于那些寻找更现代的东西的人来说，我们能够使用数据模型定义建立一个 GraphQL 端点。我们通过添加 Oauth2 服务器和基于 JWT 的令牌验证以及用于扩展用户信息的 OpenID 协议来保护一切。

事实上，这一切只花了我两天的工作是了不起的，这是可能的，只有感谢现成的组件与 Symfony 可用。

现在是时候下载代码并进行测试了！因此，这里有一个参考列表:

*   [GitHub 项目](https://github.com/zeppaman/api-farm)
*   [包装商参考页](https://packagist.org/packages/zeppaman/api-farm)
*   [基于此模板建立新项目的框架项目](https://packagist.org/packages/zeppaman/api-farm-skeleton)
*   [Symfony 项目主页](https://symfony.com/)