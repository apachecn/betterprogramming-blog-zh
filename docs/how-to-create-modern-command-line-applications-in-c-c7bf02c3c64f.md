# 如何用 C#创建现代命令行应用程序

> 原文：<https://betterprogramming.pub/how-to-create-modern-command-line-applications-in-c-c7bf02c3c64f>

## 命令行应用不一定很无聊！

![](img/8fb9a4383bc45203c7a32e587ad721ba.png)

照片由[丹·列斐伏尔](https://unsplash.com/@danlefeb?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

命令行应用程序是有史以来最原始的应用程序。在一个时髦的、高度优化的 web 应用程序提供了出色的用户体验的世界里，CLI 应用程序是初级的，容易出错，有时使用起来简直令人恼火。

你要么爱他们，要么恨他们。实际上没有任何中间立场。不管您的个人倾向如何，命令行应用程序在当今世界仍然非常普遍。无论您是使用`kubectl`管理 Kubernetes 集群还是构建. NET 应用程序，底层基础设施都是由命令行应用程序提供的。

它们填补了一个重要的空白，因为它们不需要图形界面，因此非常适合在 CI/CD 管道中运行，甚至可以跨多个操作系统运行。尤其是在编写与交付管道紧密集成的代码时，编写自己的控制台应用程序可能非常有效。

当然，你可以通过`Console.Read`自己的方式获得胜利，应用一些基本的字符串拆分，然后*瞧*你就成功了！这是做这件事的一种方法。另一个是微软即将发布的`System.CommandLine`库，它是驱动`dotnet` CLI 的库。

在本文中，我们将使用这个全新的库来创建可维护和可扩展的命令行应用程序。

# 入门指南

让我们编写一个简单的 CLI 应用程序，在构建管道的末尾，向跟踪我们的构建和发布的中央 API 发送一个 web 请求。这不是跟踪发行版的理想方式，但目前来说还可以。

从 Visual Studio 或命令行创建新的控制台应用程序:

```
> mkdir BuildTracker && cd BuildTracker
> dotnet new console
```

接下来，我们将安装所需的软件包:

```
> dotnet add package System.CommandLine --prerelease
> dotnet add package System.CommandLine.DragonFruit --prerelease
```

就这些了。让我们开始编写我们的第一个命令。

# 传递选项

我们的应用程序只是将一个版本发布到一个通过命令行参数传递的 HTTP 端点。在这个用例中没有太多的变化，也就是说，我们将向其发送消息的端点以及我们正在构建的应用程序的版本都可以作为一个选项发送到一个命令。

这允许我们保持我们的程序相对简单。您可能已经注意到，我们已经在前面的部分安装了一个名为`System.CommandLine.DragonFruit`的包。诚然，这是一个有点特殊和难以描述的名字，它允许我们极大地简化我们的控制台应用程序。

DragonFruit 使我们能够直接从我们的`Main`方法的参数中解释参数。它还可以将命令行参数直接映射到参数名，同时遵守命名约定。例如

```
> foo.exe --do-something
```

在`Main`的参数列表中被翻译成`doSomething`——这允许我们定义最基本的控制台应用程序，接受如下两个命令行参数:

DragonFruit 为我们处理命令行参数到我们的`Main`方法参数的映射。

为了进行试验，在 Visual Studio 中进入项目的属性，并选择 *Debug* 选项卡。在*应用程序参数*部分，粘贴以下内容:

```
--endpoint "[https://somewhere.com](https://somewhere.com)" --build-version "1.0.0"
```

如果您没有使用 Visual Studio，请在项目的`Properties`文件夹中创建一个包含以下内容的`launchSettings.json`文件:

请确保将占位符替换为项目的实际名称。之后，简单地调试程序就会将这些命令行参数传递给应用程序。如果一切顺利，您应该在调试应用程序时观察到以下输出:

```
Endpoint: [https://somewhere.com](https://somewhere.com)
Build Version: 1.0.0
```

![](img/c2c20c237ee57883e7d4d5c7ada7ba76.png)

克拉克·范·德·贝肯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 添加命令

虽然 DragonFruit 应用程序模型提供了一种非常有效的入门方式，但是您可能会遇到这样的情况:您希望对传递给命令的选项进行更多的控制，或者甚至希望嵌套命令。

幸运的是，`System.CommandLine`附带了一个易于使用的、非常广泛的命令解析器。假设我们想要使用`Command`语法来表达上一节中的简单控制台应用程序。

下面是我们如何使用命令语法定义上一节中的应用程序:

与之前的 DragonFruit 应用程序相比，这里发生了更多的事情。让我们仔细看看。

我们从定义一个`RootCommand`开始，它构成了命令树的根节点。我们现在不接受命令行参数作为参数，而是通过与命令相关联的`Option<T>`实例显式定义它们。

因为仅仅声明根命令对我们没有任何好处，所以我们附加了一个处理程序，它知道如何在命令被调用时处理它。请注意，委托接受两个命名参数。按照命令中声明的那样命名选项名称非常重要。如果这些名称不匹配，处理程序将无法正确绑定。

最后，我们在 root 命令上调用`InvokeAsync`,并从命令行传递参数给它。然后，类似于我们之前的应用程序，输出我们调用应用程序时使用的参数。

请注意，与 DragonFruit 应用程序模型相比，这要多得多的代码，也复杂得多。如果你正在编写一个只有一个命令的应用程序，我强烈建议你使用 DragonFruit 应用程序模型。如果您的应用程序支持多个命令，那么使用命令语法会更加有效。

# 子命令

既然我们已经简要介绍了根命令，我们应该看看子命令(通常称为*动词*)。从技术上讲，一个命令可以有任意数量的“子”命令。这允许您编写将命令“链接”在一起的命令行，就像`dotnet sdk check`将`sdk`和`check`命令链接在一起。

这与上一节中概述的命令结构非常相似。然而，我们没有将选项传递给根命令，而是为各个命令定义了选项。我们还在命令级别声明处理程序:

根据应用程序是通过`build --build-version <v> --endpoint <e>`还是`release [...]`调用，输出会有所不同。当然，在现实世界中，您可能不会有两个如此相似的独立命令，但是您应该明白这一点。

有了这个 API，可能性几乎是无限的。您可以嵌套命令，为每一个命令设置单独的处理程序，您甚至可以实现[路由和命令管道](https://github.com/dotnet/command-line-api/blob/main/docs/How-To.md#Middleware-Pipeline)来精细地控制应用程序的输入和输出流。

# 结论

微软在`System.CommandLine`发布了一个非常强大的命令行界面库。正是这个库为`dotnet`命令行界面提供了动力，而[他们已经在它的基础上构建了相当广泛的 CLI](https://github.com/dotnet/sdk/tree/main/src/Cli/dotnet) 。

这篇文章仅仅是对`System.CommandLine`的介绍——看看[、官方知识库](https://github.com/dotnet/command-line-api)以及其中可用的[样本](https://github.com/dotnet/command-line-api/tree/main/samples)和[文档](https://github.com/dotnet/command-line-api/tree/main/docs)。

我希望这篇文章能让您开始使用 CLI 应用程序，或者至少激起您的兴趣。如果有，请告诉我！