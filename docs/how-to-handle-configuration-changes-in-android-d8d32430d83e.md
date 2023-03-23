# 如何处理 Android 中的配置更改

> 原文：<https://betterprogramming.pub/how-to-handle-configuration-changes-in-android-d8d32430d83e>

## screenOrientation、onSaveInstanceState、setRetainInstance 和 ViewModel

![](img/9058a20fa76cfc660c4e60f4fe7e7031.png)

照片由[卢肯·萨贝拉诺](https://unsplash.com/@luferlex?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

在本文中，您将学习如何处理配置更改，如 Android 应用程序中的屏幕方向。您将了解五种最常用的解决方案。根据您的需求选择您需要的产品。

# 介绍

在 Android 应用中，用户每次旋转屏幕，键盘可用性等。这就是所谓的配置变更。配置更改基于用户操作在运行时发生。

当这种变化发生时，像`Activities`这样的 Android 组件会被重新创建或重启。Android 应用程序中的这种行为是为了适应新的配置变化而实现的。正确处理重启只不过是处理配置更改。

# 屏幕方向

有些情况下，我们不需要应用程序的某些`Activities`来采用配置更改，这意味着我们将`Activity`限制为固定方向(例如，仅使用横向方向播放视频)。

这是 Android 中处理配置更改最简单的任务。这里，我们可以使用清单文件中的`screenOrientation` 标志，并指定所需的方向类型。开箱即用，Android 框架提供了一堆选项，如`landscape`、`portrait`、`reverseLandscape`等等。

```
<activity android:name=".screens.HomeActivity"
    **android:screenOrientation="portrait"**
    android:theme="@style/AppThemeHomeScren"/>
```

要了解更多关于方向类型的信息，请阅读[文档](https://developer.android.com/guide/topics/manifest/activity-element)中的`screenOrientation`部分。

# onSaveInstanceState 和 *onRestoreInstanceState*

如果您的`Activity`需要重启以适应配置更改，该怎么办？这是我们面临真正挑战的地方。当一个`Activity`被重新创建时，我们在其中维护的所有状态都将消失。

这就是`onSaveInstanceState`和`onRestoreInstanceState`发挥作用的地方。基于 Android 版本，`onSaveInstanceState`在`onStop` 方法之前或之后被调用。在这种方法中，我们可以将重要数据以 bundle 的形式保存。看一下函数语法:

如果有东西保存在`onSaveInstanceState`方法中，那么`onRestoreInstanceState`在`onStart`方法之后被调用。从`onRestoreInstanceState`函数中，我们可以通过 bundle 接收保存的数据:

这就是我们如何使用`onSaveInstanceState`和`onRestoreInstanceState` 函数来保存配置变更中的重要数据。当我们使用这个包时，我们也可以使用`serialization`或`Parcelable`传递自定义对象。

# setRetainInstance

使用`onSaveInstanceState`和`onRestoreInstanceState` 的方法在数据有限的情况下非常有效。但是不建议保存像网络响应这样的大数据集。

在现代 Android 开发中，我们使用`Fragments`比`Activities`更频繁。因此，我们需要一种可以在`Fragments`中保存状态或数据的方法。这就是`setRetainInstance` 的用武之地。

以下是今天 Android 面试中一些最重要的问题:

*   为什么我们需要使用`setRetainInstance`？
*   如何阻止`Fragments`重新创建配置变更？

通常，当父`Activity`被重新创建时，嵌套的`Fragments`也被重新创建。如果我们将`setRetainInstance` 设置为`true`，即使父`Activity`在配置更改时被重新创建，嵌套的`Fragments`却没有。

这将保存保存在`Fragments`中的状态和数据。由于`Fragments`没有重新创建，我们可以保存`DataObjects`并在`Activity`重新启动时使用`FragmentManager`取回`Fragment`。在我看来，这种方法是保存大型数据对象的最佳解决方案。

# 视图模型

使用`Fragments`保存数据对象解决了配置更改时管理大型数据集的问题。但是随着`ViewModels`的引入，它在 API 28 中被弃用了。

`ViewModels`专门设计用于跨配置变更和在不同 UI 组件之间持久化和共享数据。因此，我们在`ViewModel`中管理数据对象，而不是在`Fragments`中。

将数据保存在`ViewModel`中使其更容易被所有组件访问。我们还可以维护数据的单一真实来源。这里，是`ViewModel`。

要了解更多关于`ViewModels`及其最佳实践的信息，我强烈推荐阅读以下文章:

[](/best-practices-for-using-viewmodels-in-android-e986b25dc78f) [## 在 Android 中使用视图模型的最佳实践

### 如何正确设置和使用 Java 和 Kotlin 视图模型

better 编程. pub](/best-practices-for-using-viewmodels-in-android-e986b25dc78f) 

# 奖金

要了解有关 Android 高级开发的更多信息，请阅读以下文章:

*   [《Android 开发中的 8 个常见错误》](https://medium.com/better-programming/8-common-mistakes-in-android-development-2edcf5179ec0)
*   [“了解如何在 Android 中创建矢量资产”](https://levelup.gitconnected.com/vector-asserts-in-android-f0774ba8bf92)
*   [“如何将 Google Pay 集成到您现有的 Android 应用中”](https://medium.com/better-programming/how-to-integrate-google-pay-into-your-existing-android-app-d75b269cd623)
*   [【安卓产品口味】](https://medium.com/@sgkantamani/android-product-flavors-eb526e35f9f1)
*   [“在您的 Android 应用中实现广告”](https://medium.com/android-dev-hacks/implementing-ads-in-your-android-application-28ac676024aa)

目前就这些。希望你学到了有用的东西。感谢阅读。