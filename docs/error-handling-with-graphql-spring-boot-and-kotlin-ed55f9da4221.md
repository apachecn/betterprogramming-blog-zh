# 用 GraphQL、Spring Boot 和科特林处理错误

> 原文：<https://betterprogramming.pub/error-handling-with-graphql-spring-boot-and-kotlin-ed55f9da4221>

## 使用 Spring Boot 和 Kotlin 对 GraphQL 错误和异常建模

![](img/4e447dd88b9e5f788a0bc0b31e412be0.png)

Georgie Cobbs 在 [Unsplash](https://unsplash.com/s/photos/work?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 问题

GraphQL 中的错误处理可能相当棘手，因为响应总是有 HTTP 状态`200 OK`。如果请求失败，JSON 有效负载响应将包含一个名为`errors`的根字段，其中包含失败的详细信息。

对于类似下面的查询…

…如果没有适当的错误处理，响应将如下所示:

这一点帮助都没有——回复并没有告诉你到底哪里出了问题。

# 解决办法

## 1.设置

让我们用 GraphQL 建立一个 Spring Boot 项目。此外，代码可以在[这里](https://github.com/maniish-jaiin/graphql-error-handling)找到。

转到[https://start.spring.io/](https://start.spring.io/)。

选择一个`Gradle`项目，设置语言为`Kotlin`。

对于最低限度的实现，您不必选择任何依赖项。

下载 ZIP 文件，并在您喜欢的 IDE 中打开它。

跳转到`build.gradle.kts`，并添加:

这些是您开始使用第一个端点所需的 GraphQL 库。

## 2.没有错误处理的端点

让我们创建两个端点来创建和获取资源。

添加`Service`提供`car`的创建和取数。

我们已经创建了一个`function`来创建一个`car`和另一个以`name`作为输入来取回`car`。但是如果没有找到汽车，这将抛出一个异常，并转化为如下所示的响应:

## 3.实现错误处理

为了能够在错误或异常的情况下提供一些合理的东西，我们将添加更多的代码。

让我们明白这是怎么回事。

1.  有一个消息字段，不言自明。
2.  被覆盖的函数`getExtensions()`用于提供参数映射。这可能是不同的属性，如您希望传递的错误的`ErrorCodes`。
3.  被覆盖的函数`getErrorTypes()`用于提供错误的分类，例如`DataFetchingException`或`ValidationException`。

在上面的`exception`中，你可以看到它是从`GraphQLException`延伸出来的，是我们的习俗`CarNotFoundException`和`GraphQLError`之间的桥梁。

现在我们要加上`GraphQLException`。

当我们添加它时，我们需要确保创建一个单独的 Java 模块，并在 Java 模块中添加那个`GraphQLException`。

为什么？

因为一个 Kotlin 类不能同时扩展`RuntimeException`和实现`GraphQLError`。原因可以在[这里](https://github.com/graphql-java/graphql-java/issues/1022)找到。

所以现在我们创建一个`GraphQLException`类。

从`CarService`开始，当没有找到`car`时，我们抛出异常。

有了这一改变，响应将包含比以前更多的细节。

# 结论

你可以在这里找到完整的实现。

我很高兴你坚持到了最后。说明你对 GraphQL 感兴趣。下面是另一篇[文章](https://levelup.gitconnected.com/6-months-of-using-graphql-faa0fb68b4af)，分享我使用 GraphQL 六个月的经验。