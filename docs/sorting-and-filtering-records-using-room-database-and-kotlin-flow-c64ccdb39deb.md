# 使用房间数据库和 Kotlin 流程对记录进行分类和过滤

> 原文：<https://betterprogramming.pub/sorting-and-filtering-records-using-room-database-and-kotlin-flow-c64ccdb39deb>

## 轻松构建反应式 Android 应用

![](img/8b8edfd3eb29bdd224c2941a9e1cc092.png)

由[凯利·西克玛](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，您将学习如何对本地存储记录实现排序和过滤功能。在这里，我们使用科特林流使事情反应和高效。

# 介绍

在 2021 年的 android 开发中，使用 Room library 进行本地存储是非常标准的做法。房间数据库很酷的一点是它支持跨协程和 Rxjava，我最喜欢的是带有 Kotlin 流的房间数据库。一个简单的方法，使 Android 应用程序简洁和反应。

如果您是房间数据库的新手，我强烈推荐您阅读下面的文章，从基础到高级了解您需要了解的一切:

*   [《安卓房间持久性库》🔗科特林协程](https://medium.com/@sgkantamani/android-room-persistence-library-kotlin-coroutines-26d53a09d384)
*   **[“如何通过 Kotlin 流使用房间持久性库”](https://medium.com/better-programming/how-to-use-the-room-persistence-library-with-kotlin-flow-c73f461a0819)**

**既然您是房间数据库方面的专家，那么是时候探索实现一些高级但常见的功能了，比如通过房间数据库进行排序和过滤。使代码高效和可维护的四件事。**

1.  **排序和过滤应该由房间数据库完成，我们的视图模型或 repos 不应该有任何相关的逻辑。**
2.  **事情可以是任意组合——用户可能想要过滤和排序或者只排序或者只过滤。实现应该支持所有可能的情况。**
3.  **实现应该简洁明了。**
4.  **UI 应该一直与数据库同步。假设如果添加或删除任何新记录，它应该反映在 UI 上，而不需要考虑现有条件的任何额外工作。**

# **房间实体**

**就本文而言，假设我们正在构建一个订阅应用程序，用户可以在其中保存他们的订阅列表，包括名称、金额、类别等细节(从现在开始我们称之为标签)。所以我们有两个实体类— `Subscription`和`Label`。看一看:**

# **房间叠纸**

**数据访问对象(Dao)是我们编写 SQL 可以理解的与房间数据库相关的查询的地方。这里我们需要实现与过滤和排序相关的查询。排序有两种变体:升序和降序。**

## **过滤器**

**首先，让我们创建一个简单的过滤查询，我们使用房间提供的`WHERE`和`IN`操作符:**

```
@Query("SELECT * FROM Subscription WHERE lableId IN (:lableID)")
fun getLabledSubscriptions(lableID : Int): Flow<List<Subscription>>
```

**将返回类型保持为 flow 会使您的 android 应用程序对房间数据库中的更改做出反应。**

## **分类**

**接下来，我们将讨论简单排序，正如我所说，排序有两种变体。所以我们有两个函数——一个用于名称，另一个用于数量。为了方便起见，每个函数都将 Int 作为输入。**

*   **如果输入是 1，那么我们将按升序排序。**
*   **如果输入是 2，那么我们将降序排列。**

**我们将使用`ORDER BY`与`CASE`和`WHEN`的组合:**

**排序查询**

## **过滤和排序组合**

**这很简单，我们将上面的两个查询组合起来，再创建两个查询，对名称和金额进行过滤和排序。看一看:**

**使用排序查询进行筛选**

**这就是我们对复杂事物的第一部分所做的全部工作，接下来是流程和视图模型的复杂性。**

# **视图-模型+流程**

**非常重要的是，在进一步学习之前，你要对这两件事有一个基本的了解。因此，如果您是第一次接触它们，请在继续之前阅读以下文章:**

*   **[“协程 StateFlow 简介”](/introducing-coroutines-stateflow-ca1b13133265)**
*   **[“在 Android 中使用视图模型的最佳实践”](/best-practices-for-using-viewmodels-in-android-e986b25dc78f)**

**让我们进入逻辑部分:**

## **分类**

**首先，我们有两个枚举类来表示**

*   **排序类型(升序、降序或无)**
*   **排序字段(名称、金额或无)**

**看一看:**

```
enum class SortType {
    *ASCENDING*, *DESCENDNG*, *NONE* }

enum class SortField {
    *NAME*, *AMOUNT*, *NONE* }
```

## **可变状态流和实时数据**

**我们必须创建一个科特林`triple`类型的`MutableStateFlow` 。科特林三元组只不过是价值的三元组。这里，值的类型将是`SortField`、SortType 和 Int(要过滤的标签 id)。看一看**

```
var lbaleID : Int? = null
val sortFlow = *MutableStateFlow*(Triple(SortField.*NONE*,SortType.*NONE*,lbaleID))
```

**我个人觉得不太喜欢用`MutableStateFlow`来连接 UI，所以用`asLiveData` 扩展函数把`MutableStateFlow` 转换成`LiveData` *。一旦你完成了视图模型中的逻辑，就会看到其中的编码部分。***

## **平面地图最新**

**`flatMapLatest`返回一个流，每当原始流发出一个值时，该流切换到由转换函数产生的新流。当原始流发出新值时，由转换块产生的前一个流被取消。**

**在我们的例子中，原始流是— `sortFlow`，转换函数将是房间数据库查询的执行。因此，当我们对`sortFlow`应用`flatMapLatest`时，在 lambda 中，你将始终获得三个字段的最新值。这是我们相应地执行查询和恢复列表的地方。返回类型将是带有`Subscription`列表类型的`Flow`。**

**来到 lambda 逻辑，使用第一个参数，我们确定需要确定哪一个字段是哪种过滤器。然后第二个参数用于定义排序类型，最后，第三个参数指示我们是否需要过滤带有任何标签`id`的列表。看一下代码:**

**filter map 用于排序和筛选列表的最新逻辑**

**当我们处理从房间获得数据和更新条件的流动时，几乎所有的东西都是反应性的。现在是时候从视图中观察这一点了，正如我说过的，我不喜欢视图中的流，所以它被转换为实时数据。**

```
@ExperimentalCoroutinesApi
val subscriptionsList = subscriptionsListFlow.*asLiveData*()
```

## **从视野中观察**

**基本上最难的部分已经完成了，如果你正在使用 Jetpack Compose，我推荐使用`observeAsState`来让事情变得更有反应性。如果你使用传统的用户界面，那么你可以观察来自 android 组件的实时数据。**

```
val data  = subscriptionsListViewmodel
    .subscriptionsList.***observeAsState***(*emptyList*<Subscription>())
```

## **Lambda 更新排序**

**要根据用户选择更新排序或过滤器，您可以在视图模型中创建一个简单的 lambda，并更新 sortFlow，如下所示:**

**更新排序和筛选选项**

# **结论**

**目前就这些。希望你学到了有用的东西。感谢阅读。如果你喜欢看这篇文章的视频格式，这里是 YouTube 视频:**