# 探索 Jetpack Compose 中的 Android 底部表单

> 原文：<https://betterprogramming.pub/explore-android-bottom-sheets-in-jetpack-compose-50eff0257231>

## *BottomSheetScaffold 和 ModalBottomSheetLayout*

![](img/b700ab61974f2be09f437f5605ad98c7.png)

由[布莱克·康纳利](https://unsplash.com/@blakeconnally?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 这篇文章的要点

在这篇文章中，我们将学习如何在 [Jetpack Compose](https://developer.android.com/jetpack/compose) 中使用底部表单。我们将涵盖持久底层和模态底层。在本文的最后，您会找到 GitHub 项目链接，因此可以随意探索代码。

# 介绍

Jetpack Compose 是谷歌最近试图让 Android 开发者轻松构建 UI 的尝试之一。

> *“Jetpack Compose 是一款用于构建原生 Android UI 的现代工具包。Jetpack Compose 以更少的代码、强大的工具和直观的 Kotlin APIs 简化并加速了 Android 上的 UI 开发。”—* [*安卓开发者*](https://developer.android.com/jetpack/compose/tutorial)

Jetpack Compose 最近进行了测试。这意味着 API 结构将不再有任何剧烈的变化，因此这将是学习如何使用这个下一代 UI 套件进行 Android 开发的大好时机。

您需要安装 Android Studio Canary，北极狐(2020.3.1) 版本才能使用 Jetpack Compose beta。

# 先决条件

在进一步学习之前，您必须具备如何使用 Jetpack Compose 的基本知识。如果你是作曲新手，我强烈推荐阅读以下文章:

*   "[喷气背包构成组件(第一部分)](/jetpack-compose-components-part-1-a80717dbfb59)"
*   "[在 JetPack Compose 中构建 Android 布局](/build-android-layouts-in-jetpack-compose-bda2e0837f5e)"
*   "[Android 版 Jetpack Compose 中的主题化](/theming-in-jetpack-compose-for-android-667d42b8279c)"
*   "[在 Android 中使用 Jetpack Compose 导航](/navigation-with-jetpack-compose-in-android-ab84d0984ade)"

# 底层是什么？

Android 中的底层是材质设计用户界面组件。基本上，它是一个从屏幕底部进入界面的视图，带有流畅的动画。使用底板有两个主要优点:

*   它们是可滑动的——这为用户创造了一个比点击按钮更具交互性的 UX 界面。
*   快速访问—由于它们锚定在底部，用户可以轻松访问内容。

# 底板的类型

## 永久底板

标题很明显:它一直在屏幕底部。用户可以上下滑动来展开和折叠工作表。我们可以调整峰值高度，即折叠底板的默认高度。

## 莫代尔底板

模态底层类似于对话框。唯一的区别是，模态底层具有平滑的动画，用户可以像滑动持久底层一样滑动它。与持久底部表单不同，这里用户不能访问底部表单范围之外的内容。

# 喷气背包中的底部纸张构成

开箱即用的 Jetpack Compose 支持持久化和模态底层表单。其他任何可拖动的底片也有自己的脚手架:`BottomSheetScaffold`和`ModalBottomSheetLayout`。

不要再拖延了，让我们开始吧

# `BottomSheetScaffold`

`BottomSheetScaffold`是一个简单的可组合函数，用于显示持久的底部表单。看看这个可组合的:

`BottomSheetScaffold`

不要被参数的数量搞得不知所措。因为它是一个脚手架，本质上将有两个视图需要管理，底层的表和它后面的内容。在这里，我将只解释我们构建持久底层所需的参数:

*   `sheetContent` —这是一个简单的可组合函数，表示表单的用户界面。
*   `scaffoldState` —用于管理底部表单的状态:展开、折叠、半展开。
*   `topBar` —这是一个简单的可组合函数，代表了屏幕顶栏的设计。
*   `content` —这是一个可组合的函数，用于设计底部工作表后面的屏幕内容。

## BottomSheetState

这是一个预定义的可组合函数，通过它我们可以管理底部工作表的状态。它有方便的暂停功能，如扩展和折叠，以执行流畅的动画。看一看:

组件已经足够了，是时候开始实现了。首先，我们设计底部工作表后面的视图。没有什么复杂的，只是一个简单的单一按钮的看法。单击按钮时，我们需要更新状态。为了更新状态，我们需要`BottomSheetScaffoldState`，我们将它和`CoroutineScope`一起作为参数。看一看:

持久底部表单的主屏幕视图

现在是设计底层的时候了。这里我们将使用`rememberBottomSheetScaffoldState`和`rememberCoroutineScope`可组合函数来管理整个重组的状态。

持久的底层实现

您可以将底部工作表内容的设计部分移出，以简化编码部分。

# ModalBottomSheetLayout

`ModalBottomSheetLayout`是一个简单的可组合函数，类似于`BottomSheetScaffold`，用于显示模态底部表单。看看这个可组合的:

像往常一样，我们只考虑构建模态底层所需的参数。

*   `sheetContent` —这是一个简单的可组合函数，表示表单的用户界面。
*   `scaffoldState` —用于管理底层表单的状态:展开、折叠、半展开。
*   `content` —这是一个可组合的函数，用于设计底部工作表后面的屏幕内容。

## ModalBottomSheetState

`ModalBottomSheetState`是一个 Kotlin 类，用于管理底层工作表的状态。它有方便的暂停功能，如展开、折叠、半展开、隐藏等，以执行流畅的动画。

首先，我们需要创建主屏幕视图，就像我们在上一节中所做的那样。唯一不同的是，我们将用`ModalBottomSheetState`替换`BottomSheetScaffoldState`参数。看一看:

莫代尔底板的主屏幕视图

现在是设计底层的时候了。这里我们将使用`rememberModalBottomSheetState`和`rememberCoroutineScope`可组合函数来管理整个重组的状态。

模态底层实现

仅此而已。我们已经完成了实现。

# 显示底部工作表

既然我们已经完成了核心实现，剩下的唯一事情就是从活动中调用`ModelBottomSheet`和`BottomSheetSample`组件。看一看:

触发底部工作表的主要活动

# 奖金

下面是 GitHub repo 链接，它实现了持久化和模式化的底层表单。随意探索。

[](https://github.com/SG-K/Jetpack-Compose-Bottom-Sheets) [## SG-K/Jetpack-撰写-底部-工作表

### 在 GitHub 上创建一个帐户，为 SG-K/Jetpack-Compose-Bottom-Sheets 的开发做出贡献。

github.com](https://github.com/SG-K/Jetpack-Compose-Bottom-Sheets) 

我最近看到了 Satya Pavan Kantamani 发表的这篇关于 Kotlin DSLs 的优秀文章。强烈建议:

[](https://proandroiddev.com/migrate-from-groovy-to-kotlin-dsl-951266f3c072) [## 从 Groovy 迁移到 Kotlin DSL

### 将您的 Android 构建脚本更改为 Kotlin DSL 以获得更好的灵活性

proandroiddev.com](https://proandroiddev.com/migrate-from-groovy-to-kotlin-dsl-951266f3c072) 

目前就这些。希望你学到了有用的东西。感谢阅读。