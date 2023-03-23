# Android 中适配器的演变

> 原文：<https://betterprogramming.pub/evolution-of-adapters-in-android-2e2ff58c0f98>

## 回顾一下我们在过去十年中取得的进步

![](img/34047aad7835a68df05548b686903f97.png)

照片由[émile Perron](https://unsplash.com/@emilep?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 概观

如果你从事 Android 开发已经有一段时间了，你可能需要在职业生涯的不同阶段实现一个视图列表。

首先，您可能使用了`ListView` 和`GridView` 在一个网格结构中垂直和水平地显示项目列表。然后是`Recyclerview`，它带来了对现有模式的许多增强和像`viewtypes`这样的新实现——这是 Android 适配器的革命性改进。最近`ListAdapter`出现了，主要集中于增强性能以提供流畅的体验，即使列表上有数千个项目。

知道我们今天用什么很重要。同样重要的是要知道*为什么*我们会使用我们今天使用的东西。所以我们来看看 Android 中适配器的历史。

# 基本适配器

这是 Android 中适配器的开始。在基本适配器中，我们没有像现代 Android 开发中那样的`viewtypes` 或`itemdecorator` 。让我们来看一个简单的基本适配器:

这里，`getView`是完全负责创建和膨胀每个视图的函数。我们必须手动检查视图是否已经创建，然后使用布局中的视图，这是目前最好的方法。据我所知，要扩展不同的视图类型，我们需要在相同的布局中设计所有的视图，并且我们必须根据类型切换可见性。这是一个很大的工作量，而且视图不会被适当地回收。这些事情限制了 Android 开发者创建像我们这些天在 Instagram 和 Twitter 上看到的那些花哨的列表。

另一个主要缺点是我们必须使用网格适配器来显示网格中的视图。就我个人而言，我在 2015 年末职业生涯开始时会使用`BaseAdapter` 和`GridAdapter` 一段时间——相信我，它们很烂！

这并不是说它们的性能很差，而是我们必须一直做每一点点工作，即使这样也会出现 OOM(内存不足)异常之类的问题。那是一场噩梦。

# RecyclerView 适配器

带着悲惨的`baseAdapter`前进了几个月，有一天我认识了奇迹般的`recyclerviewAdapter`。这对我来说是一个游戏规则的改变。它不仅解决了`baseAdapter`中的问题，还为新的可能性打开了大门，如`viewTypes`、`itemdecorators`，将垂直、水平和网格列表与`layoutmanager`相结合，等等。

让我们来看一个简单的`recyclerview` 适配器:

在这个`recyclerview`适配器中，我们有两个不同的功能:`onCreateViewHolder` 和`onBindViewHolder`。这些方法只创建视图一次，并在需要时展开视图。这提高了性能，因为`recyclerview`只在必要时创建和使用视图。如果列表中有 100 个条目，它不会加载所有条目，而是只加载当前可以显示给用户的视图数量。在滚动时，`recyclerview`适配器只将用户可见的视图存储在内存中，这使得它更加高效和直观。

## 视图类型

除了上述优点，`recyclerview`也为新的实现打开了大门，通过`viewTypes`，在同一个适配器中构建嵌套列表和不同的布局。

在上面的适配器中，我们在该方法中实现了如下所示的`getItemViewType()`函数和代码。这是区分要展开的视图的`viewType`逻辑。

```
@Override
public int **getItemViewType**(int position) {
    if (employees.get(position).isEmail) {
        return TYPE_EMAIL;

    } else {
        return TYPE_CALL;
    }
}
```

我们这里经过的`viewType`在`onCreateViewHolder`里有。通过使用它，我们可以放大不同的视图。

## 支持网格和列表

在 recycler-view 中有一个新的概念叫做布局管理器，它允许我们在没有任何复杂性的情况下集成网格和列表。

*   `LinearLayoutManager`:支持垂直和水平列表。
*   `StaggeredLayoutManager`:支持交错列表，就像我们在 Pinterest 应用中看到的那样。
*   `GridLayoutManager`:支持像图库一样的显示网格。

看看用布局管理器改变列表结构有多快:

## 动画片

列表中的动画是`RecyclerView`附带的另一个很酷的特性。除了`notifyDataSetChanged`之外，任何一个`notify`功能被触发，`RecyclerView`都会进行相应的动画。这包括`notifyItemChanged`、`notifyItemInserted`、`notifyItemMoved`和`notifyItemRemoved`。

## 取景器模式

`ViewHolder`模式用于加速列表的渲染，使其流畅运行。在每次呈现列表项时使用`findViewById`是很昂贵的——它必须深入到你的布局层次结构中，并且实例化对象。由于列表在滚动过程中会频繁地重绘其项目，这样的开销可能会影响性能。

`RecyclerView`毫无疑问是 Android 中革命性的小工具，但一切都有下一步。对于`RecyclerView`也是如此，下一步称为`ListAdapter`。

# 列表适配器

`RecyclerView`适配器不是`BaseAdapter`的扩展。另一方面，`ListAdapter`是`RecyclerView`适配器的下一代(它扩展了`recyclerview-adapter`)。`ListAdapter`主要致力于提高性能——即使是海量数据，如无止境的 Instagram 和 Twitter feeds 自动播放视频。

使用`RecyclerView`时，如果添加或删除了任何新项目，或者现有项目发生了任何变化，适配器本身负责验证列表。有了`ListAdapter`，这项工作现在由`[AsyncListDiffer,](https://developer.android.com/reference/android/support/v7/recyclerview/extensions/AsyncListDiffer.html)`来完成，它是一个助手，通过后台线程上的`[DiffUtil](https://developer.android.com/reference/android/support/v7/util/DiffUtil)`来计算两个列表之间的差异。看看它的基本用法:

它有两个主要功能:

## areItemsTheSame

该函数决定是放大当前视图还是创建一个新视图。如上所示，我们需要像比较 id 一样比较主键值，它对于列表中的每一项都是唯一的。

## 内容相同

仅当此函数返回 true 时，列表适配器才刷新视图。这是因为我们可能在项目中有许多不影响 UI 的变量，所以这些变量的变化不应该影响 UI。为了有效地工作，我们需要比较我们在 UI 中使用的条目变量。

通过这种方式，我们可以控制何时在列表中呈现新项目，以及何时更新项目中的数据。太酷了，对吧！

# 奖金

要了解更多关于如何使用最新的列表适配器在 Kotlin 中创建反应式和异构适配器的信息，请阅读本文。

感谢阅读。