# 引入协程 StateFlow

> 原文：<https://betterprogramming.pub/introducing-coroutines-stateflow-ca1b13133265>

## 这是 Android 版 LiveData 的终结吗？

![](img/1bc59a15e8eba5191edfd1d6175f2890.png)

[Ravi Pinisetti](https://unsplash.com/@ravipinisetti?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 这篇文章的要点

在本文中，您将了解什么是 [StateFlow](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/) ，如何使用它，使用它的优势，以及一些实时问题的解释和解决方案。通过将其与`[LiveData](https://developer.android.com/topic/libraries/architecture/livedata)`进行比较，我将发表我对使用 StateFlow 胜过使用`LiveData`的想法。

# 介绍

[协程](https://kotlinlang.org/docs/reference/coroutines-overview.html)中的[流 API](https://kotlinlang.org/docs/reference/coroutines/flow.html) 是处理数据流的更好方式；StateFlow 试图利用 Flow 的强大功能来管理应用程序中的状态。如果你看到 StateFlow 的语法，它是由 [SharedFlow](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-shared-flow/index.html) 扩展的。看一看:

```
@ExperimentalCoroutinesApi interface **StateFlow**<out T> : 
    [**SharedFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-shared-flow/index.html)<[T](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/index.html#T)> [(source)](https://github.com/kotlin/kotlinx.coroutines/tree/master/kotlinx-coroutines-core/common/src/flow/StateFlow.kt#L139)
```

SharedFlow 是具有单个可更新数据值的只读状态，它向其收集器发出更新。与常规流不同，状态流是一个*热*流，因为它的活动实例独立于收集器的存在而存在。StateFlow 的当前值可以通过 value 属性检索。

StateFlow 有两种变体— `StateFlow`和`MutabaleStateFlow`。

*   `StateFlow<T>`接口是一个只读视图，它提供对当前`value`的访问，并实现一个`Flow<T>`来收集值的更新。
*   `MutabaleStateFlow<T>`接口增加了修改值操作。

# 如何使用 StateFlow

首先，让我们创建一个`enum`类，并在视图中包含一些由`viewModel`触发时需要管理的状态。看一下`enum`类:

```
enum class **CustomStates**{
    SHOW_LOADING, HIDE_lOADING, SHOW_TOAST, HOMESCREEN, BACK
}
```

现在我们需要创建`CustomStates` 类型的`MutableStateFlow`和`StateFlow`变体。看一看:

这里的`MutableStateFlow`是只能从`viewModel`更新的私有含义。`StateFlow`是公共的，意味着可以从视图中访问它来观察`MutableStateFlow`中的变化。

现在我们需要创建一个函数来更新`MutableStateFlow`的值。看一看:

我忘记提到 StateFlow 是一个实验性的特性，所以我们需要提到`ExperimentalCoroutinesApi`来显式地禁用警告。

我们已经完成了状态流的创建和更新；下一步是收集视图中发出的值。这类似于从正常流中收集值。看一看:

```
viewModel.customState.**collect** {
    "Latest state is  $it".print()
}
```

这就是全部——我们已经使用协程中新的 StateFlow 完成了对来自`viewModel`的 UI 状态的管理。

# 隐藏的问题和解决方案

最初实现 StateFlow 时，我们似乎没有注意到两个问题:

## 问题

*   我们不能将 StateFlow 限制到一组特定的订阅者或视图，因此即使在视图被销毁后，它也可以收集值。
*   StateFlow 是热的，这意味着它是活动的，与收集器无关。这是个问题。当您重新创建使用 StateFlow 的片段时，将发出最后一个值，而不是初始值。
*   `LiveData`仅在`LifecycleOwner`处于激活状态时发出。如果生命周期状态比开始状态“低”,它将暂停消耗。这与 StateFlow 中的行为不同。

## 解决方法

*   将状态流限制为片段或活动是不可能的。我们仍然可以用那个视图的生命周期范围来包装它，所以一旦这个片段被销毁，我们不会收到任何更新，因为这个范围会取消所有的作业。

*   第二个问题的解决方案是在重新创建视图时接收初始值，而不是最后一个值。我们可以通过在`viewModel`的`onCleared`函数中发布 StateFlow 的初始值来实现这一点。看一看:

*   为了让 StateFlow 生命周期意识到复制这种`LiveData`行为，我们需要使用`launchWhenStarted`。看一看:

# StateFlow 与 LiveData

`LiveData`是一个可观察的数据持有者类——意味着`LiveData`可以保存一组可以被其收集器观察到的数据，这类似于 StateFlow。我们在这里所做的一切也可以通过`LiveData`来实现。那有什么区别呢？

各有千秋，比如`LiveData`目前比 StateFlow 更成熟。但是有了 StateFlow，我们可以使用强大的功能，如 combine、zip 等。高级功能也有一些缺点，比如当与之相关的视图被销毁时，需要手动分配初始状态。

我不认为 StateFlow 可以取代`LiveData`，至少不是马上。StateFlow 的核心理念(状态不能被破坏，即重新初始化时发出最后一个状态)与我们在大多数情况下在 Android 组件中使用`LiveData`的主要原因完全相反。

但是，在一些用例中，我们需要在整个应用程序会话中管理状态，我认为这是 StateFlow 可以帮助我们实现的地方。

这是我对 Android 中 StateFlow 的初步看法。如果您对 StateFlow 有任何建议或不同的观点，请发表评论，以便我们共同学习。

就这些了，希望你能学到一些有用的东西，感谢阅读。