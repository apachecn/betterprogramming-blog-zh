# Jetpack Compose:可滚动组件

> 原文：<https://betterprogramming.pub/jetpack-compose-scrollable-components-2edb34aa9094>

## 实现 RecyclerView 的新方法

![](img/cf1e591ef50044e368b9c14d02d7c51a.png)

作者照片。

在本文中，您将学习如何在 [JetPack Compose](https://developer.android.com/jetpack/compose) 中创建可滚动列表。Compose 最近从开发阶段进入了 alpha 阶段，所以我们将讨论一个不同于我以前关于列表的 JetPack Compose 文章的实现。坚持到最后，了解新的 Compose 组件。

# 介绍

在我们通常的 Android UI 设计中，也就是 XML 布局，我们有像`RecyclerView`这样的小部件来有效地显示大型数据集。我同意`RecyclerView`已经解决了许多痛点，但是问题是有太多的样板代码。

我们需要覆盖一堆功能— `onCreateViewHolder`、`onBindViewHolder`、`getItemCount`等等。在这些函数中，我们需要一直编写相同的代码模板。此外，如果您需要任何额外的功能，我们需要自己实现它们。我同意这是为了最好的性能，但这太难了。

这就是我认为 JetPack Compose 大放异彩的地方。构建声明性 UI 使开发人员的上下文与相同的编程语言(Kotlin)保持一致，并减少了样板代码。

# 可滚动合成组件

所以今天的主题是 JetPack Compose 中的可滚动组件。Compose 提供了几种显示垂直或水平列表的方法。

*注意:Jetpack Compose 是一个正在积极开发的框架。每天，数百名来自谷歌的聪明人都在为此努力。我们在本文中使用的代码可能会随着更新而改变。*

## ScrollableRow 或 ScrollableColumn

这些相当于我们现在环境下的`scrollview`。当内容的高度(或宽度)大于屏幕尺寸时，就会滚动内容。

## LazyRowFor 或 LazyColumnFor

这些垂直(或水平)滚动列表只对当前可见的项目进行组合和布局，这类似于传统框架中`RecyclerView`及其适配器的行为。

# 让我们开始编码吧

我们的目标是显示一个电影列表，所以我们需要创建一个数据类，包含显示电影项目所需的所有属性。为了简单起见，我们将显示电影的名称和图像。

首先，让我们创建数据类:

然后，我们需要创建一个可组合的函数，将电影类和 click-listener 作为参数:

这里我们用`Row`横向排列电影图像和名称。要显示文本，我们只需基本的文本兼容。对于图像，我们使用`Coil`。现在我们需要膨胀可滚动组件中的`MovieView`。

# ScrollableRow 或 ScrollableColumn

我们试试`ScrollableColumn`显示电影列表。我说过，这些就像`scrollview`。我们需要使用列表在`ScrollableColumn`组件中添加一些视图。在此之前，让我们看看语法:

这与 Jetpack Compose 中的任何其他组件一样——一堆与滚动、子元素和修饰符相关的参数，用于根据我们的需要调整 UI。

要实现一个简单的可滚动项目列表，我们只需创建一个可组合的函数，并传递要显示的项目列表。如果您有任何其他需求，如单击或回调，请将高阶函数或侦听器与列表一起传递:

在这个函数中，我们刚刚声明了一个`ScrollableColumn`,用一个修改器来填充屏幕的最大尺寸。在`ScrollableColumn`块中，我们迭代了电影列表，并膨胀了`MovieView`可组合函数。为了提高美观，我们在每个项目后添加了分隔条。

# LazyRowFor 或 LazyColumnFor

可滚动组件的懒惰版本是最简单的。让我们试试`LazyColumnFor`显示电影列表。我说过，这些组件和`RecyclerView`差不多。我们需要使用列表在`LazyColumnFor`组件中添加一些视图。在此之前，让我们看看语法:

来到实现部分，和`ScrollableColumn`差不多。看一看:

我们可以注意到的惰性列表实现与 scrollables 的唯一区别是，我们不需要创建循环并手动膨胀列表项。相反，`LazyColumnFor`和`LazyRowFor`将为我们做的是只放置对用户可见的视图。

组合组件将处理机制的其余部分，以便有效地加载项目。这不是很棒吗？不再担心视图持有人模式。

# 结论

正如我所说，Jetpack Compose 刚刚转移到 alpha。有一些关键的功能仍然需要实现，比如基于位置或视图滚动列表。但是不要担心:Compose 正在快速发展，开发人员正在习惯它。也许到 2020 年底，我们可以期待一个 Jetpack Compose，拥有构建花哨应用程序所需的所有特性。

目前就这些。希望你学到了有用的东西。感谢阅读。