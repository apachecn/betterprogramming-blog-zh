# GridView 和 LazyColum 与 Jetpack Compose 的集成

> 原文：<https://betterprogramming.pub/gridview-and-lazycolum-integration-with-jetpack-compose-e90849aeb6d3>

## 在 Android 中构建惊人的网格布局

![](img/4c56bcd8b2d01bd8deab7a0e16a2bdcb.png)

[Sharon McCutcheon](https://unsplash.com/@sharonmccutcheon?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/grid?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

如果你想给你的应用程序添加一个网格视图，这个组件非常有用。

不幸的是，它不能放在另一个垂直滚动容器中，因为它有垂直滚动行为。

如果您尝试将`LazyColumn`与`LazyVerticalGrid`连接，您将会看到以下异常:

```
java.lang.IllegalStateException: Vertically scrollable component was measured with an infinity maximum height constraints, which is disallowed. One of the common reasons is nesting layouts like LazyColumn and Column(Modifier.verticalScroll()). If you want to add a header before the list of items please add a header as a separate item() before the main items() inside the LazyColumn scope. There are could be other reasons for this to happen: your ComposeView was added into a LinearLayout with some weight, you applied Modifier.wrapContentSize(unbounded = true) or wrote a custom layout. Please try to remove the source of infinite constraints in the hierarchy above the scrolling container.
```

在本文中，我们将解决这个问题，并使整个屏幕可滚动。

在我的 [WallUp](https://github.com/Enes-Kayiklik/Wall-Up) 应用程序中，我有一个`HomeScreen`显示几个项目。有些是网格，有些不是。当我试图在应用程序中实现这个屏幕时，我遇到了上述问题。

我希望整个屏幕可以滚动网格项目，但我不能将`LazyGridView`与 LazyColumn 合并。

## **解决方法**

想了一会儿，突然想到网格结构中的所有条目都是分行的。所以我们可以使用行组合来实现这个结构。

## **实施**

首先，我们需要在我们的 app 中加入`gridItems`扩展功能。

```
"data" is a list of items that we are going to display as a grid. "columnColunt" is a count of grid inside Row.
```

现在我们可以在`LazyColumn`中使用网格项，如下所示:

# **结论**

在这篇短文中，我们研究了其他垂直可滚动组件的`LazyVerticalGrid`和垂直滚动问题。

我们看到，试图将可组合组件与其他垂直可滚动的可组合组件结合会导致应用程序崩溃。

作为一种解决方法，我们使用可组合行解决了这个问题。

我希望你有一些收获，在其他文章中再见。