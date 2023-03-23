# 为 Jetpack 撰写创建带索引的 LazyColumn

> 原文：<https://betterprogramming.pub/lazycolumn-with-indexing-for-jetpack-compose-3456023d19d5>

## 在基于 Jetpack Compose 的 android 应用中复制 RecyclerView

![](img/877b59cf8ad00d68d6b61312f90f0035.png)

照片由 [Adrian Trinkaus](https://unsplash.com/@adrian_trinkaus?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

`RecyclerView`可能需要“这个”和“那个”文件以及其中的许多配置，但是没有人会不同意它是一个成熟的类，并且许多库都是在考虑`RecyclerView`的情况下构建的。

我希望我能对`ListView`和`ListActivity`说同样的话，但是我不会！关于我们过去的罪恶已经说得够多了，我们应该坚持面向未来，因为现在是**时刻！**

除了[双头懒人柱](https://medium.com/@tsapalos.v/double-header-lazycolumn-in-jetpack-compose-5cbbcece75ec)我真的很想念侧面有索引的`RecyclerView` s。但是，现在是 Jetpack 编写时间，所以可以做一件事——用索引创建我自己的自定义`LazyColumn`。

该方法有两个几乎相同的实现。两者的主要区别在于`IndexedLazyColumn`实现需要一个完整的`LazyColumn`及其`LazyListState`作为参数，而`IndexedDataLazyColumn`需要`itemContent`参数，来自`items`参数来自`LazyColumn`。你感到困惑吗？别担心，我想在这篇文章结束时，你会对前面的句子有一个很好的观点。

## 简单的数据实现

下面你可以看到没有主体的`IndexedDataLazyColumn`功能。

逐个读取参数:

*   `indices`:索引项的简单列表(例如，1..10，A..z 等)。
*   `data`:包含您的数据项的列表(例如电影、姓名、汽车制造商、购物清单等)。
*   `modifier`:只是另一个`Modifier`就像你和其他组件一起使用的那个。
*   `predicate`:这里的这个λ，是`indices`和`data`之间的连接。您可以自由地编写自己的逻辑，但是您必须小心，总是从`indices`列表中添加一个项目作为参数，并从`data`返回一个项目的索引。
*   `mainItemContent`:这是将在屏幕上显示`data`项目的组件。
*   `indexedItemContent`:这是将在屏幕上显示`indices`项目的可组合组件。

这里有一个例子

注意`predicate`将索引项(字母‘A’到‘Z’)与人名的第一个字母连接起来的方式。

## 全新 LazyColumn 实现

下面你可以看到没有主体的`IndexedLazyColumn`功能。

逐个读取参数:

*   `indices:`您的索引项的简单列表(例如，1..10，A..z 等)。
*   `itemsListState`:正如我们所说的，你使用你的`LazyColumn`，通过这个值它将与索引连接
*   `modifier`:只是另一个`Modifier`，就像你和其他组件一起使用的那个。
*   `predicate`:这里的这个λ，是`indices`和`data`之间的连接。您可以自由编写您自己的逻辑，但是您必须注意始终将来自`indices`列表的项目作为参数添加，并从`data`返回项目的索引。
*   `lazyColumnContent:` 这是可以在屏幕上显示你的`LazyColumn`的组件。
*   `indexedIntemContent`:这是将在屏幕上显示`indices`项目的组件。

这里有一个例子

请注意`predicate`将索引项(字母“A”到“Z”)与该人姓氏的第一个字母连接起来的方式。另外，请注意如何定义一个`LazyListState`并将其传递给函数(`itemsListState`参数)和您的`LazyColumn` ( `state`参数)。

# 输出

前面的两个示例都呈现相同的数据并具有相同的功能。下面的视频描述了所有这些代码在做什么！

# 我只需要密码

如果你想把这个库添加到你的模块依赖关系中，或者你需要一些如何使用它的例子，请随意浏览整个项目。

![](img/519e4c073d54744c60d61ea08f33e81c.png)