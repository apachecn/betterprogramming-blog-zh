# 在 Jetpack Compose 中使用 Scaffold 和 Slot APIs

> 原文：<https://betterprogramming.pub/using-the-scaffold-and-slot-apis-in-jetpack-compose-3e2e1b0e0550>

## 构建出色的 Android 应用程序

![](img/112c87a2d9717964823ac8128f9cbb28.png)

[Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [AltumCode](https://unsplash.com/@altumcode?utm_source=medium&utm_medium=referral) 的照片。

在本文中，我们将学习 Scaffold 和 Slot APIs，以及如何使用它们在 Android 应用程序中设计一个全功能的屏幕。您将学习如何创建顶部栏、底部导航视图、抽屉和屏幕的主要内容。最重要的是，你会学到脚手架是如何将它们连接在一起的。

在本文的最后，您会发现 Github 项目。请随意探索代码。

# 介绍

Jetpack Compose 是谷歌最近试图让 Android 开发者轻松构建 UI 的尝试之一。

> “Jetpack Compose 是一个用于构建原生 Android UI 的现代工具包。Jetpack Compose 以更少的代码、强大的工具和直观的 Kotlin APIs 简化并加速了 Android 上的 UI 开发。”— [安卓开发者](https://developer.android.com/jetpack/compose/tutorial)

Jetpack Compose 最近进行了测试。这意味着 API 结构将不再有任何剧烈的变化，因此这将是学习如何使用这个下一代 UI 套件进行 Android 开发的大好时机。

您需要安装 Android Studio Canary，北极狐(2020.3.1) 版本才能使用 Jetpack Compose beta。

# 先决条件

在进一步学习之前，您必须具备如何使用 Jetpack Compose 的基本知识。如果你是作曲新手，我强烈推荐阅读以下文章:

*   [“Jetpack 编写组件(第 1 部分)”](/jetpack-compose-components-part-1-a80717dbfb59)
*   [“在 JetPack Compose 中构建 Android 布局”](/build-android-layouts-in-jetpack-compose-bda2e0837f5e)
*   [“面向 Android 的 Jetpack 合成中的主题化”](/theming-in-jetpack-compose-for-android-667d42b8279c)
*   [“底部导航栏与 Jetpack 组合”](/android-bottom-navigation-bar-with-jetpack-compose-8c2aa02b933d)

# 什么是脚手架？

[Scaffold](https://developer.android.com/reference/kotlin/androidx/compose/material/package-summary#scaffold) 是一个材质设计布局结构，它提供了一种方法来容纳不同的材质组件，如应用程序栏、底部导航视图、抽屉、浮动操作按钮等。通过实施布局结构策略来确保所有这些组件无缝地协同工作。所有这些子组件都被称为插槽—打个比方。

除了`Scaffold`Compose 以外，Compose 还提供了几个如`BackdropScaffold`、`BottomSheetScaffold`等。来处理像底层这样的复杂用例。首先，我们来看看`Scaffold`可组合函数:

脚手架

*   `modifier` —顾名思义，`Modifier`是一种修改组件某些方面的方式，这导致了它在屏幕上的显示方式。要了解更多关于`modifier`的信息，请阅读以下文章:

[](/jetpack-compose-components-part-3-ce5ae661f3d6) [## Jetpack 组件(第 3 部分)

### 关于修改器你需要知道的一切

better 编程. pub](/jetpack-compose-components-part-3-ce5ae661f3d6) 

*   `ScaffoldState`**—`Scaffold`可组合组件的状态。它包含基本屏幕状态(如抽屉配置)以及布局完成后组件的尺寸。基本上，它用于获取抽屉的状态，并执行打开、关闭等操作。**

```
class ScaffoldState(
    val drawerState: DrawerState,
    val snackbarHostState: SnackbarHostState
)
```

*   **`topBar` —设计 app 的顶栏，很简单的可组合功能。**
*   **`bottomBar` —用于在屏幕上显示底部导航视图。要了解更多信息，请阅读以下文章:**

**[](/android-bottom-navigation-bar-with-jetpack-compose-8c2aa02b933d) [## Android 底部导航栏，带有 Jetpack 组件

### 定制您的导航路线

better 编程. pub](/android-bottom-navigation-bar-with-jetpack-compose-8c2aa02b933d) 

*   `snackbarHost` —为了在已有的 UI 上有效地处理`snackbar`，我们需要使用`snackbarHost`。要了解更多信息，请阅读以下文章:

[](https://medium.com/the-devops-corner/design-snackbars-and-alert-dialogs-in-jetpack-compose-f6aa314c498d) [## 在 Jetpack Compose 中设计零食条和提醒对话框

### 在现代 android 应用程序中，显示像 snackbar 或 dialog 这样的警告是一项常见的任务。在这篇文章中，你是…

medium.com](https://medium.com/the-devops-corner/design-snackbars-and-alert-dialogs-in-jetpack-compose-f6aa314c498d) 

*   `floatingActionButton`、`floatingActionButtonPosition`、`isFloatingActionButtonDocked` —这些标志用于管理带有现有 UI 的 fab 按钮。
*   这是一个简单的可组合函数，用于设计抽屉布局。`drawerGesturesEnabled`、`drawerShape`、`drawerElevation`、`drawerBackgroundColor`、`drawerContentColor`、`drawerScrimColor`用于管理抽屉的不同 UI 元素。
*   `backgroundColor`和`contentColor` —用于定义屏幕主视图的背景和内容颜色。
*   `content` —这是一个用于设计屏幕主要内容的可组合功能。

# 创建一个基本的脚手架

让我们创建一个简单的`Scaffold`可组合函数，将`topBar`、`bottomBar`和`drawerContent`参数作为空函数。用简单的文字设计应用程序的主要内容。看一看:

# 设计组件

现在我们需要创建`ScaffoldState`和`CoroutineScope` ，使用`remember`来维护重组后的状态。要了解更多关于`remember`的信息:

[](/jetpack-compose-components-part-2-2b3eb135d294) [## Jetpack 合成组件(第 2 部分)

### 查看图标、图像、记忆、间隔、单选按钮、加载器、开关和复选框以及滑块

better 编程. pub](/jetpack-compose-components-part-2-2b3eb135d294) 

```
val scaffoldState = ***rememberScaffoldState***()
val scope = ***rememberCoroutineScope***()
```

现在让我们开始一步一步地设计可组合的。

## 顶部栏

`topBar`就是我们用来显示在屏幕上方的工具栏。让我们设计一个简单的带有菜单图标和标题的`TopAppBar`。点击菜单图标，我们需要使用`ScaffoldState`打开抽屉。`open`是一个挂起函数，所以我们需要使用协程作用域。看一看:

屏幕的顶栏

## drawer 内容

`drawerContent`是一个简单的可组合函数，用于设计抽屉布局。这里，我们将显示一个简单的项目列表。看一看:

```
@Composable
fun DrawerContent() {
    for ( i in 0 *until* 5 ){
        *Text*(text = "Item $i")
    }
}
```

## 底部条

最后，是时候设计屏幕的最后一个组件了:底部导航视图。出于本文的考虑，我将实现一个简单的底部导航视图。如果你想了解更多关于如何有效地设计它，我强烈推荐你阅读这篇[文章](/android-bottom-navigation-bar-with-jetpack-compose-8c2aa02b933d)。

底部导航视图设计

# 脚手架设计

既然我们已经设计了子组件，最后是时候通过 Scaffold 将它们集合在一起了。看一看:

支撑屏幕上所有子组件的支架

现在我们只需要从`MainActivity`中的`setContent` 调用`ScaffoldSample`即可。看一看:

仅此而已。我们完了！

# 奖金

下面是 GitHub repo 链接，它实现了持久化和模式化的底层表单。随意探索:

[](https://github.com/SG-K/Jetpack-Compose-Scaffold) [## SG-K/Jetpack-Compose-脚手架

### 在 GitHub 上创建一个帐户，为 SG-K/Jetpack-Compose-Scaffold 的开发做出贡献。

github.com](https://github.com/SG-K/Jetpack-Compose-Scaffold) 

我最近看到了 Satya Pavan Kantamani 发表的这篇关于 Android 前台服务的优秀文章。强烈建议:

[](/what-is-foreground-service-in-android-3487d9719ab6) [## 如何在 Android 中实现前台服务

### 了解什么是前台服务，并将其集成到您的 Android 应用程序中

better 编程. pub](/what-is-foreground-service-in-android-3487d9719ab6) 

目前就这些。希望你学到了有用的东西。感谢阅读。**