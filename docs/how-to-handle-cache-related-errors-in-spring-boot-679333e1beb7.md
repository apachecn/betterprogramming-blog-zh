# 如何处理 Spring Boot 中与缓存相关的错误

> 原文：<https://betterprogramming.pub/how-to-handle-cache-related-errors-in-spring-boot-679333e1beb7>

## 保护您的 Spring Boot 应用程序免受缓存故障的影响

![](img/818668d3eb5d74643226955db38a41c8.png)

布莱克·康纳利在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片。

在过去的几个月里，我一直在 Spring Boot 处理缓存问题，并在许多项目中积极使用它。这给了我深入研究 [Spring Cache 抽象](https://docs.spring.io/spring-framework/docs/5.0.0.M5/spring-framework-reference/html/cache.html)的机会，理解它的优点和缺点。

通常，您的应用程序不太可能严重依赖缓存。事实上，您可能只是将缓存作为一种提高性能的方法。在这种情况下，即使出现与缓存相关的错误，您的应用程序也可能会顺利运行。因此，您可能甚至没有意识到缓存系统中的故障，这使得它们很难被发现。这就是为什么实现一个系统来正确处理与缓存相关的错误是至关重要的。

让我们看看如何在 Java 和 Kotlin 中实现这一点。

# 处理与缓存相关的错误

为了处理缓存故障，Spring Boot 提供了`[CacheErrorHandler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/interceptor/CacheErrorHandler.html)` [](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/interceptor/CacheErrorHandler.html)接口。通过实现它，您可以定义您想要的错误处理逻辑。然后，您只需将您的自定义实现注册为默认的错误处理程序。让我们来看看如何完成这两件事。

## 定义自定义错误处理逻辑

`[CacheErrorHandler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/interceptor/CacheErrorHandler.html)`接口提供了以下四种方法:`[handleCacheGetError](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/interceptor/CacheErrorHandler.html#handleCacheGetError-java.lang.RuntimeException-org.springframework.cache.Cache-java.lang.Object-)`、`[handleCachePutError](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/interceptor/CacheErrorHandler.html#handleCachePutError-java.lang.RuntimeException-org.springframework.cache.Cache-java.lang.Object-java.lang.Object-)`、`[handleCacheEvictError](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/interceptor/CacheErrorHandler.html#handleCacheEvictError-java.lang.RuntimeException-org.springframework.cache.Cache-java.lang.Object-)`、`[handleCacheClearError](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/interceptor/CacheErrorHandler.html#handleCacheClearError-java.lang.RuntimeException-org.springframework.cache.Cache-)`。每一个都旨在帮助您处理用`[@Cachable](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/annotation/Cacheable.html)`、`[@CachePut](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/annotation/CachePut.html)`或`[@CacheEvict](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/annotation/CacheEvict.html)`注释的方法中出现的错误，这些注释是最重要的 Spring Boot 缓存相关注释。

您需要做的只是实现`[CacheErrorHandler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/interceptor/CacheErrorHandler.html)`接口，在上述四种方法中提供错误处理逻辑。

Java:

科特林:

这样，您可以记录与缓存相关的错误，不再忽略它们，或者在出现致命异常时将它们发送回客户端。

## 注册您的自定义 CacheErrorHandler 实现

现在，您必须定义一个继承自`[CachingConfigurerSupport](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/annotation/CachingConfigurerSupport.html)`的`CustomachingConfiguration`类。覆盖它的`errorHandler`方法，并确保返回上面定义的`CustomCacheErrorHandler`类的一个实例。

Java:

科特林:

瞧啊！您的应用程序现在受到保护，不会出现与缓存相关的故障。

# 奖金

处理缓存可能会带来一些只有彻底清理才能解决的问题。这就是为什么你应该通过添加一些管理工具来保护自己，正如这里[所解释的](https://levelup.gitconnected.com/building-an-api-to-clear-all-the-caches-of-your-spring-boot-application-2d0dfdfe71b3)。

[](https://levelup.gitconnected.com/building-an-api-to-clear-all-the-caches-of-your-spring-boot-application-2d0dfdfe71b3) [## 构建一个 API 来清除 Spring Boot 应用程序的所有缓存

### 通过按需清除所有缓存数据来避免一致性问题

levelup.gitconnected.com](https://levelup.gitconnected.com/building-an-api-to-clear-all-the-caches-of-your-spring-boot-application-2d0dfdfe71b3) 

此外，您不应该让您的缓存增长太多。这就是为什么[向 Spring Boot 缓存添加压缩](/how-to-add-compression-to-caching-in-spring-boot-d4d21533167c)可能变得不可避免。

[](/how-to-add-compression-to-caching-in-spring-boot-d4d21533167c) [## 如何在 Spring Boot 的缓存中添加压缩

### 释放您的缓存

better 编程. pub](/how-to-add-compression-to-caching-in-spring-boot-d4d21533167c) 

# 结论

处理缓存是一件棘手的事情，您甚至可能意识不到错误的发生或者无法复制它们。这就是为什么您应该采用一种方法来优雅地处理与缓存相关的错误。正如我们刚刚看到的，在您的 Spring Boot 应用程序中向缓存系统添加错误处理逻辑并不复杂，它可以让您避免将来的麻烦。

感谢阅读！我希望这篇文章对你有所帮助。如果有任何问题、意见或建议，请随时联系我。