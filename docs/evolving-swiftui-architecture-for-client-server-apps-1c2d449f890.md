# 为客户端-服务器应用开发 SwiftUI 架构

> 原文：<https://betterprogramming.pub/evolving-swiftui-architecture-for-client-server-apps-1c2d449f890>

## 将 React.js 学习应用于 SwiftUI

![](img/8b7fe33eb079d7441984dca4b943c711.png)

兰斯·安德森在 Unsplash 上拍摄的照片

在上一个架构中，我们详细讨论了使用 MV 模式的 [SwiftUI 架构。强烈建议你阅读那篇文章。在本帖中，我们将介绍如何使用从 React.js 框架中学到的模式和实践来创建 SwiftUI 客户端/服务器应用程序。](https://azamsharp.com/2022/10/06/practical-mv-pattern-crud.html)

# 在 React 中使用 JSON

React 于 2013 年推出，这意味着 React 比 SwiftUI 领先约 6 年。React、SwiftUI 和 Flutter 都是声明式框架，它们在本质上极其相似。我们可能不会使用 React 或 Flutter 来构建应用程序，但我们肯定可以从它们那里学习架构模式，并将其整合到我们的 SwiftUI 应用程序中。

> 鼓励查看 React 项目的大型代码库，并了解他们如何实现自己的应用程序。由于两个框架的相似性，相同的技术可以应用于 SwiftUI 项目。

在下面的代码中，您可以看到 App 组件(类似于 SwiftUI 视图)，它从一个 API 获取所有产品，然后将其显示在屏幕上。

App 组件通过 React 挂钩使用本地/私有状态，如下面的实现所示:

```
**const** [products, setProducts] **=** useState([])
```

fetch API 用于消费 JSON 响应，然后最终填充组件的本地状态。

这相当于 SwiftUI 中的`@State`。下面你可以找到 SwiftUI 中内容视图的完整实现。

我们用产品填充了 private/local`@State`, API 调用是从我们的视图(视图模型)中进行的。

> *SwiftUI 视图基本等同于 React 中的组件或者 Flutter 中的 widgets。SwiftUI views 充当视图模型。说到这里，你不应该把业务逻辑放在你的视图中(视图模型)。业务逻辑属于模型、域服务或服务器(客户机/服务器)应用程序。*

上述方法的主要问题是，在任何其他视图中很难重用`fetchProducts`，因为它是特定视图(视图模型)的一部分。

但是，如果您不打算在任何其他视图中展示产品，该怎么办呢？在视图(视图模型)内部实现`fetchProducts`可以吗？

即使在其他视图中您可能不调用`fetchProducts`,仍然建议将其移到指定的类中。通过这种方式，您可以随时随地灵活地调用它，还可以添加附加请求头、授权、缓存甚至测试等功能。

# 实施网络模型

如前一节所述，将网络调用转移到一个单独的专用类中是一个好主意。这将允许您在单独的视图中轻松地重用网络呼叫。

`NetworkModel`的实现如下所示:

可以从`ContentView`中直接调用 NetworkModel 类，如下所示:

> *你可能会问，如果网络模型只是一个有着花哨名字的网络服务。答案是肯定的……有点。网络模型和典型网络服务的主要区别之一是网络模型也可以存储状态。这由网络模型中的* `*products*` *属性表示。如果你有兴趣创建一个典型的网络层，那么你可以查看我以前的文章* [*这里*](https://azamsharp.com/2022/10/06/practical-mv-pattern-crud.html) *。*

我们甚至可以重构上面的代码来创建`ProductListView`和`ProduceCellView`。如下所示:

> 在上面的代码中，ContentView 用作容器视图，ProductListView 和 ProductCellView 是表示视图。容器视图用于包含其他视图(可重用视图)。容器视图的主要工作是向子视图提供数据。

# 搜索

大多数情况下，当您显示数据列表时，您还希望对数据执行不同的操作，如搜索和排序。让我们看看如何完成搜索的任务。

我们将使用`searchable`修饰符，这将允许我们创建一个搜索栏。我们还将使用`onChange`修饰符，它将在每次用户在文本字段中输入内容时触发。`onChange`修饰符调用 performSearch 函数，该函数搜索从 networkModel 返回的产品，并将其分配给 filteredProducts 数组。

新的私有属性`products`如果不为空，则返回 filteredProduct，否则返回所有产品的列表。

*swift ui 中的 ContentView 也是一个视图模型，不执行任何业务逻辑操作。所有的业务规则都在服务器端运行，只有结果被发送回客户端(iOS App)进行显示。*

现在，让我们看看排序，以及如何在我们的应用程序中实现排序。

# 整理

我们将允许用户根据产品的标题对产品进行升序或降序排序。`SortDirection`实现如下:

```
**enum** **SortDirection** {
    **case** asc
    **case** desc
}
```

分类将通过按下按钮来执行。我们还需要切换按钮的文本。这是由本地@State 属性执行的，如下所示:

每当 sortDirection 发生变化时，就会触发 performSort 函数。performSort 负责按升序或降序对列表进行排序。

*排序功能的核心是* `[*sorted extension*](https://www.swiftbysundell.com/articles/the-power-of-key-paths-in-swift/)` *，它允许基于关键路径进行排序。这意味着，它是一个可重用的函数。*

*您不必满足于整个应用程序只有一个 NetworkModel 类。单个网络模型可能适用于小型应用程序，但对于大型应用程序，您可以根据请求类型创建多个网络模型。这可以包括用户网络模型、帐户网络模型、产品网络模型、目录网络模型等。*

# 贮藏

缓存允许您的客户端(iOS 应用程序)从内存或文件系统获得响应，而不是一直到服务器。这可以极大地提高应用程序的性能。即使只有 5-6 秒的缓存(微缓存)也可以帮助您节省大量请求，并提高应用程序的性能。

缓存层可以在网络模型中实现。缓存将在以后的文章中讨论，但是你可以看看下面的评论来了解一下。

有时开发人员会在视图和网络之间增加一个中间层。我们喜欢将这一层称为聚合根模型，然后您的网络模型就变成了 Webservice。那种方法在上一篇文章 [*中讨论过，这里*](https://azamsharp.com/2022/10/06/practical-mv-pattern-crud.html) *。根据您的应用，您可以评估哪种架构更适合您的应用。*

SwiftUI 团队在使框架易于使用方面做了大量工作。有时，感觉如此简单，以至于我们开始质疑自己解决方案的有效性。使用 React 和 Flutter 时会有相同的体验。声明式用户界面给编程带来了新的概念和模式，这可能不太符合旧的原则。人们必须尝试不同的模式，并选择满足他们需求和标准的模式。

# 结论

在本帖中，您了解了用于客户端/服务器应用程序的 SwiftUI 架构。该架构的灵感来自 React 应用程序。正如 React 之前提到的，Flutter 和 SwiftUI 有很多相似之处，作为开发人员，我们应该总是尝试从更成熟的框架中学习。

# 资源

*   [iOS 中的 MV 设计模式:以苹果的方式构建 SwiftUI 应用](https://www.udemy.com/course/mv-design-pattern-in-ios-for-swiftui/?referralCode=4627986F77F533DEF0C7)
*   [SwiftUI 架构——MV 模式方法完全指南](https://azamsharp.com/2022/10/06/practical-mv-pattern-crud.html)
*   [在 SwiftUI 中拥抱核心数据](https://azamsharp.com/2022/10/11/embracing-core-data-in-swiftui.html)
*   [SwiftUI 视图也是一个视图模型](https://azamsharp.com/2022/07/21/view-is-the-view-model.html)