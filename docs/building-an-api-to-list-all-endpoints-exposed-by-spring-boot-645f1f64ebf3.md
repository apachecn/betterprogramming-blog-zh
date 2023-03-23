# 构建一个 API 来列出 Spring Boot 公开的所有端点

> 原文：<https://betterprogramming.pub/building-an-api-to-list-all-endpoints-exposed-by-spring-boot-645f1f64ebf3>

## 不再迷失在你的后端

![](img/790d70e734873b3515385ae5f5affa6d.png)

由[戈兰·艾沃斯](https://unsplash.com/@goran_ivos?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

通过与后端打交道，我了解到您很容易忽略正在发生的事情或当前部署的内容。这可能会成为一个问题，尤其是在处理生产环境时。

有很多库可以解决这个问题，我个人推荐 [Spring Boot 执行器](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)。

> “Spring Boot 包括许多附加功能，可帮助您在应用程序投入生产时对其进行监控和管理。您可以选择使用 HTTP 端点或 JMX 来管理和监视您的应用程序。审计、健康和指标收集也可以自动应用到您的应用程序中。”— [Spring 文档](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)

`[spring-boot-actuator](https://github.com/spring-projects/spring-boot/tree/v2.4.1/spring-boot-project/spring-boot-actuator)`模块提供了上述 Spring Boot 的所有生产就绪特性，帮助您监控和管理您的应用。

然而，本教程的目标不是向您展示如何利用 Spring Boot 致动器。事实上，对于简单的应用程序，单个监控 API 应该足够了。

根据我的经验，第一件也是最重要的事情是**所有暴露端点的列表**。可能有一些您不记得或不使用的 API 仍然在线，并且可能对任何人都可用。出于安全原因，应该避免这样做，尤其是在这些 API 被否决的情况下。

让我们看看如何定义一个自定义 API，返回使用 Spring Boot 应用程序公开的所有端点的列表。

# 构建 API

在网上寻找检索 Spring Boot 应用程序中所有已部署端点的方法时，我发现了[*requestmappingchandlermapping*](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/mvc/method/annotation/RequestMappingHandlerMapping.html)的存在。

这个类被 Spring Boot 用来执行每一个用`[*@RequestMapping*](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/RequestMapping.html)` 标注的方法，并包含一个所有方法的列表，具体来说，就是你的应用程序所暴露的所有端点的列表。

这就是为什么实现这样一个 API 比你想象的要容易。这可以通过以下方式实现:

## Java 语言(一种计算机语言，尤用于创建网站)

## 科特林

在这两种情况下，我都使用了`[handlerMethods](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/handler/AbstractHandlerMethodMapping.html#getHandlerMethods--)`属性，它存储了一个包含所有映射和 [HandlerMethod](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/method/HandlerMethod.html) 的只读映射。他们的`[toString()](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/mvc/method/RequestMappingInfo.html#toString--)`方法返回到达与 [HandlerMethod](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/method/HandlerMethod.html) 对象关联的端点所需的所有内容，具体来说，就是用`*@RequestMapping*` *注释的方法。*

这个 API 的响应示例如下:

```
[
   "{GET /monitoring/endpoints}",      
   "{GET /v1/genres}",    
   "{GET /v1/genres/{id}}",
   "{POST /dex/v1/genres}", 
   "{PUT /v1/genres/{id}}",
   "{DELETE /v1/genres/{id}}",     
   "{PATCH /v1/genres/{id}}", 
   "{GET /v1/books}",    
   "{GET /v1/books/{id}}",
   "{POST /dex/v1/books}", 
   "{PUT /v1/books/{id}}",
   "{DELETE /v1/books/{id}}",     
   "{PATCH /v1/books/{id}}", 
   "{GET /v1/authors}",    
   "{GET /v1/authors/{id}}",
   "{POST /dex/v1/authors}", 
   "{PUT /v1/authors/{id}}",
   "{DELETE /v1/authors/{id}}",     
   "{PATCH /v1/authors/{id}}", 
   "{GET /v1/authors/{id}/books}"    
]
```

# 奖金

只有少数选定的用户应该被允许访问[这种 API](https://levelup.gitconnected.com/building-an-api-to-clear-all-the-caches-of-your-spring-boot-application-2d0dfdfe71b3) 。

[](https://levelup.gitconnected.com/building-an-api-to-clear-all-the-caches-of-your-spring-boot-application-2d0dfdfe71b3) [## 构建一个 API 来清除 Spring Boot 应用程序的所有缓存

### 通过按需清除所有缓存数据来避免一致性问题

levelup.gitconnected.com](https://levelup.gitconnected.com/building-an-api-to-clear-all-the-caches-of-your-spring-boot-application-2d0dfdfe71b3) 

这就是为什么我建议实现一个定制的身份验证系统。如需进一步阅读，您可以查看我以前的文章，其中我展示了如何在 Spring Boot 实现这样的目标。

[](https://medium.com/better-programming/how-to-implement-custom-token-based-authentication-in-spring-boot-and-kotlin-5b59b55c1de2) [## 如何在 Spring Boot 和科特林实现自定义的基于令牌的身份验证

### 构建更安全的 Spring Boot 应用程序

medium.com](https://medium.com/better-programming/how-to-implement-custom-token-based-authentication-in-spring-boot-and-kotlin-5b59b55c1de2) [](https://levelup.gitconnected.com/how-to-implement-basic-access-authentication-in-spring-boot-eaded2e33d19) [## 如何在 Spring Boot 实现基本接入认证

### 让您的 Spring Boot API 更加安全

levelup.gitconnected.com](https://levelup.gitconnected.com/how-to-implement-basic-access-authentication-in-spring-boot-eaded2e33d19) 

# 结论

迷失在后端的复杂性或广阔性中是很常见的。这也正是你要通过增加一些监控工具来保护自己的原因。正如我所展示的，实现一个列出所有端点的 API 并不复杂，对于简单的系统来说，这可能就足够了。

感谢阅读！我希望这篇文章对你有所帮助。如有任何问题、意见或建议，请随时联系我。