# 停止对 Web 服务器使用 Express.js，考虑使用 NestJS

> 原文：<https://betterprogramming.pub/stop-using-express-js-to-make-web-servers-faed1942eaf3>

## 这就是为什么 NestJS 是更好的选择

![](img/49837b0f70e76b8375353d9d796992e3.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

如果您在 Google 上搜索如何在 Node.js 中构建 web 服务器，大多数教程都会推荐 Express.js，理由很充分——对于那些不熟悉 Node.js、Javascript、HTTP 服务器或这三者的人来说，它是一个灵活而简单的工具。然而，它的流行经常给新开发人员留下一个错误的概念，即 Express 是在 Node 中构建服务器的最佳方式。

实际上，这并不是一个很好的选择，尤其是对于企业应用来说。

如今——尤其是在 Node.js 领域——人们神秘地偏爱轻量级、非个人化的库，而不是包含电池的框架。这是为什么还不完全清楚。使用为个人设计选择留有余地的极简框架可能感觉很好，但是是什么让你的设计选择比那些经验丰富的软件工程师的设计选择更好呢？可能没什么。

非个性化框架迫使开发人员重新设计已经设计好的轮子。

这就是为什么高效的软件开发人员会利用现有的工具，这些工具已经在许多不同的用例中经过了多年的尝试和测试。

那么，有哪些替代的表达方式呢？

有像 Koa，Fastify，`hapi`这样的库。但是它们只共享 Express 采用极简、轻量级的方法——因此在提供更健壮的开箱即用的体系结构方面几乎没有帮助。

# 输入 NestJS

NestJS 是一个真正包含电池的框架。它是一个类型脚本优先的库，为模块化 web 服务器应用程序使用基于类的架构。它为 GraphQL 和 WebSockets 等工具提供官方支持，包括一流的文档。它的依赖注入架构改进了代码组织，简化了测试，增加了测试覆盖率。

这一切可能会让你想起 Angular，这是应该的，因为 NestJS 从 Angular 那里借鉴了很多设计。如果这让你感到害怕，那也不应该——但稍后会有更多的内容。

让我们从了解 NestJS 的一些常见的第一反应开始。

## “Javascript 中的类？Ew。”

我通常建议 Javascript 开发人员避免使用类，原因很简单:它们通常不是必需的，越简单越好。但是回想一下，NestJS 采用了依赖注入方法(稍后将详细介绍)，这几乎需要使用类——在 NestJS 中，它工作得非常好。

还怀疑？我也在这个时候。继续读。

## “打字稿？似乎太多了”

您*可以*将 NestJS 与普通的旧 Javascript 一起使用，但是我(和 NestJS 团队)强烈建议不要这样做。您将失去依赖注入的许多好处— [并且无论如何您都应该对 Node.js 项目使用 TypeScript。](/the-bad-reasons-people-avoid-typescript-and-the-better-reasons-why-they-shouldnt-86f8d98534de)

如果你还没有开始学习 TypeScript，但是有兴趣的话，这是一个很好的学习机会。

## “我还不如用 Java 构建一个服务器”

如果你习惯于轻量级库，很容易将任何较大的框架视为仅仅是在 Node.js 中模仿 Java 和 OOP 架构的尝试，毫无疑问，与 Express 这样的库相比，具有 OOP 背景的开发人员会更熟悉 NestJS。但是不要因为这个原因而放弃它，保持开放的心态，您将会看到 NestJS 使用的设计模式是如何令人惊讶的有意义，即使对于像 TypeScript 这样的非面向对象语言。

# NestJS 有什么好的？

诚然，这是一种后天养成的习惯，但也有很多东西值得喜爱。

## 模块性

无论你喜欢 OOP 还是函数式，类型脚本还是 Javascript，每个软件工程师都喜欢模块化。

现代 REST APIs 通常对每种数据模型类型都有不同的路径——例如，与文章相关的路由可能位于`/articles`，而用户的路由位于`/users`。因此，按模型分离代码很有意义。

这个*是 Express 中的*标准实践，但是它绝对是 NestJS 架构不可或缺的。一个 NestJS 应用程序由每种数据类型的模块组成——例如，`ArticlesModule`和`UsersModule`——它们都位于一个根`AppModule`下。

## 控制器和服务

所以，`ArticlesModule`处理与阅读和更新文章的路线和逻辑有关的一切。但是我们可以进一步分离处理路由和执行业务逻辑的不同关注点——这正是控制器和服务(分别)的用武之地。

`ArticlesModule`会有一个`ArticlesController`和一个`ArticlesService`。我们将从服务开始，它定义了对文章进行 CRUD 操作的方法。例如，在`ArticlesService`类中，我们可以定义一个通过 id 获取文章的方法:

```
getArticleById(articleId: string) {
  return this.db.articles.findById(articleId);
}
```

相比之下，控制器为每条`articles`路线定义了一种方法。每个方法指定路由的路径，处理路由参数和请求体，以及许多其他可能的职责。

例如，我们可以在`ArticlesController`中定义以下路线:

```
@Get('/:articleId')
getArticleById(@Param() params) {
  return this.articlesService.getArticleById(params.articleId);
}
```

这里需要注意一些事情:

*   你已经可以看出 NestJS 喜欢装修工(比如`@Get`和`@Param`)。它隐藏了各种常见行为的重复代码——在本例中，定义 GET routes 并从请求中提取参数。
*   当我们使用 NestJS CLI 生成一个控制器时(稍后会有更多介绍)，CLI 会为控制器添加前缀`articles`，因此每条路由都会自动添加前缀`/articles`。
*   控制器方法如何向客户端返回响应？这非常直观:方法返回的任何对象或值都将被转换成 JSON 并作为响应发送。
*   最后，您可能想知道控制器如何访问`ArticlesService`中的方法。这就是依赖注入的由来。

## 依赖注入

基本上 NestJS 中的所有东西都是类。这些类中有许多是相互依赖的——例如，正如我们在上面看到的，控制器通常需要访问同一个模块中的服务。有时，他们甚至需要从另一个模块访问服务。

依赖注入允许我们定义模块和它们的提供者(通常是像`ArticlesService`这样的类，但也可以是普通的旧函数或任何其他 Javascript 值)之间的依赖关系。

理解提供者的关键是:模块中的任何提供者都可以访问该模块中任何其他提供者的实例。模块中的控制器(不是提供者，但工作方式相同)也可以访问任何提供者。

例如，我们的`ArticlesModule`可能看起来像这样:

```
@Module({
  providers: [ArticlesService],
  controllers: [ArticlesController],
})
export class ArticlesModule {}
```

通过将服务指定为提供者，我们可以将服务注入控制器。那么依赖关系到底是怎么注入的呢？

与大多数基于类的依赖注入实现一样，NestJS 中的依赖是在构造函数中声明的。例如，如果我们希望我们的`ArticlesController`使用`ArticlesService`中的方法，我们只需将服务列为控制器构造函数的参数:

```
@Controller('articles')
export class ArticlesController {  
  constructor(**private articlesService: ArticlesService**) {} @Get('/:articleId')
  getArticleById(@Param() params) {
    return this.articlesService.getArticleById(params.articleId);
  }
}
```

然后我们可以在任何方法中使用`ArticlesService`的实例。当 NestJS 运行时发现`ArticlesController`需要一个`ArticlesService`来初始化时，它将在模块中搜索类型为`ArticlesService`的提供者。

## 但是为什么呢？

您可能会问为什么在 NestJS 中使用依赖注入。毕竟，您可以使用`import`或`require`关键字来调用不同类和文件之间的依赖关系。为什么所有这些花哨的构造函数和提供程序的东西？

有几个原因。

首先，它帮助你清楚地推理依赖关系。例如，构造函数会让你快速浏览每个类的重要函数依赖关系，而不是去搜索文件中无数的`import`来理解它们之间的关系。

其次，它改变了你对代码的思考方式。它迫使您将服务和控制器视为其依赖项之上的功能扩展。这鼓励你的代码更加整洁，更加模块化。

最后，也是最重要的，依赖注入极大地简化了测试。因为每个类都只是一个扩展其依赖项功能的接口，所以我们可以模拟那些依赖项的功能，并隔离出我们真正想要测试的东西——类本身的方法。

例如，为了测试我们的控制器，我们可以为它的方法编写一个简单的、可预测的实现的`MockArticlesService`类。然后，我们通过传入模拟类来初始化`ArticlesController`；这样，我们可以只测试控制器引入的代码层，而忽略服务的真正实现和任何其他依赖。

## CLI

对像 NestJS 这样的框架的一个普遍批评是，即使对于最简单的应用程序来说，过多的样板文件也是必要的。很高兴，像 Angular 一样，NestJS 包含了一个现成的命令行界面，可以很容易地为典型用例生成项目和文件。

要创建项目，请执行以下操作:

```
nest new myproject
```

这将为您创建一个默认的`AppModule`。要创建另一个模块:

```
nest g module users
```

这将在一个新的`users`文件夹中创建一个`UsersModule`。要创建服务:

```
nest g service users
```

CLI 足够智能，可以将`UsersService`放在`users`文件夹中，并自动将其指定为`UsersModule`的提供者。

点击阅读关于 CLI [的更多信息。](https://docs.nestjs.com/cli/overview)

## 精彩的文档

任何库或框架都只能和它的文档质量一样好。如果没有关于如何使用工具的有用指导——尤其是像 NestJS 这样复杂的框架——开发人员将无法实现其全部潜力。

NestJS 文档中有一页又一页详尽的、写得很好的教程、指南和对某些设计选择动机的解释。

## 对现代工具的一流支持

NestJS 的一个经常被忽视的好处是对 GraphQL、WebSockets、OpenAPI 等工具的官方支持。这使得将这些工具与您的服务器集成更加容易，消除了寻找最佳 npm 包的需要(并祈祷它有良好的文档记录)。

事实上，Nest 的高级文档也适用于这些工具。除了全面和准确的文档，NestJS 团队还编写了深入的教程和与这些工具集成的示例，如 GraphQL 和 WebSockets。

# 结论

毫无疑问——express . js*是一个很好的库(实际上，NestJS 是在它的基础上构建的)。但是它缺乏设计观点，这就要求你产生你自己的设计模式，这些设计模式几乎肯定不如那些由经验丰富的工程师设计和生产测试的框架。*

因此，对于您的下一个 web 应用程序，与其尝试重新设计轮子，不如我鼓励您尝试一下 NestJS。许多人，包括我自己，在第一次了解它的时候都有所保留——然而，以我的经验来看，这些人中的大部分都非常喜欢。

[NestJS docs](https://docs.nestjs.com/) 是一个开始的好地方。