# 用 Spring Boot 软件开发自定义库

> 原文：<https://betterprogramming.pub/developing-custom-libraries-with-spring-boot-starter-cf463a5eca39>

## 如何使用 Spring Boot 入门框架开发自定义库的分步说明

![](img/2c8be2d72481da4785335cd7bd645f3b.png)

作者照片

# 为什么选择自定义库？

似乎是一个有明显答案的问题。但是在微服务领域，有很多关于为什么或者为什么不使用定制库的讨论。这里有几个值得一提的要点:

*   DRY(不要重复自己)原则鼓励我们创建可重用的代码。我们将重复的代码提取到抽象中，比如自定义库，然后我们可以从多个地方调用它。对共享逻辑的更改发生在一个库中，而不是在所有调用该共享逻辑的地方。定制库是微服务间代码复制的关键解决方案。
*   如果共享代码的使用泄漏到我们的服务边界之外，我们将会引入一种潜在的耦合形式，这在微服务世界中是不可取的，因为它的原则是建立在低耦合和高内聚的基础上的。
*   一些人认为“服务间过多耦合的弊端比代码复制引起的问题更糟糕。”(引自山姆·纽曼的书*构建微服务*)。

# 如何最好地设计定制库？

*   不要在定制库中编写任何特定于领域的代码。
*   即使是与业务相关的共享代码也不应该在定制库中。
*   想想共同点。思考抽象。想想卑鄙的行为。这些是定制库的候选对象。

# 为什么是 Spring Boot 首发？

Spring Boot 启动库是 Spring Boot 应用程序的一些主要构件。作为 Spring Boot 开发者，我们都已经习惯了一些我们最喜欢的启动库，比如`spring-boot-starter-web`、`spring-boot-starter-data-jpa`、`spring-boot-starter-actuator`等等。在这个故事中，我们将探索 Spring Boot 启动器框架来创建我们自己的自定义启动器库。使用 Spring Boot 启动器作为我们的定制启动器库框架有很多好处:

*   与 Spring Boot 的自动配置更加无缝集成。
*   更全面地利用成熟的 Spring Boot 启动框架，使其成为 Spring Boot 的延伸。
*   在我看来，最大的优势是 Spring Boot 启动框架能够提供自定义库的默认行为，并且仍然允许调用自定义库的微服务用自定义行为覆盖默认行为。仅这个特性就在很多方面解决了定制库导致微服务间耦合的困境。
    是的，通过 Spring Boot 启动器提供的出色框架，我们可以拥有定制库，如果需要，还可以选择在微服务中定制这些库的逻辑，从而减少耦合。

# 如何使用 Spring Boot 启动建立自定义库？

Spring Boot 让我们通过使用 Spring Boot 自动配置来构建我们自己的定制库变得非常容易。遵循几个简单的步骤:

# 步骤 1:选择多模块还是单模块

