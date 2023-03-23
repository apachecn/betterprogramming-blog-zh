# 再见，RecyclerView 适配器—你好，Jetpack 中的列表构成

> 原文：<https://betterprogramming.pub/goodbye-recyclerview-adapters-hello-lists-in-jetpack-compose-d0a389a1683e>

## 没有更多的适配器来显示大型 Android 列表。使用 LazyColumn 和 LazyRow

![](img/f09e091aa08487bfbabe7238b5b583ae.png)

照片由 [Cookie 在](https://unsplash.com/@cookiethepom?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 Pom 拍摄。

在本文中，我们将学习如何在 [Jetpack Compose](https://developer.android.com/jetpack/compose) 中显示大型数据集。我们将涉及水平和垂直列表。除此之外，您还将学习如何实现粘性标题和多种视图类型。

# 介绍

Jetpack Compose 是谷歌最近试图让 Android 开发者轻松构建 UI 的尝试之一。

> “Jetpack Compose 是一个用于构建原生 Android UI 的现代工具包。Jetpack Compose 以更少的代码、强大的工具和直观的 Kotlin APIs 简化并加速了 Android 上的 UI 开发。”— [安卓开发者](https://developer.android.com/jetpack/compose/tutorial)

Jetpack Compose 最近进行了测试。这意味着 API 结构将不再有任何剧烈的变化，因此这将是学习如何使用这个下一代 UI 套件进行 Android 开发的大好时机。

您需要安装 Android Studio Canary，北极狐(2020.3.1) 版本才能使用 Jetpack Compose beta。

# 先决条件

在进一步学习之前，您必须具备如何使用 Jetpack Compose 的基本知识。如果你是作曲新手，我强烈推荐阅读以下文章:

*   [“Jetpack 合成组件(第 1 部分)”](/jetpack-compose-components-part-1-a80717dbfb59)
*   [“在 JetPack Compose 中构建 Android 布局”](/build-android-layouts-in-jetpack-compose-bda2e0837f5e)
*   [“面向 Android 的 Jetpack 合成中的主题化”](/theming-in-jetpack-compose-for-android-667d42b8279c)
*   [“在 Android 中使用 Jetpack 组合导航”](/navigation-with-jetpack-compose-in-android-ab84d0984ade)

# 惰性柱

要以传统方式显示垂直滚动列表，我们必须编写大量代码:

*   显示`RecyclerView`的布局文件
*   适配器类
*   显示适配器项目的布局文件
*   一个`Activity`或`Fragment`级

使用 Compose，只需一个函数就可以显示一个列表。当涉及到编写时，主要的优点是代码简洁，手动工作少。一种常见用例的简单方法(显示项目列表)。不再拖延，让我们从`LazyColumn`可组合函数开始:

这是一个有很多事情要做的功能，对吗？相信我，我第一次用的时候也有这种感觉，但是一旦你熟悉了，就小菜一碟了。

这些是我们经常使用的参数:

*   `Modifier`:`Modifier`是一种修改组件某些方面的方式，这将导致它在屏幕上的显示方式。不仅仅和`lazyColumn`有关。我们几乎可以在任何组合组件上使用修饰符。你可以在[这篇文章](/jetpack-compose-components-part-3-ce5ae661f3d6)中了解更多。
*   `LazyListState`:用于控制和监控列表的状态。
*   `PaddingValues`:用于给列表添加填充。一个常见的用例是向列表添加内容填充。
*   `reverseLayout`:在`true`时反转滚动方向。
*   `content`:描述内容的块。在这个块中，您可以使用像`LazyListScope.item`这样的方法来添加单个项目，或者使用`LazyListScope.items`来添加项目列表。

## 使用

理论说够了。让我们获得一些实际经验。我们的目标是使用`LazyColumn`显示订阅列表。这里，我们必须显示一个可以滚动订阅列表的`topbar`。看一下实现情况:

我们使用`lazyColumn`和`modifier`参数来填充最大宽度和高度。然后我们使用`item` Compose 函数将`topbar`布局放大为列表中的第一项。我们使用`items` Compose 函数通过传递列表来显示一个`SubscriptionsItems` 的列表。

`item`和`items`是`LazyListScope` 界面中的两个函数，分别用于展开单个作曲函数和显示列表。

# 拉兹罗

是另一个用于显示水平列表的可组合函数。在语法和实现上都是`LazyColumn` 的翻版。唯一的区别是列表只能水平滚动。让我们从`LazyRow` 可组合函数开始:

我认为没有必要做任何解释，因为它是`lazyColumn`的复制品。我们来看看用法。

## 使用

我们的目标是使用`LazyRow`显示订阅列表。这里，我们必须显示订阅列表。看一下实现情况:

# 多视图类型

多视图类型就是在列表中显示不同布局的能力。为了在传统方法中实现这一点，我们在`RecyclerView`适配器中使用了一个叫做`getViewType`的东西。基于列表项，我们传递一个唯一的视图类型常量。基于这个常量，我们在适配器中创建和膨胀各自的布局和`viewholder` 。

但是在 Compose 中，我们不需要使用任何常量或额外的代码。我们只是触发适当的可组合函数，就像我们在`lazyColumn`示例中对`topbar`和列表项所做的那样。

为了更清楚地理解多视图类型，让我们看一个常见的用例，其中我们根据显示名称的第一个字符显示列表。这里我们的目标是显示列表，列表由订阅名称的首字符分隔。看一看:

在收到的数据集上，我们已经基于订阅名称的第一个字符应用了`groupBy` 函数。这将返回一个以初始字符为键的映射，以及以该字符为值的订阅列表。然后，我们使用这个映射来扩展标题和组合函数列表。

# 粘性标题

在 Jetpack Compose 中实现 sticky headers 是一个简单的函数调用，但是对于传统的方法，我们必须做大量的工作。还记得我说过`item`和`items`是`LazyListScope`界面中的两个函数吗？原来它还有一个功能叫`stickyHeader` *。*

`stickyHeader` 仍然是一个实验性的特性。要使用它，我们需要用`@ExperimentalFoundationApi` *注释我们的 Compose 函数。*我们只需要用`stickyHeader` 替换上面示例中的`item`函数，就可以使标题项成为粘性标题。看一看:

# 参考

*   [https://www.youtube.com/watch?v=BhqPpUYJYeQ&t = 1s](https://www.youtube.com/watch?v=BhqPpUYJYeQ&t=62s)

# 奖金

Jetpack 中的 Android 素材主题由 Satya Pavan Kantamani 创作

[](/material-theming-in-jetpack-compose-776af9b4498c) [## Jetpack Compose 中的 Android 材质主题

### 使用 Compose 轻松定制应用程序范围的主题

better 编程. pub](/material-theming-in-jetpack-compose-776af9b4498c) 

目前就这些。希望你学到了有用的东西。感谢阅读。