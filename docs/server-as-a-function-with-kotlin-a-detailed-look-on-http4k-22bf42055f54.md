# Kotlin 的服务器功能——http 4k 详解

> 原文：<https://betterprogramming.pub/server-as-a-function-with-kotlin-a-detailed-look-on-http4k-22bf42055f54>

## 编写只包含函数的高性能轻量级 HTTP 服务器

![](img/20c88c71aa1c895c9bba85e7e55b1a94.png)

丹尼斯·简斯在瑞士军刀上的照片

我们知道的许多服务器框架都很笨重，固执己见，有时很难调试，并且不是特别有效。幸运的是，有非常有趣的选择。在今天的文章中，我想和你谈谈“服务器作为一种功能”的概念我们将了解服务器、镜头、客户端、无服务器以及如何测试 http4k 应用程序。

这个想法是我们仅仅基于普通的功能来编写服务器应用程序。这是一种轻量级的方法，基于 Marius Eriksen(Twitter)撰写并发表的论文 [Your Server as a Function](https://monkey.org/~marius/funsrv.pdf) 中概述的概念。在 Kotlin 世界中，这个概念最突出的实现是 [http4k](https://github.com/http4k/http4k) 。http4k 工具包被描述为“用 Kotlin 编写的 http 工具集，重点是创建简单的、可测试的 API。”最重要的一点是，http4k 应用程序只是我们可以直接使用的 Kotlin 函数。

给你一个想法，这里有一个简单的例子。

# 第一个 http4k 服务器示例

这段代码展示了一个功能完整的 http4k 应用程序，它由一个 Kotlin 函数`app`组成，我们将这个函数嵌入到一个`SunHttp`服务器中，这是我们可以选择的可用服务器实现的一个例子。注意这里的类型`HttpHandler`,它代表了作为功能的服务器的两个基本部分之一:

1.  HttpHandler ( `(Request) -> Response`):通过将 HTTP 请求映射到响应来将 HTTP 请求处理成响应的抽象。
2.  Filter ( `HttpHandler -> HttpHandler`):向一个`HttpHandler`添加预处理和后处理的抽象，比如缓存、调试、认证处理等等。过滤器是可组合/堆叠的。

每个 http4k 应用程序都可以由`HttpHandler`和`Filter`组合而成，两者都是普通 Kotlin 函数类型的简单类型别名。如果我们不把 Kotlin 标准库算作一个的话，Http4k 是零依赖的。

因为纯粹形式的 http4k 应用程序只需要一些嵌套的 Kotlin 函数；不涉及反射或注释处理。因此，http4k 应用程序可以超级快速地启动和停止，这使它们成为部署在功能即服务环境中的合理候选对象(例如，与 Spring Boot 应用程序相反，后者被认为更重)。

# 更高级的 http4k 应用

让我们看一个更高级的 http4k 服务器的例子。

在这个片段中，我们可以看到 http4k 应用程序可能带来的一些令人兴奋的事情。前两个表达式是对`HttpHandler`的定义，其中第一个表达式接受任何响应，并将其映射到一个主体中包含“pong”的`OK`响应。第二个处理程序接受一个请求，提取一个名字，并问候调用者。在下一步中，我们通过为每个处理程序分配一个特定的路由来将路由应用到处理程序。正如我们所见，`pingPongHandler`用于服务调用`/ping`的客户端，而`greetHandler`被映射到`/greet`。让我们来看看这个微型服务器的不同方面:

# 1.按指定路线发送

[http4k 中的路由](https://www.http4k.org/blog/meet_http4k/#routing)可以使用任意级别的嵌套，这种方式可以完美地工作，因为`routing`本身会产生一个新的`HttpHandler`(严格来说，是一种特殊的类型`RoutingHttpHandler`)，就像原来的类型一样。

# 2.过滤

如前所述，我们想看的另一个重要概念是`Filter`。首先，我们创建一个`requestTimeLogger`,它通过测量处理时间和记录运行时间来拦截每个传入的请求。可以使用`then`方法组合过滤器，这允许我们定义过滤器链。相应的 API 如下所示:

```
fun Filter.then(next: Filter): Filter
```

在上面的示例应用程序中，我们将自定义过滤器添加到一个名为`GZip`的默认过滤器中。一旦我们组合了所有的过滤器，我们想要添加一个`HttpHandler`到我们的过滤器链中。同样，我们可以使用一个`then`函数来实现:

```
fun Filter.then(next: HttpHandler): HttpHandler
```

正如我们所见，这又导致了一个`HttpHandler`。你现在可能已经明白了；它只需要两个简单的类型来表达 HTTP 服务器应该如何操作。

演示的`GZip`滤镜只是我们可以选择的众多默认滤镜之一。其他包括缓存、CORS、基本认证或 cookie 处理，可以在`org.http4k.filter`包中找到。

# 3.调用 HttpHandlers

该示例的最后一条语句显示了如何创建`app` o。它本身只是一个我们可以从服务器开始的`HttpHandler`。但是我们不需要。`app`处理程序描述了如何处理请求。我们可以使用这个对象，以及其他单独的`HttpHandler`和`Filter`并直接调用它(例如，在我们的测试中)。这不需要 HTTP。让我们来看看实际情况:

Http4k 自带了一个`Request`和一个`Response`的实现，其中第一个可以用来调用一个`HttpHandler`。调用不吸引人的`pingPongHandler`会产生类似于`HTTP/1.1 200 OK pong!`的结果，而调用最终的`app`处理程序会由于应用了 GZip 过滤器而给出一个 gzipped 响应。这个调用还包含一个服务器日志，通知请求的持续时间:`2019-09-20T21:22:55.300768Z LOG - Request to /ping took 3ms`。

请注意，虽然用随机 URI ( `/any`)调用`pingPongHandler`是没问题的，但是在调用路由支持的`app`时，我们必须使用指定的`/ping` URI。

最后，但同样重要的是，我们在端口`9000`上的`Jetty`上启动我们自己的 http4k `HttpHandler`作为服务器。在这里找到可用的服务器实现列表[。](https://www.http4k.org/guide/modules/servers/)

# 镜头

一个复杂的 HTTP 应用程序必须处理的事情之一是取出内容，以及将内容放入 HTTP 消息。当我们从请求中提取参数时，我们也关心相应值的验证。Http4k 有一个迷人的概念，可以帮助我们处理所陈述的问题:镜头。

## 基本定义

根据多种[资源](https://medium.com/javascript-scene/lenses-b85976cb0534)的说法，镜头最初是在[哈斯克尔世界](https://www.schoolofhaskell.com/school/to-infinity-and-beyond/pick-of-the-week/basic-lensing)中使用的，是一个可能看起来有点难以理解的功能概念。让我试着用一种可以理解的方式来描述它。假设我们有一个类`Whole`，它有不同的字段`part1`、`part2`，等等。一个镜头基本上由一个 getter 和一个 setter 组成，精确聚焦于`Whole`的一个部分。一个`Part1Lens`镜头获取器会获取一个`Whole`的实例来返回它所关注的部分，例如`part1`。

另一方面，镜头设置器接受一个`Whole`和一个设置聚焦部分的值，然后返回一个新的`Whole`和更新后的部分。记住，镜头既可以用来拍摄也可以用来拍摄整个物体的一部分。这在实践中是什么样子的？

## http4k 中的镜头

按照透镜的基本思想，http4k 透镜是双向实体，可以用来从 http 消息中获取或设置特定的值。描述镜头的相应 API 以 DSL 的形式出现，它也允许我们定义 HTTP 部分的可选性，我们将镜头安装到这个 HTTP 部分。由于 HTTP 消息是一个相当复杂的容器，我们可以将镜头聚焦于消息的不同区域:查询、头部、路径、表单字段或主体。

让我们来看一些如何制作镜头的例子:

到目前为止，创建镜头的 API 看起来或多或少很简单，但是如何在目标上使用它们呢？下面是
的伪代码语法 a)检索一个值:`<lens>.extract(<target>)`或`<lens>(<target>)`
b)设置一个值:`<lens>.inject(<value>, <target>)`或`<lens>(<value>, <target>)`

## 使用 Lens 从 HTTP 请求中检索值

重用之前的`greet`示例，让我们修改代码，使用镜头来检索名称:

我们创建一个双向镜头，聚焦于消息的查询部分，从中提取一个必需的非空名称。现在，如果客户机碰巧调用端点而没有提供`name`查询参数，镜头会自动返回一个错误，因为它被定义为“必需的”和“非空的”

请注意，默认情况下，应用程序向客户端公开了大量细节，将错误声明为`org.http4k.lens.LensFailure: query 'name' must be string`，包括详细的堆栈跟踪。相反，我们希望将所有镜头错误映射到 HTTP 400 响应，以表示客户端提供了无效数据。因此，http4k 提供了一个`ServerFilters.CatchLensFailure`过滤器，我们可以很容易地在我们的过滤器链中激活它:

## 使用 Lens 在 HTTP 请求中设置值

在研究了从 HTTP 消息中提取值之后，我们如何使用`nameLens`在 HTTP 请求中设置值呢？

这个例子展示了我们如何创建一个`Request`的实例，并通过一个或多个透镜注入一个值。我们可以使用`Lens::inject`函数来指定我们想要设置到任意`Request`实例中的值。既然我们已经看到了一个字符串镜头的基本示例，我们想深入研究如何处理一些更高级的 JSON 内容。

# JSON 处理

我们可以从几个 JSON 实现中进行选择，包括常见的 Gson 和 Jackson 库。我个人更喜欢杰克逊，因为它有一个伟大的科特林模块。在我们的应用程序中添加了 JSON 格式模块之后，我们可以开始使用 lenses 在 HTTP 消息之间封送对象。

让我们考虑一个管理人员的部分完整的 REST API:

在这个例子中，我们看到了一个类，它提供了两个处理程序，表示您期望从 REST API 中得到的常见操作。`getAllHandler`获取所有存储的实体并将它们返回给客户端。我们使用了通过 Jackson 的`org.http4k.format.Jackson.auto`扩展创建的`BiDiBodyLens<List<Person>>`(双向的)。

正如 http4k 文档中所提到的，`auto()`方法需要手动导入，因为 IntelliJ 不会自动选择它。我们可以通过提供一个类型为`List<Person>`的值并将其注入到一个 HTTP `Response`中，如`getAllHandler`实现所示，来使用前面已经展示过的结果透镜。

另一方面，`postHandler`提供了一个`HttpHandler`的实现，它从请求中提取一个`Person`实体并将其添加到存储中。同样，我们使用镜头轻松地从请求中提取 JSON 实体。

这已经结束了我们对镜头的先睹为快。正如我们所看到的，lets 是一个奇妙的工具，它让我们提取和注入 HTTP 消息的一部分，并提供了验证这些部分的简单方法。现在，我们已经看到了 http4k 工具集的最基本的概念，让我们考虑如何测试这样的应用程序。

# 测试

大多数时候，当我们考虑测试位于 web 框架之上的应用程序时，我们不得不担心框架的细节，这会使测试变得更加困难。

剧透一下:http4k 的情况并不完全如此🎉。

我们已经知道，http4k 工具集中的两个核心概念之一的`HttpHandlers`，只是将请求映射到响应的普通 Kotlin 函数，甚至一个完整的 http4k 应用程序也只是一个`HttpHandler`，因此是一个可调用的函数。因此，整个和部分 http4k 应用程序可以很容易地进行测试，而不需要额外的工作。

尽管如此，http4k 的开发者认为提供一些额外的模块来支持我们测试我们的应用程序仍然是有帮助的。其中一个模块是`http4k-testing-hamkrest`，它增加了一组 [Hamkrest](https://github.com/npryce/hamkrest) matchers，我们可以用它来更容易地验证消息对象的细节。

# Http4k 处理程序测试示例

这个片段演示了我们之前已经使用过的`PersonHandlerProvider`的测试。如图所示，用一个`Request`对象调用一个`HttpHandler`，然后使用 Hamkrest 或任何您喜欢的断言库来检查结果`Response`，这非常简单。另一方面，测试`Filter` s 稍微困难一些。

老实说，这只是我们在处理程序上需要做的一件小事。过滤器通过应用一些中间预处理或后处理将一个`HttpHandler`映射到另一个。与其研究处理程序本身之间的映射，不如再次通过那个过滤器发送一个`Request`并查看结果`Response`会更方便。好消息是这样做非常容易。

# Http4k 过滤器测试示例

我们有一个名为`addExtraHeaderFilter`的`Filter`,它向一个已处理的请求添加一个自定义头，然后将其转发给下一个过滤器。我们的目标是通过测试中的过滤器发送一个简单的请求。我们能做的是通过`then`给过滤器添加一个哑的`{ Response(OK) }`处理程序，使其成为一个简单的`HttpHandler`。因此，我们可以调用新创建的处理程序，现在它包含了我们自己的过滤器，并调查产生的`Response`对象是否包含新的预期头。我们开始吧——处理程序和过滤器都得到了测试。

最后，我想说的是，这只是用最熟悉的工具测试 http4k 应用程序的愉快经历的一瞥。可能有必要对实际运行的服务器进行测试，并在较低的级别上验证响应，即比较生成的 JSON。通过[认证测试](https://www.http4k.org/guide/modules/approvaltests/)模块也可以做到这一点。在本文的后面，我们想看看 http4k 的客户机模块，这又打开了一些新的可能性。

# 无服务器

我们这个时代最热门的话题之一是无服务器计算。我们可以在别人的服务器上运行我们的代码。其中一部分被称为功能即服务(FaaS)。最常见的 FaaS 平台包括 AWS Lambda、谷歌云函数和微软 Azure 函数。总的想法是，这些供应商提供了一个平台，我们可以在那里部署我们的代码，他们负责管理资源和按需扩展我们的应用程序。

无服务器的一个棘手之处是，如果我们的功能暂时不用，它可能会被平台关闭。这需要在有人再次需要他们的时候重新创业。这对我们意味着什么？我们需要选择允许应用程序快速启动的目标平台和工具。例如，传统形式的 JVM 上的 Spring 可能不是该用例的最佳工具。然而，正如您所想象的，http4k 占用空间小，启动时间超级快，是一个很好的选择。它甚至自带对 AWS Lambda 的原生支持。

# 客户作为一个功能

到目前为止，我们已经了解了 http4k 有多酷，以及为什么它是开发服务器应用程序的好工具。如果客户机不使用 HTTP 服务器，那么 HTTP 服务器就没有多大意义，所以我们想从另一个角度——客户机作为一种功能——来结束本文。

http4k `core`库提供了我们开始使用客户端所需的一切。http4k 中的客户端也是一种特殊形式的`HttpHandler`，正如我们在这个小片段中看到的:

这里使用的`JavaHttpClient`是`core`库附带的默认实现。如果我们更喜欢 OkHttp、Apache 或 Jetty，我们将使用相关的[模块](https://www.http4k.org/guide/modules/clients/)来替换默认模块。因为我们是针对接口编程的(客户是 T4)，所以在任何时候交换实现都不是什么大事。显然,`core`库附带了几个默认的`Filter`,我们可以应用到我们的客户端。这些都可以在`ClientFilters.kt`文件中找到，该文件包含像`BasicAuth`、`Gzip`这样的内容，以及更多您所期望的内容。

事实上，http4k 服务器的所有概念——包括处理程序、过滤器和镜头——都可以在 http4k 客户机中重用，这带来了很多可能性。例如，使用客户机模块来测试您的服务器，反之亦然，这是很有意义的。我很快就爱上了整个堆栈，并在生产中使用了几年。

# 总结与展望

在过去的几年里，我个人学会了欣赏 http4k。一旦熟悉了基本概念，快速开发服务器应用程序就变得简单了。Http4k 附带了一系列令人难以置信的受支持的概念和技术，包括 OAuth、Swagger、Websockets、XML 等等。

它的模块化本质允许我们根据需要通过应用依赖关系来添加功能，并且由于它简单的基本类型，它是高度可扩展的。Http4k 是一个工具集，它允许我们编写具有快速启动时间的应用程序，因此当涉及到 FaaS 和无服务器计算时，它也是一个有效的选择。

似乎这还不够，这个工具集还包括了编写 HTTP 客户端的复杂方法，这是我们在上一节中了解到的。总的来说，http4k 是一项很有前途的技术，在选择下一个 http 工具集时，您绝对应该考虑它。