# 导航 NAP 堆栈:NestJS + Angular + PostgreSQL

> 原文：<https://betterprogramming.pub/navigating-the-nap-stack-nestjs-angular-postgresql-9a6d3c5b6bcf>

## 开发人员喜欢首字母缩写词，所以终于有了一个每个人都能理解的首字母缩写词

![](img/170f50abb39b04add38a2eaea16e2645.png)

[Jackman Chiu](https://unsplash.com/@jackmanchiu?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

说到网络开发，有很多技术栈。你可能对 LAMP、MEAN 或 MERN 等首字母缩写词很熟悉。

*   在前端方面，角度和反应是常见的选择
*   在后端，Express.js 是最受欢迎的选择
*   在数据库方面，MongoDB 和 PostgreSQL 是最受欢迎的选择
*   TypeScript 正在成为前端和后端的首选。

我个人不太喜欢表达。虽然它很容易上手，并且有一个巨大的生态系统围绕着它，但也有一些缺点:

*   它的发展停滞了一段时间。
*   缺乏一个合适的开箱即用的架构使得项目看起来不同，使得加入和调整标准更加困难。
*   Express 是相当准系统的，所以你需要安装许多第三方的包，用于运行微服务所需的常见东西，如认证、缓存、安全和测试。
*   测试可能比 Java 等其他语言的框架更困难。

有一个相当新的孩子在(后端)块: [NestJS](https://nestjs.com) 。我知道，命名可能会很混乱，因为有类似的项目，如 Next.js 和 Nuxt.js 获得关注的原因有很多:

*   Nest 充分利用了 TypeScript。Express 虽然有 TS 支持，但是还是用 JS 写的。另一方面，Nest 是开箱即用的强类型，并使用类似于[装饰器](https://www.typescriptlang.org/docs/handbook/decorators.html)的特性。
*   Nest 是一个提供开箱即用架构的框架。这个架构很大程度上受到 Angular 的启发:熟悉 Angular 的开发人员会很快找到自己的路。由于 Nest 比 Express 更接近经典的服务器端框架，如 Spring Boot，Java / C#开发人员也将很快理解 Nest。
*   Nest 提供了大量开箱即用的功能，或者借助于第一方的软件包。虽然 Express 本身是轻量级的，但是您可能需要安装几个第三方包。
*   Nest CLI 是一个提高生产率和简化代码生成的好工具，比如创建一个新的控制器(Nest 的创造者从 Angular 那里获得灵感的另一件事)
*   您仍然可以使用 Express(或 Fastify)包，因为 Nest 使用了其中一个框架
*   Nest 对测试有很好的支持。模仿任何可注入类的返回值都像 Angular 中一样简单和有用。 [Jest](https://jestjs.io) 和 [supertest](https://github.com/visionmedia/supertest) 已经默认包含在内。
*   [文档](https://docs.nestjs.com)相当广泛。除了基本的嵌套概念之外，它还涵盖了数据库、GraphQL、安全性和性能等主题。该文档提供了一套启动 OpenAPI、health checks 和 Prisma 的方法。

你可能注意到了很多优点，因为 Nest 把 Angular 的很多优点带到了后台世界。这也意味着，如果你非常讨厌 Angular，你可能不会成为 Nest 的粉丝。不过，你还是应该给 Nest 一个机会。我们来看看任何一个 Nest 应用的核心概念。

# Nest 的核心概念

*   控制器:处理传入的请求并向客户端返回响应。
*   提供者:通常是一个可以作为依赖注入的类。
*   模块:用于封装一组密切相关的功能的类。虽然您可以将所有内容都压缩到一个模块中，但是最好将它们分开。
*   中间件:在路由处理器之前调用的函数。通常用于对请求和响应对象进行更改。
*   Pipe:具有`transform`函数的类。管道对于将输入数据转换成期望的形式或进行验证(当输入数据不正确时抛出异常)非常有用
*   Guard:一个类，根据特定的条件，决定给定的请求是否由路由处理器处理。如果用户不能访问这个端点，它应该抛出一个错误。

看看这个基本的 Nest 模板来玩一下 Nest:

[](https://github.com/nestjs/typescript-starter) [## GitHub—nestjs/TypeScript-starter:嵌套框架 TypeScript starter

### 一个渐进式 Node.js 框架，用于构建高效且可伸缩的服务器端应用程序。嵌套框架类型脚本…

github.com](https://github.com/nestjs/typescript-starter) 

# 介绍 NAP 堆栈😴

在工作中，这是我们目前最喜欢的技术组合:

*   [**N** est.js](https://nestjs.com#) 用于微服务
*   [**单页应用程序的一个**三角形](https://angular.io)
*   [**P** ostgreSQL](https://www.postgresql.org) 作为主数据存储

由于开发人员喜欢首字母缩写词，并且我必须用几句话来解释我们的技术堆栈，我正在为我们的技术堆栈寻找一个合适的名称。人们会记住的东西。没有人会怀疑午睡的必要性，所以我们应该好好休息。让我总结一下我们选择这一技术堆栈的原因:

*   NestJS 是一个 Node.js 框架，用于构建高效、可靠和可伸缩的服务器端应用程序。
*   Angular 是一个创建 web 应用程序的框架，专注于生产力和企业就绪性。
*   由于 Angular 和 Nest 共享许多概念，使用 TypeScript 和类似的架构使得知识转移更加容易，开发人员很快就变得高效。
*   PostgreSQL 是主存储的可靠选择。它提供了关系数据库的好处。如果需要存储数据(就像使用 MongoDB 这样的非关系数据库一样)，那么可以使用`[jsonb](https://www.postgresql.org/docs/current/datatype-json.html)`类型。

## 结论

感谢阅读这篇文章。你对 Nest 和这一系列技术有什么看法？你期待下一次午睡吗？

请在评论中告诉我。