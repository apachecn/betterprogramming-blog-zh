# Spring Boot 和科特林基于环境的错误处理

> 原文：<https://betterprogramming.pub/environment-based-error-handling-with-spring-boot-and-kotlin-b36b901135ad>

## 简化登台环境中的调试

![](img/0a352f5f801982589fc2ea7a1ddf585b.png)

埃里克·麦克林在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

在处理 web 服务中的错误时，提供简单但有意义的消息应该是首选方法。这样，我们将不会暴露任何关于错误原因的内部信息，并且我们将帮助 API 客户端正确地响应问题。

[当错误发生](https://medium.com/quick-code/what-a-recurring-outofmemory-error-taught-me-11f2061063a1)时，默认的 Spring Boot 行为是返回一个[堆栈跟踪](https://en.wikibooks.org/wiki/Java_Programming/Stack_trace)。这种方法的主要缺点是，它可能会将有关我们实现的有用信息泄露给潜在的攻击者。另一方面，与有意义的消息不同，堆栈跟踪对于调试极其重要。

> 堆栈跟踪可以告诉开发人员更多关于导致失败的事件顺序，而不仅仅是错误发生时软件的最终状态。不幸的是，同样的信息可能对攻击者有用。堆栈跟踪中的类名序列可以揭示应用程序的结构以及它所依赖的任何内部组件。— [塞姆勒](https://help.semmle.com/wiki/display/JAVA/Information+exposure+through+a+stack+trace)

我们将要介绍的方法的主要目标是根据当前的[部署环境](https://en.wikipedia.org/wiki/Deployment_environment)自动调整错误消息。在暂存环境中，将返回一条简单而有意义的消息以及一个堆栈跟踪。在生产环境中，我们将只公开第一个。

让我们看看这是如何在 [Spring Boot](https://spring.io/projects/spring-boot) 和[科特林](https://kotlinlang.org/)中实现的。

# 处理不同的部署环境

为了理解我们所处的部署环境，我们将使用一个名为`ENV`的自定义[环境变量](https://docs.oracle.com/javase/tutorial/essential/environment/env.html)。我们假设它的值在生产环境中是`PRODUCTION` ，在暂存环境中是`STAGING` 。在 Kotlin 中，可以按如下方式检索环境变量值:

```
[System.getenv](https://docs.oracle.com/javase/8/docs/api/java/lang/System.html#getenv-java.lang.String-)("env_name")
```

# 定义自定义错误类

我们将定义一个名为`ErrorMessage`的自定义类来表示 API 错误。这个类的目标是将异常封装在一个漂亮的 JSON 表示中，使 API 客户端的工作变得更容易。

我们可以如下实现这样一个类:

正如您所注意到的，`stackTrace`属性是可空的和可选的，因为它将只在登台环境中使用。

# 基于 Spring Boot 环境的错误处理

使用`[@ExceptionHandler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/ExceptionHandler.html)`和`[@ControllerAdvice](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/ControllerAdvice.html)`是 Spring Boot 3.2 以来处理错误的主要方法之一。

> "`@ExceptionHandler`是一个 Spring 注释，它提供了一种机制来处理处理程序(控制器操作)执行期间抛出的异常。如果在控制器类的方法上使用这个注释，它将作为处理这个控制器中抛出的异常的入口点。总之，最常见的方法是在`@ControllerAdvice`类的方法上使用`@ExceptionHandler`，这样异常处理将被全局应用或应用于控制器的子集。”— [总计](https://www.toptal.com/java/spring-boot-rest-api-error-handling)

多亏了它们，我们可以构建一个名为`ControllerExceptionHandler`的全局错误处理组件。它的目标是捕捉异常并将它们包装在`ErrorMessage`对象中，这些对象将被序列化为 JSON 并发送回 API 客户端。它还实现了基于环境的逻辑，如下所示:

最重要的方法是`generateResponse`，它将异常堆栈跟踪转换为一个字符串(如本课程中的[所述)，并仅在不在生产环境中时将其传递给`ErrorResponse`构造函数。](https://www.baeldung.com/java-stacktrace-to-string#conversion-with-core-java)

分别在生产和试运行环境中的相同错误响应示例。

这样，`message`属性可以用来解释用户发生了什么。在 staging 环境中，由于有了`stackTrace`属性，开发人员可以理解为什么会出现错误。

# 额外的

本教程可以用来构建一个定制的错误处理层，添加到我们在本文的[中介绍的多层架构中。](https://medium.com/swlh/designing-a-multi-layered-architecture-for-building-restful-web-services-with-spring-boot-and-a12ef85b77c9)

[](https://medium.com/swlh/designing-a-multi-layered-architecture-for-building-restful-web-services-with-spring-boot-and-a12ef85b77c9) [## 用 Spring Boot &科特林设计多层架构来构建 RESTful Web 服务

### 让我们建立一个超过五层的高级架构

medium.com](https://medium.com/swlh/designing-a-multi-layered-architecture-for-building-restful-web-services-with-spring-boot-and-a12ef85b77c9) 

# 结论

无法理解错误发生的原因会令人沮丧！公开详细的错误描述可能是危险的。然而，有一种方法可以谨慎地面对这个问题。使用上面的方法，当错误发生时，用户总是会看到一条简单的消息。但是当在登台环境中时，开发人员将拥有调试它所需的一切。

就这些了，伙计们！我希望这能帮助你处理 Spring Boot 和科特林的错误。如果你有任何意见或建议，请告诉我。