# Android 底部导航栏，带有 Jetpack 组件

> 原文：<https://betterprogramming.pub/android-bottom-navigation-bar-with-jetpack-compose-8c2aa02b933d>

## 定制您的导航路线

![](img/2157774805123c86f67b9fcc6776adac.png)

照片由 [Szabo Viktor](https://unsplash.com/@vmxhu?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 从文章中摘录

在本文中，我们将学习如何使用 [Jetpack Compose](https://developer.android.com/jetpack/compose) 显示底部导航栏。我们还将学习如何维护后台堆栈，底层工具条与后台堆栈的行为，等等。我会在文章结尾给你 GitHub 链接——你可以随意使用它。

# 介绍

Jetpack Compose 是谷歌最近试图让 Android 开发者轻松构建 UI 的尝试之一。

> *“Jetpack Compose 是一个用于构建原生 Android UI 的现代工具包。Jetpack Compose 以更少的代码、强大的工具和直观的 Kotlin APIs 简化并加速了 Android 上的 UI 开发。”—* [*安卓开发者*](https://developer.android.com/jetpack/compose/tutorial)

Jetpack Compose 最近进行了测试。这意味着 API 结构将不再有任何剧烈的变化，因此这将是学习如何使用这个下一代 UI 套件进行 Android 开发的大好时机。

您需要安装 Android Studio Canary，北极狐(2020.3.1) 版本才能使用 Jetpack Compose beta。

# 先决条件

在进一步学习之前，您必须具备如何使用 Jetpack Compose 的基本知识。如果你是作曲新手，我强烈推荐阅读以下文章:

*   "[喷气背包构成组件(第一部分)](/jetpack-compose-components-part-1-a80717dbfb59)"
*   "[在 JetPack Compose 中构建 Android 布局](/build-android-layouts-in-jetpack-compose-bda2e0837f5e)"
*   "[Android 版 Jetpack Compose 中的主题化](/theming-in-jetpack-compose-for-android-667d42b8279c)"
*   "[在安卓系统中用 Jetpack 组合导航](/navigation-with-jetpack-compose-in-android-ab84d0984ade)"

# 综合

将以下数据添加到应用程序级`gradle`文件中，以将 Compose 与所需的库集成在一起:

# 底部导航栏

要创建可组合的底部导航栏，首先创建一个名为`BottomNavigationBar`的 Kotlin 文件，并在该文件中创建一个可组合的函数`BottomBar`。使用 Compose，只需一个函数就可以显示`BottomNavigation` *。*看一看:

```
@Composable
fun BottomBar(){
    *BottomNavigation*(

    )**{

    }** }
```

这是一个常见用例的简单方法(底部条)。不再拖延，让我们从`BottomNavigation`可组合函数开始:

```
@Composable
fun BottomNavigation(
    modifier: Modifier = Modifier,
    backgroundColor: Color = MaterialTheme.colors.*primarySurface*,
    contentColor: Color = *contentColorFor*(backgroundColor),
    elevation: Dp = BottomNavigationDefaults.Elevation,
    content: @Composable RowScope.() -> Unit
)
```

`BottomNavigation`是一个简单的可组合函数，它的每个参数都是不言自明的。话虽如此，我们还是从内容部分开始吧。

# 定义路线

在`BottomNavigation` composable 的内容部分，开发人员应该定义要显示多少选项卡，哪个选项卡应该处于选中状态，以及与选中和未选中项目相关的 UI 内容。

让我们一步一步来。我们需要做的第一件事是创建一个密封的类来定义和管理底部栏项目的数量。看看那个密封的类:

底部条形项目的密封等级

`route`用作主键来区分应该向用户显示哪个可组合组件，而`title`和`icon`用于底部栏的 UI 目的。

# 创建组件

当用户选择一个特定的底部栏项目时，我们需要显示相应的视图。在传统的开发中，我们可能是通过片段来完成的。

但是使用 Compose，一切都是可组合的函数。因此，我们需要创建四个不同的可组合函数，每个函数都有一个显示视图名称的简单文本视图。看一看:

主页、搜索、收藏夹和个人资料屏幕的可组合功能

# 配置底部导航栏

现在我们已经定义了路由和各自的可组合组件，是时候配置底部导航栏了。首先，我们需要创建一个列表，所有的标签都显示在底部栏中。

```
val items = listOf(
    Screen.Home,
    Screen.Search,
    Screen.Favourites,
    Screen.Profile,
)
```

在`BottomNavigation`的内容部分，我们需要通过`map`对列表进行迭代，并对`BottomNavigationItem`进行膨胀。看一看:

配置底部导航视图

首先，我们在底部的横条上添加了`5.dp`，即仰角，以提高 UX。

*   `icon` —我们已经使用了`Icon`可组合函数来显示可绘制资源。
*   `label` —我们已经使用了`Text`可组合函数来显示选项卡的标题。
*   `selected` —这是选择模式下的选项卡。默认情况下，所有选项卡都是`false`。我们将在下一节处理导航部分时更新它。
*   `selectedContentColor` & `unselectedContentColor` —代表底部栏中选中和未选中项目的颜色。
*   `onClick` ——现在是`TODO()`，但我们将在下一部分更新。

# 航行

为了管理导航，我们需要`NavController`，它应该作为参数传递给可组合的`BottomBar`来保持状态。首先，我们需要从`NavController`获取当前视图来更新底部栏的选中状态。这可以按如下所示完成:

获取后台堆栈以识别当前路由

接下来我们需要在用户点击任何导航视图时更新`currentRoute`，如下所示:

```
navController.navigate(**it**.route)
```

现在，当我们把所有的事情放在一起，一切都有意义了。看一看:

全功能底部条

## 绘制导航图

导航的最后一部分是定义应该相对于路线显示哪个视图。为此，我们需要创建一个名为`BottomBarMain`的函数，并将`NavHostController`作为参数传递。我们将使用这个`NavHostController`来定义导航。看一看:

导航映射

# 设计主视图

现在我们已经完成了导航和底部栏，是时候设计主视图了，在这里我们将所有的组件放在一起。这里我们将使用`Scaffold`来显示`bottomBar`。看一看:

现在我们需要从我们的`MainActivity`中触发这个组件。看一看:

```
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MyApplicationTheme {
                MainView()
            }
        }
    }
}
```

仅此而已。您已经在 Jetpack Compose 中实现了一个全功能的底部栏。

# 在实时用例中派上用场的一些技巧

我们可以通过以下调整来配置导航图和底部行为。

## 简单的后堆栈

默认情况下，当用户单击 Back 按钮时，Compose 会遍历所有事务。如果您只想在第一次点击返回时将用户导航到主页，并在第二次点击返回时关闭应用程序，您可以通过在底部栏的`onClick`中添加以下代码来实现。

```
navController.navigate(**it**.route)**{
    navController.graph.startDestinationRoute?.*let* { route ->
        popUpTo(route) {
            saveState = true
        }
    }**
**}**
```

## 恢复叙述

这是另一个有用的标志，用于在重新选择之前选择的项目时恢复状态。

```
navController.navigate(**it**.route)**{
  **  restoreState = true
}
```

## 启动 SingleTop

使用此标志可以避免同一可组合/目标的多个实例。

```
navController.navigate(it.route){
    launchSingleTop = true
}
```

我们还有更多这样有用的函数，比如`popUpToId`、`anim`等等。

# 奖金

下面是带有底栏实现的 GitHub repo 您可以随意体验。

[](https://github.com/SG-K/Jetpack-Compose-Bttom-Navigation-Bar) [## SG-K/Jetpack-Compose-Bttom-导航栏

### 在 GitHub 上创建一个帐户，为 SG-K/Jetpack-Compose-Bt Tom-Navigation-Bar 的开发做出贡献。

github.com](https://github.com/SG-K/Jetpack-Compose-Bttom-Navigation-Bar) 

我最近看到了由 [Satya Pavan Kantamani](https://medium.com/u/d255c4fc9d76?source=post_page-----4c9f44d04e68--------------------------------) 发表的这篇关于 Android 的观点的优秀文章:

[](https://pavan-careers5208.medium.com/understand-how-view-renders-in-android-763f0adfb95c) [## 了解视图在 Android 中的呈现方式

### 渲染视图时的应用性能优化技巧

pavan-careers5208.medium.com](https://pavan-careers5208.medium.com/understand-how-view-renders-in-android-763f0adfb95c) 

目前就这些。希望你学到了有用的东西。感谢阅读。