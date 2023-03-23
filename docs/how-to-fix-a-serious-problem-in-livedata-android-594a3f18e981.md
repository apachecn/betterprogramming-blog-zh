# Android:如何修复 LiveData 中的一个严重问题

> 原文：<https://betterprogramming.pub/how-to-fix-a-serious-problem-in-livedata-android-594a3f18e981>

## 每当组件生命周期改变时，避免观察相同的数据

![](img/68f6322f1fa2fea780c337e807ad4814.png)

由[阿诺·弗朗西斯卡](https://unsplash.com/@clark_fransa?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 介绍

`LiveData`出自`JetPack`家族。它主要用于将数据从视图模型交付给最新架构中的片段和活动，如`MVVM`和`clean`。

`LiveData`是一个可观察的数据持有者类。与其他数据保存器不同，`LiveData`是一个生命周期，能够识别各个组件，如`Activities`、`Fragments`、`services`等等。由于它是生命周期感知的，`LiveData`确保它只将数据发布给那些在活动时观察它的人，这意味着当他们处于前台时。

# 问题

如果目标组件*在前台，则`LiveData`将数据发布到目标组件*。*如果不是，它可能会保存数据，并在特定组件返回到前台时传递数据，就像在`onResume` 状态中一样。*

现在我们知道了它是如何工作的，假设我们有一个`livedata`，它有多个观察者和三个片段——F1、F2 和 F3——它们在堆栈中。所有三个片段共享一个`ViewModel`并观察一个 T 类型的`liveData`来显示一条消息。

现在，当我们通过`liveData`发布类型 T 的数据时，因为片段 F3 在前台，所以消息首先显示在 F3 中。这很好——但是当我们点击返回时会发生什么呢？片段 F2 被恢复，因为 F2 是其订户之一。`LiveData`再次传递数据，因此消息显示两次，片段 F1 重复相同的内容。

这就是`livedata`的工作方式，但是，在实时情况下，可能会出现一旦观察到数据，就不应该再观察的情况。以下部分包含解决方案。

# 解决办法

## 第一步

首先，我们需要创建一个名为`Event`的类来包装`livedata`公开的数据。看看如何创建一个`Event`类:

在这个类中，我们维护一个标志`hasBeenHandled` 来检查数据是否至少被观察了一次。

## **第二步**

现在我们需要创建另一个名为`EventObserver` 的类来扩展`Observer`。只是为了让使用过程简单一点，看看:

当发布新数据时，`onChanged`功能被触发，标志`hasBeenHandled` 被设置为假。一旦第一个订户观察到数据，标志`hasBeenHandled` 将为真，使得其余订户不会接收到数据，并且每次发布新数据时循环重复。

## 第三步

在观察`livedata`时，我们需要使用`EventObserver` 而不是常规的`Observer` ，以便简化操作。看一看:

这是我从 2020 AndroidDev 峰会 app 源代码中找到的解决方案。

感谢您的阅读。