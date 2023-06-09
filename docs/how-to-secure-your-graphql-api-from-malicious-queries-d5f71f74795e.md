# 如何保护您的 GraphQL API 免受恶意查询

> 原文：<https://betterprogramming.pub/how-to-secure-your-graphql-api-from-malicious-queries-d5f71f74795e>

## GraphQl API 的安全性和效率

![](img/f60b4d2e0672b102b237733e5e55ecce.png)

Clint Patterson 在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

如今有了这么多的图书馆和在线社区，构建 API 是很容易的。尽管如此，你心里可能还是有些疑问。比如，我们如何保护我们的服务器？我们如何限制或白名单某些查询运行在我们的服务器上？

如果你曾经使用过`graphql`，你可能知道`graphql`查询循环。让我们来看一个例子:

上面的查询类型有什么问题吗？我们在这里可以有一个无限循环，如果有人对我们的服务器运行这个查询，它肯定会使我们的服务器崩溃或产生类似 DOS 攻击的东西。如果恶意用户可以创建一个损害后端的嵌套查询，这就是一个问题。有许多方法可以解决这个问题——让我们来看看其中的几种。

# 尺寸限制

一种简单的方法是通过原始字节限制查询的大小，因为在`graphql`中，所有请求都被视为一个`post`请求，并且所有查询都是字符串化对象的一部分。然而，这可能并不是在所有情况下都有效，反而会伤害到您，因为您的一些具有长字段名的有效查询可能会失败。

您可以在中间件内部的每个请求之前运行上面的代码。它将为进入您的`graphql`服务器的每个请求运行，验证所有请求，并拒绝任何过长的查询。

# 深度限制

另一种方法是将嵌套限制在第`n`级。您可以定义允许执行查询的级别，并去掉第`n`级之后的其余字段。做这件事的一个很好的包是`[graphql-depth-limit](https://github.com/stems/graphql-depth-limit)`，它限制我们定义我们希望在我们的服务器上允许的查询深度。快速服务器和 Koa 都能很好地工作。即使你使用的是阿波罗服务器，它也能很好地工作。

# 查询成本分析

在深度限制中，我们将查询的执行限制在第`n`级，但这可能不适合所有情况。有时深度可能会小很多，但是计算该查询的成本可能会很高。当我们在单个查询中获取大量数据，并给我们的后端服务器或数据库服务器带来大量负载时，可能会发生这种情况。这些查询可能如下所示:

尽管这个查询只有两层深度，但您可以理解其复杂程度——它将从数据库服务器请求的数据量以及后端服务器上发生的计算。

这个问题不能通过深度限制或大小限制来解决，所以我们需要一些健壮的东西来处理这种查询。通常在这些情况下，我们需要执行查询成本分析，其中我们的服务器计算每个查询的成本，并决定是允许还是拒绝该查询。

为此，我们需要在服务器上运行每个查询之前对其进行分析。如果它们太复杂或太昂贵，我们需要阻止它们。有许多开源库是由一些真正聪明的人建立的——其中一个库是`[graphql-validation-complexity](https://github.com/4Catalyzer/graphql-validation-complexity)`。您可以单独定义每个字段的复杂性，就像标量类型和对象的不同复杂性一样。还有基于每个字段计算复杂度的`[graphql-query-complexity](https://github.com/slicknode/graphql-query-complexity)`，不像`[graphql-validation-complexity](https://github.com/4Catalyzer/graphql-validation-complexity)`基于类型计算复杂度。使用这些库添加查询成本分析非常简单:

在您开始在您的服务器上实现查询成本分析之前，只需确保您的服务器确实需要它。否则，这将成为服务器的另一项开销，最终只会浪费资源和时间。如果您的服务器没有任何复杂的关系获取，那么没有查询成本分析可能会更好——只需添加大小限制和深度限制。

# **查询白名单**

查询白名单有点复杂，有时可能是一把双刃剑。我来简单解释一下。

每家餐馆供应的每一道菜都有一个名称或编号，所以你不用说这道菜的全名，比如“奶酪比萨，双层奶酪汉堡，旁边放有橄榄和薯条”，而只需说“2 号”这样既省时又省力。在这种情况下，你只是节省了几个词，但你也节省了*一些东西*。但是当涉及到从客户端到服务器的请求时，如果不发送整个查询，而是只发送查询的散列，可以节省大量请求数据。

这些在`graphql`术语中被称为“持久查询”,它们根据请求保存您的数据，并保护您的`graphql`查询免受在您的服务器上执行的恶意查询。基本上，您需要提前编译一个包含所有允许的查询的列表，并根据这个列表检查任何查询。您甚至可以为每个查询生成一个散列，并在请求中发送散列值。

该请求将类似于以下内容:

实际上没有人知道模式服务器正在运行，或者正在运行哪个查询或变化——这只是一个散列。如果您的查询完全是静态的，并且您没有使用像`relay`这样的库来动态生成这些查询，这可能是您最可靠的方法。您甚至可以自动化散列查询并将其放入生产应用程序的整个过程。您不需要在服务器上进行查询验证，因为您已经知道服务器上正在运行的所有查询。

在您开始实施`Query`白名单之前，请了解一些限制:

*   你很难在服务器上添加、删除或修改任何查询，因为现在你必须与所有客户端通信，并给它们新的哈希值。如果有人运行稍加修改的查询，将导致查询失败。
*   如果您正在构建除您自己的团队之外的开发人员可以访问的公共 API，使用这种方法真的不是一个好主意。
*   如果团队之间沟通不畅，查询中意外的细微变化会导致应用程序崩溃。

# 结论

总而言之，我建议使用深度限制，这可能是每个 GraphQL 服务器都应该默认具备的。之后，您可以在它的基础上进行构建，添加更多的层来使您的服务器更加安全。

我感觉查询白名单是针对特定类型的应用的。在实施之前，您应该正确地分析您的需求。

其他一些不太为人所知的方法是实现查询超时，这样您的查询就不会无限运行并使服务器崩溃。查询成本分析有点复杂，但它可以最大程度地保护您的服务器免受恶意查询的攻击。