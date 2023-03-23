# 深入探究 Android Jetpack 库，WorkManager

> 原文：<https://betterprogramming.pub/a-deep-dive-into-the-android-jetpack-library-workmanager-5aee2f57e07b>

## 利用高级功能处理实时工作

![](img/c49fd692b2b45ef8d7c8aa68fb212a1a.png)

由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 目录

```
1\. [Overview](https://medium.com/p/5aee2f57e07b#10bb)
2\. [Integration](https://medium.com/p/5aee2f57e07b#8435)
3\. [Things to Know Before Using WorkManager](https://medium.com/p/5aee2f57e07b#785f)
4\. [Creating Workers](https://medium.com/p/5aee2f57e07b#3243)
5\. [Observing Your Status](https://medium.com/p/5aee2f57e07b#3815)
6\. [Observing Your Progress](https://medium.com/p/5aee2f57e07b#9261)
7\. [Chaining Requests](https://medium.com/p/5aee2f57e07b#bf73)
8\. [Canceling Work](https://medium.com/p/5aee2f57e07b#05df)
```

# 概观

Android 环境中不断变化的一件事是我们如何处理后台任务。这是因为当手机上的应用数量增加时，后台运行的任务数量也会增加。大多数应用程序开发人员在开始工作之前不会检查电池的健康状况，这导致电池在短时间内耗尽。

我们有许多解决方案来处理后台任务，但是对于开发者和用户来说, [WorkManager](https://developer.android.com/topic/libraries/architecture/workmanager) 都是更好的。WorkManager 有权控制在什么情况下应该执行哪些任务。

如果你的电池电量低，那么它会停止所有在后台运行的任务，并在手机充电后重新启动它们。有了 WorkManager，开发人员也可以轻松地维护周期性请求，而不必麻烦地维护时间戳。

## **工作管理器是:**

*   与 API 级兼容
*   带/不带 Google Play 服务运行

# 综合

WorkManager 是一个 Jetpack 库，它一直在不断更新新特性。WorkManager 开箱即可与协程和 RxJava 兼容。根据您的喜好集成以下工作管理器库。

# 使用工作管理器之前需要了解的事项

在使用 WorkManager 之前，您应该知道两件事情:*约束*和*工作请求的类型。*

## **约束**

约束只不过是一种向工作管理器传达系统应该满足某些条件来执行工作请求的方式。这些情况可能是系统需要互联网连接，也可能是系统处于节电模式而无法工作。

限制

## **工作请求**

工作请求有两种类型:`OneTime`和`Periodic`。

## **一次性工作请求**

从名字本身，我们可以确定这个请求只执行一次。

一次性工作请求

## **阶段性工作要求**

一旦我们启动了 worker，这个请求就会定期执行。在构建一个 worker 对象时，我们必须提到执行之间的特定时间间隔。

定期工作请求

# 创建工人

Kotlin 是 Android 开发的一等公民，协程是处理后台任务的推荐方式。所以这里我们在例子中使用协程工作器。

为了创建一个 worker 类，我们需要用`CoroutineWorker`扩展这个类，并实现覆盖函数`doWork()`(我们将在其中执行任务)。看一看:

## 构建请求

现在我们有了一个工人类，让我们开始后台工作。为此，首先，我们需要创建一个约束集，并使用它来构建一个工作请求，如下所示:

# 观察你的状态

既然我们开始了请求，在某些情况下，我们想知道工作的状态——比如它是成功还是失败。在某些情况下，当约束没有得到满足时，请求甚至不会开始，因此了解请求的当前状态是至关重要的。

WorkManager 有办法做到这一点。它有各种状态，每种状态代表请求的各个阶段。以下是 WorkManager 的一些重要状态。

*   `ENQUEUED`:这个状态表示请求已经排队，一旦满足约束就可以运行
*   `RUNNING`:该状态表示请求已经开始执行
*   `SUCCEEDED`:该状态表示请求执行成功
*   `FAILED`:该状态表示请求由于某种原因失败

你可以在这里找到完整的状态列表[。](https://developer.android.com/reference/androidx/work/WorkInfo.State#ENQUEUED)

WorkManager 使用其`id`提供实时数据来监控请求的状态变化。看一看:

观察工作请求状态

# 观察你的进步

在某些情况下，观察状态变化是不够的——例如，如果应用程序在前台，您想向用户显示请求的进度。

自从 2.3.1 稳定版发布以来，工作管理器已经有了一种方法来做到这一点。我们可以使用`setProgress()`函数异步设置 worker 类中的进度。我们可以设置一个可以被用户界面观察到的中间进度。

观察进度信息和观察你的状态是一样的。您可以使用 `[getWorkInfoById()](https://developer.android.com/reference/androidx/work/WorkManager#getWorkInfoById(java.util.UUID))`或`[getWorkInfoByIdLiveData](https://developer.android.com/reference/androidx/work/WorkManager#getWorkInfoByIdLiveData(java.util.UUID))` 方法并获得对`[WorkInfo](https://developer.android.com/reference/androidx/work/WorkInfo)`的引用。这里我们使用`[WorkInfo](https://developer.android.com/reference/androidx/work/WorkInfo)`类中的进度变量，而不是状态。看一看:

观察工作请求进度

# 链接请求

在一些用例中，我们希望并行地发出一组请求，或者在某些情况下，顺序地发出请求。例如，我们可能有一个图像要上传，但在此之前，我们需要应用过滤器。在这种情况下，一系列动作应该是连续的；首先，我们应该应用所有的过滤器，然后应该执行上传任务。

WorkManager 涵盖了这种现成的场景。看一看:

链接请求

首先，我们通过调用`getInstance`函数获得工作管理器的实例。然后我们调用`beginWith` 函数以及一系列可以并行执行的工作请求(过滤器)。之后，我们使用`then` 函数调用顺序工作器(上传)。我们可以添加多个`then` 函数来调用多个顺序工作器。

# 取消工作

如果先前已经开始的工作没有用，那么我们可以取消请求以节省资源。我们可以使用`cancelWorkById`取消请求，它将请求`id`作为参数。看一看:

```
WorkManager.**cancelWorkById**(downloadWork.id)
```

这里我们需要记住的重要事情是取消一个请求也会导致取消依赖它的请求。

# 奖金

要了解更多关于 WorkManager 的信息——比如如何将 WorkManager 与 RxJava 一起使用、worker 的输入和输出机制，以及如何标记请求——请阅读本文。

感谢您的阅读。