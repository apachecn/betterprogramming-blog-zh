# 如何遵循 NestJS 和 Angular 中的依存倒置原则

> 原文：<https://betterprogramming.pub/how-to-follow-the-dependency-inversion-principle-in-nestjs-and-angular-8d344303dc3b>

## 保持你的模块独立，抽象无细节

![](img/504c972c7162afd1ab22d2a3e058c930.png)

作者照片。

在 [NestJS 文档](https://docs.nestjs.com/#philosophy)中，我们可以找到下面这句话:

> “Nest 提供了开箱即用的应用程序架构，允许开发人员和团队创建高度可测试、可伸缩、松散耦合且易于维护的应用程序。”

让我们通过检查 Nest 是否允许我们容易地遵循一个可靠的原则来测试这个声明:依赖倒置原则。

Nest 深受 Angular 的启发，因此本文中的所有食谱也适用于 Angular。

快速提醒一下，[原则](https://web.archive.org/web/20150906155800/http://www.objectmentor.com/resources/articles/Principles_and_Patterns.pdf)规定:

1.  高层模块不应该依赖低层模块。两者都应该依赖于抽象(例如接口)。
2.  抽象不应该依赖于细节。细节(具体的实现)应该依赖于抽象。

# 例子

为了解释如何实现 DIP，让我们首先考虑下面的例子。
我们创建了一个应用程序，它将分析关于 GitHub 平台上的存储库的数据。我们当前的任务是实现一个端点，它将返回给定存储库的活动 pull 请求的数量。

该功能可以快速实现，如下所示，但它不符合我们的原则:

即使将 HTTP 调用转移到专用服务，也不是对控制器隐藏实现的适当方式:

# 依赖倒置原则的实现

正确的实现应该是这样的:

1.  我们需要创建一个抽象层来获取拉请求的集合。这个抽象是通过`RepositoryService`抽象类实现的。
2.  对于`AppController`，我们试图注入一些被`RepositoryService`注入令牌隐藏的东西。
3.  `GithubInfrastructureModule`说在那个地方，应该提供一个`GithubRepositoryService`级。

好吧，但是为什么我们不能使用一个普通的接口作为抽象层呢？

答案是在从 TypeScript 到 JavaScript 的转换过程中接口会发生什么。问题是，我们将丢失所有关于`RepositoryService`接口存在的信息以及应该注入到`AppController`中的信息，而且基本上，不可能在模块提供者内部将接口作为属性值传递。

上课的时候情况就完全不一样了。即使是 transpilation 后的抽象类，也会是一个普通的类，可以作为注入令牌使用。在 TypeScript 中，我们可以通过与接口相同的另一个类来实现一个类。

# 为什么这如此重要？

假设我们的应用程序的需求发生了变化。我们还需要支持存储在 Bitbucket 上的存储库作为专用的应用程序实例。如果我们没有更早地添加它，现在我们将不得不在我们的服务和控制器中添加许多`ifs`来为所需的数据准备一个适当的 HTTP 调用。

有了这个优雅隐藏的数据源层，我们就可以为 Bitbucket 的服务创建一个专用模块，并正确地导入我们的特征模型，如下所示:

现在`GithubRepositoryService`或`BitbucketRepositoryService`将根据环境注入到`AppController`中，外层代码没有任何变化。

在角度应用中，这一点适用于以下情况:

*   我们正在创建 web 和移动应用程序，它们以不同的方式加载数据。
*   我们正在用 SSR 构建一个应用程序。在服务器端接收数据的同时，我们也想将它们保存在`TransferState`中。而在浏览器端，我们希望从它那里获得。

在这两种情况下，遵循依赖倒置原则将帮助我们实现并减少使用我们的数据访问层的高级模块中的更改和逻辑。唯一的区别是，我们基本上不应该依赖环境来决定应该导入哪个模块。

# 摘要

遵循坚实的原则会给我们带来很多好处。此外，它们使我们的系统可重用、可维护、可伸缩、可测试，等等。Nest 和 Angular 允许我们以一种简单而优雅的方式使用它们。

如果你对我们如何在同一个应用实例中使用`GithubRepositoryService`和`BitbukcetRepositoryService`感兴趣，请查看[这个报告](https://github.com/valueadd-poland/pimp-my-pr/blob/2.0.1/libs/server/repository/core/application-services/src/lib/queries/get-repository-statistics/get-repository-statistics.handler.ts#L27)。

毕竟 Nest 的文档里的说法可以认为是一个真理！