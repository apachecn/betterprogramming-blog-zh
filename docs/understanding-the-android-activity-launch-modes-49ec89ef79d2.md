# 了解 Android 活动启动模式

> 原文：<https://betterprogramming.pub/understanding-the-android-activity-launch-modes-49ec89ef79d2>

## 标准、单实例、单任务和单实例

![](img/40e7e5d3034e0b4a94e0cf1bd1c85f2a.png)

照片由[this engineering RAEng](https://unsplash.com/@thisisengineering?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

在这篇文章中，我们将学习什么是启动模式，以及 Android 中使用了多少种启动模式。将包括每个启动模式如何工作的详细解释。

# 介绍

自从 Android 开发以来，没有改变的一个标准就是启动模式。Android 中的启动模式帮助开发人员启动`Activities`,其中包含一组关于它应该如何与任务相关联的指令。

在 Android 应用程序中实现导航时，你会面临不同的需求，比如如果用户进入一个特定的`Activity`就销毁一组 backstack `Activities`，或者总是创建一个新的`Activity`实例(反之亦然)。

通过使用正确的启动模式，您可以在一行代码中实现所需的导航行为。为此，您应该首先了解启动模式的类型以及它们是如何工作的。

# 任务和后台堆栈

在进一步学习之前，任务和后台堆栈是需要学习的两件重要的事情。

## 工作

任务是用户在执行某项工作时与之交互的`Activities`的集合。基于用于`Activities`的启动模式，一个 Android 应用可以有 *n* 个任务。任务中的第一个`Activity`实例是该任务的根。

## 背面堆栈

当用户与应用程序交互时，会启动几个`Activities`。这些`Activities`按照每个`Activity`发射的顺序保存在堆栈中。维护适当的后台堆栈是一项非常复杂的任务，如果您犯了任何错误，都可能会破坏用户体验。

要了解更多关于任务和后台堆栈的信息，请浏览 Android 的文档。

# 发射模式的类型

Android 中有四种启动模式:

1.  `Standard`
2.  `SingleTop`
3.  `SingleTask`
4.  `SingleInstance`

每种发射模式都有其重要性和独特的发射方式`Activities`来实现每一种可能的导航形式。在本文的其余部分，您将通过一个简单的示例详细了解每种启动模式。

让我们开始吧。

# 标准

这是 Android `Activities`的默认启动模式。每次在目标任务中，它都会创建一个`Activity`的新实例。

一个常见的用例是显示组件的细节。例如，考虑一个电影应用。每当用户点击一个电影项目，我们需要在`details` `Activity`中显示电影的细节。

```
<activity android:launchMode=”standard” />
```

假设我们有五个`Activities`。`A`、`B`、`C`、`D`、`E`均定义为`Standard`。

所以，我们的栈可以这样:`A-B-C-A-A-B-B`。

# 单身公寓

如果任务中已经存在一个`Activity`的实例，并且它位于任务的顶部，那么 Android OS 将把意图数据传递给`Activity`的`onNewIntent`函数，而不是创建一个新的实例。

如果任务中的`Activity`上没有实例，或者已经存在的任务实例不在任务顶部，那么将创建一个新的`Activity`实例。

```
<activity android:launchMode=”SingleTop” />
```

假设我们有三个`Activities` : `A`、**、**、`B`、**、**和`C`。`B` 定义为`singleTop`。

```
Step 1: Launch A -> **A**
Step 2: Launch B -> **A-B** 
Step 3: Launch C -> **A-B-C** 
Step 4: Launch B -> **A-B-C-B**
Step 5: Launch B -> **A-B-C-B***
```

*   步骤 2:创建一个新的`Activity` `B`实例，因为它不在任务中。
*   步骤 4:创建一个新的`Activity` `B`实例，因为现有的`B`实例不在任务的顶部。
*   步骤 5:意向数据被重新路由到`onNewIntent`函数，因为活动实例位于任务的顶部。

# 单一任务

如果任务没有现有的`Activity`实例，那么就会创建一个类似于`singleTop`的新实例。

如果任务有一个现有的`Activity`实例，并且它在`Activity`的顶部，那么系统将把意图数据传递给`onNewIntent`函数。

如果任务有一个已存在的实例，但不在顶层，那么它将回滚到那个`Activity`并销毁`SingleTask` `Activity`顶层的`Activities`。

```
<activity android:launchMode=”SingleTask” />
```

假设我们有三个`Activities` : `A`、`B`和`C`。`B` 定义为`singleTask`。

```
Step 1: Launch A -> **A**
Step 2: Launch B -> **A-B** 
Step 3: Launch C -> **A-B-C** 
Step 4: Launch B -> **A-B**
Step 5: Launch B -> **A-B***
```

*   步骤 2:创建一个新的`Activity` `B`实例，因为它不在任务中。
*   第四步:通过从任务中删除`Activity` `C`回滚到`Activity` `B`的现有实例，因为它在`Activity` `B`之上。
*   步骤 5:意图数据被重新路由到`onNewIntent`函数，因为活动实例位于任务的顶部。

# 单一实例

当您用`SingleInstance`调用`Activity`时，系统将创建一个新的特殊任务，其中只有一个`SingleInstance` `Activity`。如果您从`SingleInstance` `Activity`中触发任何默认`Activity`，它将重新路由到前一个任务并创建默认`Activity`的一个新实例。

如果已经创建了`Activity`的实例，那么系统将路由到任务并使用`onNewIntent`函数传递数据。

```
<activity android:launchMode=”SingleInstance” />
```

假设我们有三个`Activities` : `A`、`B`和`C`。`B` 定义为`SingleInstance`。

```
Step 1: Launch A -> **Task 1: A**Step 2: Launch B -> **Task 1: A
                   Task 2: B** // Visible to the userStep 3: Launch C -> **Task 1: A-C** // Visible to the user **Task 2: B**Step 4: Launch B -> **Task 1: A-C
                   Task 2: B*** // Visible to the user
```

*   步骤 2:在一个单独的任务中创建一个`Activity` `B`的新实例，因为它不在系统中。
*   第三步:在`Task 1`中创建一个`Activity` `C`的新实例，这是前面的任务。
*   第四步:`Task 2`出现，`Activity` `B`显示给用户。

# 结论

如你所见，每种发射模式都有自己的发射方式`Activities`。如果您需要多个`Activity`实例，那么在`Standard`和`SingleTop`之间进行选择。如果您在任何给定时间只想要一个`Activity`实例，那么在`SingleTask`和`SingleInstance`之间选择。

# 奖金

要了解有关 Android 高级开发的更多信息，请阅读以下文章:

*   [《Android 开发中的 8 个常见错误》](https://medium.com/better-programming/8-common-mistakes-in-android-development-2edcf5179ec0)
*   [“了解如何在 Android 中创建矢量资产”](https://levelup.gitconnected.com/vector-asserts-in-android-f0774ba8bf92)
*   [“如何将 Google Pay 集成到您现有的 Android 应用中”](https://medium.com/better-programming/how-to-integrate-google-pay-into-your-existing-android-app-d75b269cd623)
*   [“在您的 Android 应用中实现广告”](https://medium.com/android-dev-hacks/implementing-ads-in-your-android-application-28ac676024aa)

目前就这些。希望你学到了有用的东西。感谢阅读。