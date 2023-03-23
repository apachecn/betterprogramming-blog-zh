# Android 中正确的片段通信

> 原文：<https://betterprogramming.pub/proper-fragment-communication-in-android-489fcac520b0>

## 不要让系统挂在你的片段的监听器上

![](img/fea8056c416a4b6a4f87f948780332b3.png)

照片由 I [srael Palacio](https://unsplash.com/@othentikisra?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄。

你可能已经阅读了大量关于这个主题的文章。想知道我为什么要多写一个是合理的。

我说的是*正确的方式*，事实上，有几种解决方案。Android 生态系统从一开始就在发展，你可能希望利用[视图模型](https://developer.android.com/topic/libraries/architecture/viewmodel?gclid=CjwKCAjwv4_1BRAhEiwAtMDLsgUPYGAEh1HAFlebOllUHvz0dEjmr2WnQlCQi4uA1r4FY5vx5vjtqBoCKSYQAvD_BwE&gclsrc=aw.ds)甚至第三方库，如 [RxJava](https://github.com/ReactiveX/RxJava) 或 [EventBus](https://github.com/greenrobot/EventBus) 。

如果您有兴趣了解更多关于视图模型的知识，我推荐您阅读这篇优秀的文章:

[](https://android.jlelse.eu/communicate-between-fragments-and-activity-using-livedata-631526d6357a) [## 使用 LiveData 在片段和活动之间进行通信

### 本文分为两部分——一、片段间的交流二。活动与…之间的通信

android.jlelse.eu](https://android.jlelse.eu/communicate-between-fragments-and-activity-using-livedata-631526d6357a) 

尽管上述所有优点都存在，但你可能处于无法利用它们的情况。然后，您又回到了良好的老式界面解决方案的起点。

也就是说，使用接口工作非常完美，并且仍然是可能的最轻的方法，因为它不需要任何第三方库。

然而，开发人员经常无法正确配置它们。

# 片段到活动的通信

让我们假设您有一个嵌入片段的活动。当片段上发生特定事件时，您希望通知您的活动，以便它对该事件做出反应。

下面是一个基本示例，其中有一个片段声明了一个接口，用于将事件传播给实现它的任何人:

公开接口的片段。

以及实现接口的活动:

将实现接口的活动。

为了解决契约，活动必须以侦听器的形式向片段声明自己。

问题是:我们在哪里调用这个方法？例如，您可以选择在`onCreate`方法中调用它。

如果碎片被破坏了呢？活动还在监听片段的事件吗？

答案是否定的，如果系统，出于任何原因，破坏了片段而不是活动。您的活动将创建另一个片段实例，但不会将侦听器重新附加到它。片段中的任何事件都不会传播到您的活动中，使其处于损坏状态。

为了正确处理所有情况，当您的活动被通知您的片段已被附加时，您必须绑定您的侦听器:

片段中正确附加的监听器。

使用这种配置，您可以将活动与片段松散地耦合在一起。

# 嵌套片段通信

如果您碰巧有一个片段托管另一个片段，您可以使用相同的机制在这两个片段之间进行通信。片段还提供了一个`onAttachFragment`回调函数，在您的子片段被附加时通知您。

# 请使用正确的附加回调

最近在 [API 24](https://developer.android.com/reference/android/app/Fragment#onAttachFragment(android.app.Fragment)) 中添加了`onAttachFragment`回调，然后用最新的 [androidX 支持包](https://androidx.de/androidx/fragment/app/Fragment.html#onAttachFragment(androidx.fragment.app.Fragment))进行了后移植。我仍然会在定义接口的片段中遇到前面的`onAttach`方法的用法。

Fragment 强制其调用方实现其接口。

虽然这两种解决方案都有效，但我推荐使用最新的，因为它防止片段知道哪个组件实现了它的接口。片段不应该负责附加它们自己的侦听器。

此外，通过这样做，您可以强制片段的调用者实现其接口——即使调用者不需要对片段事件做出反应。简直是糟糕的建筑。

无论如何，Android 团队更新了官方文档[以示支持。](https://developer.android.com/training/basics/fragments/communicating)

# 关于碎片化传播的思考

我有机会尝试上述所有解决方案。到目前为止，我一直忠于接口，因为它们提供了一致的机制——无论我需要在片段之间还是在片段和活动之间进行通信。

无论您如何进行，只要您理解一个片段的生命周期是如何工作的，您就不会有问题。