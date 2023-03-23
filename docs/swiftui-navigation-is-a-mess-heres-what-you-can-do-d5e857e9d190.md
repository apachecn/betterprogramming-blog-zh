# SwiftUI 导航一塌糊涂。你可以这样做

> 原文：<https://betterprogramming.pub/swiftui-navigation-is-a-mess-heres-what-you-can-do-d5e857e9d190>

## 更轻松地管理您的视图

![](img/17cbd7a1000997022fe364b216be1e70.png)

Mick Haupt / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) 摄影

SwiftUI 好看又强大。然而，在 pure SwiftUI 中管理导航是困难的，并且会导致混乱的解决方案。在这篇文章中，我将向您展示如何有效地管理视图。

# 为什么乱？

这是因为 SwiftUI 的核心思想——视图是状态的函数，或者说视图是状态驱动的。不要误会，这个概念很棒，但是 SwiftUI 的导航还没有这么先进。

> 视图是状态的函数，导航也不例外。

然而，SwiftUI 无法在您的应用程序中构建健壮的导航。

# 混乱的例子

当您需要呈现一些带有漂亮过渡的视图序列时，请考虑 onboarding 屏幕的常见情况。你能用 SwiftUI 做什么？或许，创建一个`enum`来告诉用户哪个屏幕是活动的，然后使用`switch`来呈现视图序列。

如果需要修改顺序或者改变视图数量怎么办？你需要修改相应的`enum`，修改视图内部的切换逻辑和其他东西。

没那么灵活吧？

哦，然后你决定通过`.sheet`在中间展示一个视图。这就是混乱开始出现的时候。您创建一个额外的`@State`来检查工作表是否打开，确保它被正确更新，并重构您之前使用的`switch`块。

现在，这是一个混乱的视图，容易出现意想不到的错误。

# 现有导航视图

最明显的就是 [NavigationView](https://developer.apple.com/documentation/swiftui/navigationview) ，在新的 iOS 16 中已经弃用。

![](img/8dd627ab357f03536bcaf50278c84901.png)

图片由[https://developer . apple . com/documentation/swift ui/navigation view](https://developer.apple.com/documentation/swiftui/navigationview)

使用`NavigationLink`，它可以呈现新的视图，并添加一个“back”按钮来返回到上一个视图。

并且它不支持编程导航。

苹果推出了新的[导航堆栈](https://developer.apple.com/documentation/swiftui/navigationstack)来解决这个问题，但它仍然不够灵活。例如，我希望能够随心所欲地修改视图，但是 NavugationStack 插入了后退按钮。此外，它不支持不同的过渡。虽然很高兴看到 SwiftUI 朝着这个方向发展，但我们还没有到那一步。

所以，即使在 iOS 16 中，SwiftUI 也没有强大到可以管理你能想到的任何导航。

还有`.sheet()`。`NavigationStack`也不会让`.sheet()`变得更容易处理。

# 设计灵活的导航库

我决定创建一个有几个要求的库:

*   编程视图导航
*   呈现一系列视图的能力
*   支持任何 SwiftUI 过渡和动画
*   完全由状态驱动:没有单件或环境对象
*   手柄`.sheet()`

听起来很酷，对吧？

直截了当地说，我能够创建这样一个库。

[](https://github.com/AlexRoar/PathPresenter/blob/main/README.md) [## PathPresenter/README.md 位于主 AlexRoar/PathPresenter

### swiftUIOnboarding.mp4 纯 SwiftUI 路由与过渡，动画，和。工作表()支持。在 SwiftUI 中，视图是一个…

github.com](https://github.com/AlexRoar/PathPresenter/blob/main/README.md) 

> 我总是乐于接受客观的批评和对新功能的要求。不要犹豫，在 GitHub 上打开一个问题！

所以，如果你只是想要一个好的工具来做我上面列出的事情，你可以就此打住。现在，让我们看看我是怎么做的。

# 呈现方式

库的核心是一个存储视图和关于如何展示它们的信息的结构。演示的可能选项包括:

> 请注意，通过`.sheet()`展示就像展示任何其他视图一样简单。

因此，您可以在没有任何动画的情况下呈现视图，用所需的过渡来呈现视图，并在表单中呈现视图。

# 小路

这个结构存储关于视图的信息。它只是存储一个带有表示类型信息的类型删除视图数组。您可以在顶部添加视图，也可以从顶部移除视图。

老实说，我从`NavigationStack`得到了这个想法，就像以前一样，我尝试做一个类似的库，能够在中间插入。然而，我在中间插入动画时遇到了几个问题。大概，有可能做到。

# 视图本身

关键的想法是如何呈现这些视图。PathPresenter 使用 ZStack 来完成这项工作。它仅显示未标记为`.sheet`类型的视图。

视图在自身内部管理`.sheet`，并决定何时需要显示它。如果最后一个元素必须以工作表的形式显示，那么工作表将被激活。

就是这样。

我介绍了实现的核心概念。您可以查看 GitHub，了解完整的实现。代码有完整的文档，你可以在评论里问我任何问题。

# 使用 PathPresenter 的入职示例

我们简单地使用图书馆；).

为了构造`Path`，我们向它添加了所需的视图，如下所示:

然后，你用`RoutingView(path: $path)`来呈现这个路径。您可以查看这个项目中的完整示例:

[](https://github.com/AlexRoar/PathPresenterExample/tree/main) [## GitHub-Alex roar/pathfrenderexample

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/AlexRoar/PathPresenterExample/tree/main) 

# 最终注释

我喜欢这个图书馆的样子。您可以自由地构建任何视图序列，并构建自己的导航。

如果你注意到任何错误，不要犹豫与我联系。

> 这篇文章最初发表在 [alexdremov.me](https://alexdremov.me/swiftui-navigation-is-a-mess-heres-what-you-can-do/) 上。