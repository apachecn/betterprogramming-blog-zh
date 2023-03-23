# 使用 AOP Loggers 在 Spring Boot 编写优雅简洁的日志代码

> 原文：<https://betterprogramming.pub/write-elegant-and-clean-logging-code-in-spring-boot-using-aop-loggers-6e44fb2e7552>

## 与其编写大量难以阅读的日志样板代码，不如在 Spring Boot 使用面向方面编程(AOP)

![](img/5b4df241a265be592c1820aff6722c8f.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我是`Logger.debug`的忠实粉丝，我喜欢记录尽可能多的信息。另一方面，我也是`DRY`和`KISS` 原则的严格追随者。当我看到代码中的重复时，我感到强烈的焦虑。结合这些激情并不容易。很难不重复地记录所有的事情。即使这是可能的，通常也会使你的代码难以阅读。

让我们假设你有这个除法。一切看起来简单易读。

然后，您开始在它周围添加日志，以便您可以查看发生了什么。您记录收到的参数和返回值。您应该在整个代码中放置一个 try/catch 来记录异常。如果这是你所关心的，你可能想要记录消耗的时间。

在您意识到之前，您已经用大量非功能性代码扩展了您的单行代码。你的代码变得难以阅读。更糟糕的是，当你开始在代码的所有地方都这样做的时候。

我们应该使用 Spring Boot 的面向方面编程(AOP)来解决我们的日志横切关注点，而不是编写大量难以阅读的日志样板代码。AOP 是 Spring 框架的核心组件之一。AOP 提供了使用简单的可插拔配置在实际逻辑之前、之后甚至周围添加横切关注点的方法。它使得现在和将来维护代码变得容易。

我们可以很容易地找到许多指南，展示如何通过创建我们的定制注释和建议来实现 AOP 日志记录。因此，我将引入一个现成的库`im-aop-loggers`来完成这项工作，而不是重复所有这些。

`im-aop-loggers`是一套方便且可配置的基于注释的 Spring Boot 记录器，它可以在正常或异常进入或退出时记录方法的每次执行，而无需您编写一行样板代码。

在 Spring Boot 项目中使用`im-aop-loggers`的第一步是将依赖项添加到项目的 pom.xml 文件中。你可以在 [Maven 中心库](https://search.maven.org/artifact/com.github.andylke/im-aop-loggers)找到`im-aop-loggers`的最新版本。

一旦有了项目设置，就可以开始向方法中添加 AOP 注释了。

根据您的需要，您可能只需要通过在您的方法上添加`@LogBefore`注释来记录您的方法收到了什么。

您还可以通过在方法上添加`@LogAfterReturning`注释，选择只在方法正常退出时写日志。

您也可以通过在方法上添加`@LogAfterThrowing`注释，选择只在方法遇到异常时写日志。

更好的是，在编写日志时，您可以通过在`@LogAfterThrowing`注释上添加`ignoreExceptions` 属性来选择忽略某种类型的异常。

对于全面的日志记录，也就是您想要记录的所有内容，请使用`@LogAround`注释。

通过在`@LogAround`注释上添加`elapseTimeLimit` 和`elapseTimeUnit` 属性，您还可以选择在您的方法花费了比预期持续时间更长的时间来完成时记录一条警告消息。

您可以从 [GitHub](https://github.com/andylke/im-aop-loggers-sample) 下载示例源代码，并使用以下 maven 命令运行应用程序。

您应该会在您的控制台中看到如下内容。

您可以在`application.properties`中为每个记录器配置默认日志级别和消息。

如果出于某种原因，您需要禁用所有这些事件记录，您可以在`application.properties`中将`enabled`属性设置为`false`。

你可以参考 [GitHub](https://github.com/andylke/im-aop-loggers) 获取关于`im-aop-loggers`的更多文档。感谢阅读。希望你会发现这是有用的。