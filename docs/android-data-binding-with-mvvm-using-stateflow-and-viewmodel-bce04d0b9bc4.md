# 与 MVVM 的 Android 数据绑定:使用 StateFlow 和 ViewModel

> 原文：<https://betterprogramming.pub/android-data-binding-with-mvvm-using-stateflow-and-viewmodel-bce04d0b9bc4>

## 使用数据绑定构建基于 MVVM 架构的应用程序指南

![](img/d14e77f39d0d8ea211b7c8955d0a7ffc.png)

美国宇航局在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

这是一个系列的第三部分，在这个系列中，您将学习数据绑定的基础知识，以及如何将数据绑定用于像`LiveData`和`ViewModel`这样的架构组件。今天，您将学习如何使用 Kotlin 协同程序和`StateFlow`通过来自`ViewModel`的数据绑定来更新 UI。

*   [“Android 与 MVVM 架构的数据绑定(上)”](/android-2-way-data-binding-with-mvvm-c13022a2f04a) : *基础知识>双向绑定>代码优化*
*   [“Android 与 MVVM 架构的数据绑定(下)”:*与 LiveData 和 ViewModel 的数据绑定*](/android-data-binding-with-mvvm-using-livedata-and-viewmodel-71028901e3bb)
*   “Android 数据绑定与 MVVM 架构(第三部分)”:你在这里。

# 介绍

## 状态流

[协程](https://kotlinlang.org/docs/reference/coroutines-overview.html)中的[流 API](https://kotlinlang.org/docs/reference/coroutines/flow.html) 是处理数据流的更好方式。`StateFlow`试图在你的 Android 应用中引入强大的 Flow 特性来管理状态。

正如 GitHub 上的[所解释的那样，`StateFlow`有两种变体:`StateFlow`和`MutabaleStateFlow`。](https://github.com/Kotlin/kotlinx.coroutines/issues/1973)

> “`StateFlow<T>`接口是一个只读视图，它提供对当前`value`的访问，并实现一个`Flow<T>`来收集对值的更新。
> 
> `MutabaleStateFlow<T>`接口增加了修改值的操作。"

如果你是第一次接触`StateFlow`，我强烈建议你在进一步了解之前阅读以下文章:

[](/introducing-coroutines-stateflow-ca1b13133265) [## 引入协程 StateFlow

### 这是 Android 版 LiveData 的终结吗？

better 编程. pub](/introducing-coroutines-stateflow-ca1b13133265) 

## 数据绑定

数据绑定是一个框架，它通过减少开发人员需要编写的样板代码来充当布局和数据源之间的桥梁。这将提高开发人员的生产力，让他们有时间专注于重要的事情。

如果您是数据绑定框架的新手，我建议您阅读下面的文章和本系列的第一部分。今天的部分是关于如何在架构中使用数据绑定。要理解这一点，你应该知道一些基本知识。

[](https://sgkantamani.medium.com/data-binding-in-android-4ff7bba93a2c) [## Android 中的数据绑定

### 不再需要编程来更新 UI 并将事件链接到视图

sgkantamani.medium.com](https://sgkantamani.medium.com/data-binding-in-android-4ff7bba93a2c) 

# 数据绑定中的状态流支持

如果您阅读了本系列的第二部分，我们使用了`LiveData`作为数据源来更新布局中的 UI。使用`LiveData`使数据绑定具有生命周期意识，并且只有当屏幕在前台时才更新 UI。

本文的目标是用`StateFlow`代替`LiveData`的用法。Kotlin 团队最近宣布，我们现在可以使用`StateFlow`作为数据绑定的数据源。要做到这一点，你应该安装 Android Studio 北极狐金丝雀 4 (2020.3.1.4)，现在金丝雀和 Dev 频道都有。

在继续之前，让我们检查一下缺点:

1.  与`LiveData`不同，`StateFlow`是新的。
2.  `LiveData`是 Jetpack 库的一部分，所以它可以在 Java 代码中使用。`StateFlow`是协程的一部分，只能在 Kotlin 中使用。

除此之外，我看不出使用`StateFlow`有任何潜在的缺点。但是我应该说`StateFlow`将改变我们在架构中管理状态的方式，因为我们倾向于非常深入地使用协程。

话虽如此，我们还是开始吧。

# 履行

让我们继续本系列前几部分中使用的例子。在第 1 部分中，我们直接导入了`Activity`中的`UserModel`作为绑定的数据源。在第二部分中，我们创建了一个`ViewModel`并从`ViewModel`实例中访问了由`LiveData`包装的`UserModel`。

## 快速回顾

数据类别:

```
data class UserModel (var name: **String** ="" ,
                 var email: **String** = "")
```

`ViewModel`同`LiveData`:

```
class UserViewModel : ViewModel() {

    var userModel  = **MutableLiveData<UserModel>()**

}
```

最后，使用`LiveData`作为数据源来在 XML 布局中进行数据绑定:

## 用 StateFlow 替换 LiveData

很像`LiveData`，`StateFlow`完全支持开箱即用的数据绑定。就我个人而言，我认为这是 Kotlin 在 Android 开发中的影响力的一种进步。数据绑定是来自 Android 家族的 Jetpack 库，而`StateFlow`来自 Kotlin coroutines。看到两个来自不同环境的不同实体走到一起真是太棒了。

我希望`StateFlow`对数据绑定的支持不会像最近被弃用的 Kotlin synthetics 那样。

感谢 Kotlin 团队，我们只需要将`ViewModel`中的`LiveData`更新为`StateFlow`对象。其余的将由数据绑定和引擎盖下的`StateFlow`处理。

首先，我们需要创建一个带有初始值的私有可变`StateFlow`实例。然后，我们将它分配给另一个`StateFlow`实例，我们在 XML 布局中使用它来更新 UI:

仅此而已。我们不需要对视图或 XML 布局做任何改变。内部发生的事情是，它将通过替换`LiveData`在我们的 Android 组件中使用具有给定上下文的`StateFlow`。最重要的事情之一是要求生命周期所有者在使用`LiveData`或`StateFlow`时绑定对象:

现在你有了一个使用`ViewModel`、`StateFlow`和数据绑定的应用程序。

在本系列的第一部分中，我们学习了如何用可观察字段更新 UI。在第二部分中，我们删除了所有带有`LiveData`和数据绑定的样板文件。今天，我们使用一个 Kotlin 核心 API 来替换`LiveData`,以便用协程改进代码。

# 奖金

要了解有关 Android 高级开发的更多信息，请阅读以下文章:

*   [“Android recycler view With kot Lin Sealed Classes”](https://medium.com/better-programming/android-recyclerview-with-kotlin-sealed-classes-6d2985aac3e5)
*   [“使用 Android 构建变体管理服务器环境”](https://medium.com/better-programming/use-android-build-variants-to-manage-server-environments-146125ae31d2)
*   [“使用 BuildSrc 和 Kotlin DSL 进行梯度依赖管理”](https://medium.com/better-programming/gradle-dependency-management-with-buildsrc-and-kotlin-dsl-1de958eab166)
*   [《Android 中视图链接的演变》](https://medium.com/better-programming/the-evolution-of-view-linking-in-android-d6219678740d)

目前就这些。希望你学到了有用的东西。感谢阅读。