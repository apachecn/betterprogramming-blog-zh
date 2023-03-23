# Android Jetpack Compose 中的反压处理

> 原文：<https://betterprogramming.pub/back-press-handling-in-android-jetpack-compose-42d9ed402d40>

## 创建一个处理函数来定制你的反压事件

![](img/7251ba6ad1e1675c34f0c428bf151051.png)

照片由[谢尔盖·佐尔金](https://unsplash.com/@szolkin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我们可以在 Android 和 iPhone 中发现的一个显著差异是它们如何处理系统背压。在 iOS 中，你可以从左向右滑动，而 Android 提供了不同的选项。无论用户选择使用哪个选项，开发人员都可以通过`onBackPressed`函数处理事件。

# 历史

回到 2015 年，当我们主要使用 android activities 来设计新屏幕时，处理系统 back press 非常容易，因为我们可以直接覆盖`onBackPressed`功能。

后来出现了片段 API。几年来，开发人员努力在片段中处理反压，因为反压事件是活动的一部分，而不是片段。过了一段时间，Android 团队引入了`onBackPressedDispatcher`,这使得片段可以添加一个回调函数来接收回压事件。

# 先决条件

在进一步学习之前，您必须具备如何使用 Jetpack Compose 的基本知识。如果你是作曲新手，我强烈推荐阅读以下文章:

*   [Jetpack 组合组件(第 1 部分)](/jetpack-compose-components-part-1-a80717dbfb59)
*   [探索 Jetpack Compose 中的 Android 底部表单](/explore-android-bottom-sheets-in-jetpack-compose-50eff0257231)

# Jetpack 撰写

Jetpack Compose 在现代 android 开发中起着关键作用。Jetpack compose 是稳定的，人们开始用 compose 构建生产就绪的应用程序。所以是时候学习如何在可组合函数中处理系统回压了。

现成的系统 back press in compose 基于导航控制器运行。因此，当用户触发 back press 事件时，默认情况下，compose 会在内部导航到导航视图中的上一个可组合函数。要了解更多有关撰写导航的信息，请阅读本文。

当您想要在用户回压时执行自定义操作，或者当您想要关闭 compose 中的底部表单时，就会出现问题。由于导航库不支持 Jetpack compose 底部表单，系统 back press 会关闭当前屏幕，而不是底部表单。

为了处理这些实时用例，compose 提供了`BackHandler`函数。首先，让我们来看看函数签名:

```
@Composable
public fun BackHandler(enabled: Boolean = true, onBack: () -> Unit)
```

正如你所看到的，这个函数有两个参数:

*   `enabled` —确定是否应启用此`BackHandler`来处理系统回压事件。
*   `onBack` — Kotlin 单元函数，可以传递一段代码在系统回压时执行。

让我们来看看函数的用法:

这非常简单，但是深入挖掘，您会发现上述实现的一个根本问题。Jetpack 的 compose 主要基于根据状态构建 UI 的理论。通过向 enabled 参数传递一个静态值，您将无限地消耗 back press 事件。

我们应该传递一个状态，而不是传递一个静态值。例如，您正在处理构成底部纸张的印刷。这里我们需要使用的理想状态是`ModalBottomSheetState`(基于您正在使用的底板类型)。看一下代码:

此处`BackHandler`的启用是基于底层纸的状态，这意味着只有当底层纸的状态可见(显示给用户)时，它才会接收回压事件。

如果在多个可组合组件中使用`BackHandler`,最里面的可组合组件接收回调，如果未注册，则回调在阶梯中向上移动。

目前就这些。希望你学到了有用的东西。感谢阅读。