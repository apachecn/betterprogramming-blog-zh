# 关于 Jetpack Compose 的 4 个酷事实

> 原文：<https://betterprogramming.pub/4-cool-facts-about-jetpack-compose-d9e6c795f9a1>

## 在 Android 中构建用户界面变得越来越容易和快速

![](img/2ed3a26782ed9c8585f5779d25df6c95.png)

[Andrew M](https://unsplash.com/@andymant?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

Jetpack Compose 是谷歌最新的 UI 框架，用于构建 Android(和[桌面](https://www.jetbrains.com/lp/compose/))应用。它极大地改变了构建 UI 组件的方法。它是完全声明性的，意味着您通过调用一系列将数据转换成 UI 层次结构的函数来描述您的 UI。它是不捆绑的，这意味着它向后兼容旧版本的 Android，可以独立于 Android 操作系统进行更新。

但最重要的是，它使得编写 UI 代码变得非常容易。你会问，有多容易？就像下面的代码一样简单:

这个简短的函数在 UI 中创建一个列表，就像您使用 RecyclerView 构建的一样。

并且你可以在这个`LazyColumn`中构建任何 UI，想象一下自己通过在布局中添加一个适配器、`ViewHolder`、`DiffUtil`类和一个`RecyclerView`来开发它。

在这篇文章中，我想分享我在学习作曲时遇到的最有趣的事实。

# 1.Compose 可与 Android 视图互操作

可以在 Android 视图中使用 Compose，在 Compose 中使用 Android 视图。

这很好，因为 Compose 将与您现有的 UI 实现一起工作。如果你想把它添加到你的项目中，你不必一步到位，一气呵成。你可以慢慢开始，介绍一些基础的东西，比如主题、样式，然后逐渐将 Compose 集成到你的代码库中。欲了解更多详情，请查看此[页面](https://developer.android.com/jetpack/compose/interop)。

# 2.可组合的函数可能在后台线程池中执行

与 Android 视图不同，Compose 可以在后台线程中运行您的可组合函数(您可以在其中声明您的 UI )!

它可以通过并行运行可组合函数来优化重组(更新 UI 中发生变化的部分)。这使得 Compose 可以利用多个内核，并以较低的优先级运行不在屏幕上的可组合函数。

# 3.所有可组合的函数和 lambdas 必须没有副作用

可组合函数必须具有以下属性:

*   当用同一个参数多次调用该函数时，它的行为是一样的，并且它不使用其他值，比如全局变量或对`random()`的调用。
*   函数描述了没有任何副作用的 UI，例如修改属性或全局变量。

在 Compose 使用的声明式编程范式中，当您想要更新 UI 时，需要用新数据再次调用 composable 函数。这样做会导致 UI 被重新组合——如果需要的话，由函数发出的小部件会用新数据重新绘制。

重新构建整个 UI 树的计算开销会很大。Compose 用这种智能重组解决了这个问题。重组是当输入改变时再次调用可组合函数的过程。当 Compose 基于新的输入重新组合时，它只调用可能已经改变的函数或 lambdas，而跳过其余的。通过跳过所有没有改变参数的函数，Compose 可以高效地重新组合。

当需要执行副作用时，从回调(如`onClick`)中触发它，而不是从可组合函数中访问局部或全局变量。这些回调总是在 UI 线程上执行。[ [来源](https://developer.android.com/jetpack/compose/mental-model#recomposition)

如果你必须在一个组合函数中保持一个状态，你应该使用`mutableStateOf`和`remember`来避免每次重组都有不同的状态。这就把我们带到了下一个话题。

# 4.撰写没有内部状态

在 Compose 中，状态管理掌握在开发人员手中。默认情况下，Compose 中没有内部状态。这将消除应用程序和合成组件之间的同步问题。

例如，与 Android 视图不同，当你点击复选框或切换按钮(在 Compose 中称为切换)时，它不会在 Compose 中默认自动切换。您需要编写“点击开关改变开关状态”逻辑的代码。

例如，考虑以下可组合函数:

![](img/788f651ce56c7b2b38de81a84734cc6f.png)

作者图片

这不会使复选框自动可勾选。因为我们还需要实现`onCheckedChange` 回调，实现“点击复选框使其被选中”的逻辑。

你可能认为我们可以像这样在可组合函数中添加一个 Kotlin 变量:

但是，这不是好的做法，也不会起作用。因为 Compose 不知道这个状态，所以这个值将在下一次重新组合时被丢弃。我们需要在 Compose 中注册这个状态，以便它可以在进行重组时考虑它。

您应该使用`remember`函数，而不是科特林变量:

![](img/c4025f3d7673c7371accec236a5272b0.png)

现在用`remember` Compose 了解我们的状态。这样，我们可以创建一个典型的行为，您可以检查并看到它处于选中状态。

还有其他存储状态的方法。因为您可能并不总是希望将它存储在您的可组合函数中。还可以将必要的信息和回调函数作为可组合函数的参数。

这允许父节点控制子节点的状态，称为状态提升*。*在构建这些函数时，您应该考虑父节点是否可能对状态感兴趣，如果是，则考虑使用这种方法。[ [来源](https://developer.android.com/jetpack/compose/state)

# Compose 现在可以投入生产了

作曲最近达到稳定。这是一个开始学习并为你的项目考虑它的好时机。[立即开始使用 Jetpack Compose！](https://developer.android.com/jetpack/compose)

感谢您今天的参与，下次再见。