根据 [Spring 的文档](https://docs.spring.io/spring-boot/docs/2.6.7/reference/htmlsingle/#features.developing-auto-configuration.custom-starter)，定制启动器可以包含以下内容:

*   包含库逻辑自动配置代码的`autoconfigure`模块。
*   为`autoconfigure`模块提供依赖关系的`starter`模块，以及库逻辑和任何通常有用的附加依赖关系。简而言之，添加 starter 应该提供开始使用该库所需的一切。

这种分成两个模块的方式是不必要的。如果库逻辑有几种风格、选项或可选特性，最好将自动配置分开，因为这样可以清楚地表达一些特性是可选的这一事实。

此外，您有能力制作一个提供关于那些可选依赖项的观点的启动器。与此同时，其他人只能依靠`autoconfigure`模块，用不同的观点来制作自己的启动器。

如果自动配置相对简单，并且没有可选特性，那么在 starter 中合并这两个模块无疑是理想的，因为它简化了库结构。

基于这个指导方针，我们可以建议，对于我们的定制库，如果库逻辑非常简单，我们应该坚持只创建一个模块。让我们简化，而不是过度工程化。对于可能需要多个实现的特殊库场景，建议采用上面提到的两模块方法。

# 步骤 2:选择库名

Spring 关于自定义库命名约定的指导方针是，我们应该首先以我们的库名开始命名，然后是“`-spring-boot-starter`”。因此，例如，对于一个样本定制库，我们应该将我们的工件 id 命名为“`sample-spring-boot-starter`”。原因是，如果我们以“`spring-boot-starter-`”开始我们的库名，它很有可能与现成的 Spring Boot 初学者库或他们可能提供的任何潜在的新初学者发生冲突。

确保在 pom 中指定库的工件 id，例如:

# 步骤 3:POM . XML 中的变化

除了为定制库添加任何特定的依赖项之外，确保 pom `parent`被指定，注意我们使用的是`spring-boot-starter-parent`版本`3.0.0-M5`，这是撰写本文时最新的 boot 3 里程碑版本:

另外，确保添加`spring-boot-autoconfigure`依赖关系:

# 步骤 4:创建定义定制库中主要逻辑的服务层

这是您的详细库逻辑实现，无论是在服务层、配置层还是任何其他层。这是我们想要提取到自定义库中的共享代码。

# 步骤 5:创建自定义自动配置类

这一步，结合第 6 步，是我们的定制库如何成为一个定制 Spring Boot 启动器的关键。

示例代码如下:

*   `@ConditionalOnClass`注释让`SampleService`类基于`ApplicationContext`中`SampleService`类的存在而被包含。
*   `@ConditionalOnMissingBean`注释表明如果`ApplicationContext`中还没有包含`SampleService`类型的 bean，那么将创建`sampleService` bean。

# 步骤 6:添加 spring.factories 来反映这个定制的自动配置类

`spring.factories`文件是 Spring Boot 在启动时自动加载的文件。Spring Boot 检查 jar 文件中的`META-INF`目录下是否存在`spring.factories`文件。该文件应该在`EnableAutoConfiguration`键下面列出您的配置类。

对于我们的示例，我们指示 Spring Boot 将我们的`SampleAutoConfiguration`类作为`EnableAutoConfiguration`的候选类加载到我们的`META-INF/spring.factories`中:

```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.github.wenqiglantz.library.sample.config.SampleAutoConfiguration
```

一旦我们的自定义启动器库被创建，我们就可以开始在需要调用我们的自定义启动器库的微服务中使用它，只需添加一个引用我们的新自定义启动器库的新依赖项，就像我们如何添加任何常规的 Spring Boot 启动器库一样。

定制 Spring Boot 启动器的样本可以在 GitHub 上找到[。此示例用作要创建的新自定义库的模板。它除了提供上述所有功能外，还提供:](https://github.com/wenqiglantz/sample-spring-boot-starter)

*   确保编码风格自动化的 checkstyle maven 插件
*   确保单元测试覆盖率的 jacoco maven 插件
*   依赖性检查插件，以确保我们的库中不包含有漏洞的第三方库

# 依赖层次结构

该示例服务库的完成的依赖关系层次结构如下所示:

![](img/c0d90d760a9bf9c42815e75be6116793.png)

# Monorepo 存放自定义库

谷歌、脸书和优步等工程领先的公司已经将 monorepos 作为其基础设施的核心部分，引发了一阵采用热潮。让我们来看看使用 monorepo 来存放我们的定制库的优缺点。

## 赞成的意见

*   跨团队更好的可见性和协作。Monorepo 使定制库更容易被发现和利用。这鼓励协作和代码重用，这可以减少技术债务。
*   简化的依赖性管理。Monorepos 有助于简化内部和第三方依赖性管理。它有助于解决挑战，如[钻石依赖问题](https://jlbp.dev/what-is-a-diamond-dependency-conflict#:~:text=A%20diamond%20dependency%20conflict%20is,features%20that%20the%20consumers%20expect.)以及多个项目具有相同依赖的多个版本的问题。
*   大规模代码重构。monorepos 允许我们通过一次提交来处理利用特性的服务，而不是必须在多个项目中进行多次 PR 来重构一个特定的特性。
*   简化版本。当构建一个新版本时，我们为所有的库创建相同的版本，所以当使用这些库时，我们可以为所有的库创建一个单一的版本。

## 骗局

*   修建管道。对于拥有大型代码库的团队来说，确保您的构建是一个挑战。脸书和谷歌都开发了自己的构建工具(分别是 Buck 和 Bazel ),来帮助管理他们庞大的 monorepos 的构建过程。幸运的是，Buck 和 Bazel 都是开源的，所以如果您使用 monorepo，就可以利用它们。
*   VCS 工装挑战。拥有大量代码和数百万次提交的组织可能会面临 git 性能下降的问题。
*   关于访问控制的限制。有了 monorepo，开发人员将可以看到其中的所有代码。限制对特定图书馆的访问并不容易。
*   开源漏洞优先级和许可。尽管 monorepos 可以使管理开源依赖关系变得更容易，但它们可能会使漏洞修复的优先级排序和生成特定于产品的属性报告的过程变得复杂。您需要将软件组合分析(SCA)或开源管理软件与构建系统集成起来，以了解用于构建每个产品的文件和依赖关系。

# 摘要

在这个故事的开始，我们探讨了支持和反对定制库的论点。然后我们看了为什么 Spring Boot 入门框架非常适合开发定制库。接下来，我们查看了使用 Spring Boot 启动器开发定制库的一步一步的说明。

示例代码可以在 [my GitHub repository](https://github.com/wenqiglantz/sample-spring-boot-starter) 中找到。

编码快乐！

# 参考

[Spring Boot 参考文献](https://docs.spring.io/spring-boot/docs/2.6.7/reference/htmlsingle/#features.developing-auto-configuration)

[微服务共享库——设计和最佳实践](https://medium.com/duda/shared-libraries-design-and-best-practices-710774ae0bdc)

[使用 Monorepos — FOSSA 的利与弊](https://fossa.com/blog/pros-cons-using-monorepos/)