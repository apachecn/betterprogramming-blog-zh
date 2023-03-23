# 在 Android 中使用 Jetpack 组合导航

> 原文：<https://betterprogramming.pub/navigation-with-jetpack-compose-in-android-ab84d0984ade>

## 在可组合函数之间导航，使用自定义对象作为参数，等等

![](img/37be9de392412bc02359591c78bbeafc.png)

照片由[田口孝宏](https://unsplash.com/@tak_tag?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

在本文中，您将了解如何在 Jetpack Compose 中实现导航。您还将学习如何在导航时传递原始数据类型和自定义数据对象。

*注意:Jetpack Compose 最近已转移到测试版。这意味着 API 结构将不再有任何剧烈的变化。因此，这将是学习如何使用下一代 UI 套件进行 Android 开发的大好时机。导航是 Android 开发中的核心任务之一，所以请欣赏这篇文章。*

*您需要安装北极狐版本的 Android Studio Canary 才能使用 Jetpack Compose Beta。*

# 介绍

Jetpack Compose 可以与像`Fragments`这样的 Android 组件互操作。因此，如果您有一个现有的项目，您决定迁移到 Jetpack Compose，那么导航不会有任何变化。

但是，如果您决定迁移整个应用程序，而不使用任何 Android 组件，比如`Fragments`，或者创建一个只有组件的全新应用程序，那么本文就是为您准备的。

今天，我们将使用`Navigation`架构组件来实现屏幕之间的导航。如果你不熟悉，我强烈推荐你阅读下面这篇文章:

[](/jitpack-navigation-component-in-android-944165c35f54) [## Android 中的 JetPack 导航组件

### 近十年后，Android 团队为开发者引入了一个新的导航系统

better 编程. pub](/jitpack-navigation-component-in-android-944165c35f54) 

# 在 Compose 中创建一个具有两个屏幕的应用程序

与传统的 Android 开发不同，Jetpack Compose 没有任何像`Fragments`和`Activities`这样的约束。我们可以使用一个可组合的函数来表示屏幕的一部分或者整个屏幕。当你从头开始构建一个项目时，我建议只使用 composables，因为这样你就可以利用 Compose 的力量。

为了更好地理解 Jetpack Compose 中的导航，让我们构建一个带有两个屏幕的`Subscription`应用程序: `SubscriptionsListScreen`和`SubscriptionDetailsScreen`。看一下代码:

在这段代码中，我们有三个可组合的函数，代表应用程序中的两个屏幕:

*   `SubscriptionsListScreen`是一个表示订阅列表屏幕的可组合函数，它将订阅列表作为一个参数。
*   `SubscriptionsListItem`是一个表示订阅列表项设计的可组合函数，它将`Subscription`数据类作为参数。
*   `SubscriptionDetailsScreen`是一个可组合的函数，表示用户从列表中导航到的订阅的细节，它将`Subscription`数据类作为参数。

这里，`SubscriptionsListScreen`和`SubscriptionDetailsScreen`代表应用程序的列表和详情屏幕。与传统开发相比，它们充当`Activity`或`Fragment`布局。同时，`SubscriptionsListItem`代表屏幕的子部分，类似于`RecyclerView`适配器项目。

我们在这里的任务是在列表和细节屏幕之间创建一个导航框架，绕过订阅信息。

# 综合

为了让`Navigation`组件与 Compose 一起工作，我们必须在 dependencies 节点下添加下面一行。这将增加导航与可组合函数的兼容性:

# 导航框架

在进入`Navigation`组件之前，你应该了解一下 Android 中的`remember`。在声明式 UI 系统中，代码本身描述了 UI。我们需要描述任何时间点的 UI——不仅仅是初始时间。

为了解决这个问题并以最简单的方式有效地维护状态，Jetpack Compose 提供了`remember`。`remember`基本上在任何给定的时间点跟踪状态。要更好地理解和了解 Jetpack Compose 中的`remember`和其他组件，请阅读以下文章:

[](/jetpack-compose-components-part-2-2b3eb135d294) [## Jetpack 合成组件(第 2 部分)

### 查看图标、图像、记忆、间隔、单选按钮、加载器、开关和复选框以及滑块

better 编程. pub](/jetpack-compose-components-part-2-2b3eb135d294) 

现在是时候开始为组件实现导航框架了。通过集成前面提到的库，`Navigation`组件与开箱即用的组件完全兼容。

我们首先需要了解的是创建一个`NavHostController`来处理`ComposeNavigator`的添加的`rememberNavController`可组合函数。这将返回`NavHostController`，我们可以用它在可组合函数之间导航。

```
val navController = *rememberNavController*()
```

接下来，我们需要创建一个将所有屏幕作为对象的密封类。对于我们的例子，我们有两个对象:`SubscriptionsList`和`SubscriptionDetails`。看一看:

是时候创建我们的主可组合函数了，所有的组合屏幕都嵌入在`NavHost` 可组合函数中。一旦调用了这个函数，就可以从提供的`navController`中导航到给定的`NavGraphBuilder`中的任何可组合组件。在我们的例子中，我们有两个可组合的屏幕，我们需要在`NavHost`中声明它们，如下所示:

`composable`是`NavGraphBuilder`上的扩展函数，用来给`NavGraphBuilder`添加一个可组合的。下一步是传递参数。`SubscriptionDetailsScreen`可组合函数期望`Subscription`数据类作为参数来显示细节。

请记住，我们需要给我们的`MainActivity`类中的`SubscriptionsMain`组件充气。

对于参数和深层链接支持，`composable`扩展函数还有两个 list 类型的参数，默认值为`emptyList` *。*看看功能就有更好的主意了:

`arguments`和`deepLinks`参数是可选的。在我们的例子中，我们需要将参数传递给细节，但是主屏幕不需要。基本上，我们需要将字符串类型的订阅名称作为参数传递。看一看:

现在，如果你看到我们更新的`SubscriptionsMain`函数，在 details composable 中，我们已经将字符串类型的订阅名称声明为一个参数，并且我们正在 lambda 中使用该参数。

# 在可组合函数之间导航

一般来说，要在任何 Android 组件之间导航，我们需要`navController`。可组合函数也是如此。为了从另一个组件中触发或启动一个组件，我们需要`navController`。

与传统方法不同，composables 还不能找到`navController`。所以我们需要传递我们在`SubscriptionsMain`函数中创建的`navController`。如果一个可组合函数需要触发另一个可组合函数，源可组合函数应该接受`navController`作为参数。在我们的例子中，`SubscriptionsListScreen`和`SubscriptionsListItem`应该有`navController`。看看更新的组件:

现在我们需要将`navController`传递给更新的函数:

我们可以从订阅列表项导航到详细信息。看一看:

仅此而已。我们已经学习了如何设置导航框架，以及如何通过参数在屏幕之间导航。

# 自定义对象作为参数

使用 Jetpack Compose 导航仍然在 alpha 中，所以像传递`parcelable`对象这样的特性还不被支持。作为一种变通方法，我们可以将 object 类转换成一个`gson`字符串，并在另一端提取对象。

首先，我们需要在 dependencies 节点下添加下面一行，以便在项目中包含`gson`库:

```
implementation **"com.google.code.gson:gson:2.8.6"**
```

然后我们需要将自定义对象转换成一个字符串，并将该字符串作为参数传递。看一看:

```
val gsonString = Gson().toJson(**subscription**)
navController.***navigate***("details/$**gsonString**")
```

另一方面，我们可以使用`fromJson`函数获得`Subscription`类对象。看一看:

# 奖金

要了解有关 Jetpack Compose 的更多信息，请查看以下文章:

*   [“Android 版 Jetpack Compose 中的主题化”](/theming-in-jetpack-compose-for-android-667d42b8279c)
*   [“Jetpack 合成组件(第 1 部分)”](/jetpack-compose-components-part-1-a80717dbfb59)
*   [“Jetpack Compose——一种在 Android 中创建素材 UI 的简单新方法”](https://medium.com/better-programming/jetpack-compose-a-new-and-simple-way-to-create-material-ui-in-android-f49c6fcb448b)
*   [“使用服务器驱动的用户界面构建 JetPack”](https://medium.com/android-dev-hacks/jetpack-compose-with-server-driven-ui-396a19f0a661)

目前就这些。希望你学到了有用的东西。感谢阅读。