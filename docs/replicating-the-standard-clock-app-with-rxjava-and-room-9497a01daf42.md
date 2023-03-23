# 用 RxJava 和 Room 复制标准时钟 Android 应用程序

> 原文：<https://betterprogramming.pub/replicating-the-standard-clock-app-with-rxjava-and-room-9497a01daf42>

## 使用 Android 中的可观察模式

![](img/37d19630e7071fcfcc397f386683d618.png)

[Malvestida](https://unsplash.com/@malvestida?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

当我学习流和反应式编程时，我发现了 RxJava，但是因为它看起来很复杂而推迟了学习。

我最终决定试一试，创建一个应用程序，在那里我很容易找到可观察的模式，所以我选择创建一个标准 Android 时钟应用程序的克隆。

# 该应用程序

我把这个项目命名为“`ClockClone`”我的目标是识别标准时钟应用程序中适用观察者模式的所有用例，然后使用 RxJava 自己重新创建它，以便我可以学习它。

为了简单明了，我没有把这个应用程序做成了一个完全的克隆——不是所有的功能都被复制了。其中，警报实际上并不工作，也没有全屏通知。

# 使用案例

该应用程序的主要功能源自时钟应用程序主屏幕上的四个选项卡:

*   警报—设置和管理警报。只有报警数据的持久存储，实际上不会安排报警。
*   世界时钟-查看世界主要城市的时间和天气情况。
*   秒表—运行秒表。它还可以显示分割时间和单圈时间。
*   计时器—运行一个简单的倒计时计时器。

我将只讨论应用程序中与 RxJava 相关的部分。

# 代码

所有代码都可以在 Github [这里](https://github.com/DavidGrath/Clock-Clone)找到。为了简洁起见，本文中的代码被缩短了。

# RxJava

RxJava 是[react vex API](https://reactivex.io/)的 Java 实现。引用他们的主页:

> ReactiveX 结合了观察者模式、迭代器模式和函数式编程的最佳理念

ReactiveX 中的所有操作都围绕着[可观测量](https://reactivex.io/documentation/observable.html)的使用。它们的功能类似于 Java 期货，但是是为处理异步值序列而不是单个值而构建的。

Observables 发出三种主要的事件:onNext、onComplete 和 onError。可观察序列由零个或多个 onNext 事件组成，并以 onComplete 或 onError 事件终止，但不能同时以两者终止。

ReactiveX 附带了一大组操作符，可用于对可观测量进行各种操作，如转换、过滤、合并可观测量、聚合操作等。我只在应用程序中使用了其中的一些，我将对此进行讨论。

对于这个应用程序，我使用的是 RxJava 3，特别是版本`3.0.0`。查看他们的 Github [发布版](https://github.com/ReactiveX/RxJava/releases)页面，获取完整版本列表。

# 假设

您熟悉以下内容:

*   观察者模式
*   加装
*   房间
*   Android 应用架构
*   Java 并发框架
*   匕首

# 使用的库

*   [RxJava](https://github.com/ReactiveX/RxJava)
*   [RxAndroid](https://github.com/ReactiveX/RxAndroid)
*   [房间](https://developer.android.com/jetpack/androidx/releases/room) —包括 RxJava 适配器
*   [生命周期](https://developer.android.com/jetpack/androidx/releases/lifecycle#java) —包括使用 RxJava 的反应流
*   [改装](https://github.com/square/retrofit#download)
*   [改装 RxJava 适配器](https://github.com/square/retrofit/tree/master/retrofit-adapters/rxjava3#download)

# 跑表

秒表所需的可观察数据包括自启动以来经过的时间(以毫秒计)——运行时间、自用户最后一次单击“lap”以来经过的时间(以毫秒计)——分段时间、状态(`OFF`、`RUNNING`、`PAUSED`)——主要用于 UI 和通知，以及分段和分段时间列表，每次单击“分段”或“分段”时都会更新。想到这一点后，我发现需要一种特殊的可观察对象才能手动发出事件。

## 学科

[主体](https://reactivex.io/documentation/subject.html)同时充当数据的观察者和被观察者。有四种主题:

1.  PublishSubject:从订阅点向观察者发出值。在订阅之前发出的所有值都不会被接收。
2.  ReplaySubject:从头开始重放流，并发出新值。
3.  BehaviorSubject:与 PublishSubject 相同，但发出订阅前的最新值。有初始状态的流就是一个很好的例子。
4.  AsyncSubject:只向观察者发出最后一个值。该值仅在主体调用`onComplete()`时发出。

我为 UI 选择了 BehaviorSubject，以便当它从后台重启或配置更改时，能够获取可观察对象的最后状态。

为了跟踪时间并以固定的时间间隔发送更新，我使用了[ScheduledThreadPoolExecutor](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ScheduledThreadPoolExecutor.html)。这是比 Handler 更好的选择，因为 Handler 是在主(UI)线程上执行的。

# 计时器

计时器的工作原理与秒表非常相似。可观测的数据包括以毫秒为单位的剩余时间和状态(`OFF`、`COUNTING`或`PAUSED`)。我还使用了 BehaviorSubject 和 ScheduledThreadPoolExecutor。

# 世界时钟

世界时钟的主要子功能是能够看到世界上所有主要城市的当前时间。第二个子功能是能够看到这些城市的汇总的当前天气情况。

可观测数据包括一个计时器，用于更新当前显示的时间、天气状况和城市列表。我要指出的是，我在开发世界时钟时遇到了困难——我一直在改变代码的结构，直到我找到一个运行良好的类结构。

这个特性的核心数据是时区标识符。看起来是这样的:

```
Africa/Lagos
```

它可以用来获取主要城市的当前时间以及天气状况。我使用来自 [TZDB](https://www.iana.org/time-zones) 的数据创建了一个时区列表，并将它作为 CSV 文件存储在应用程序资产中。

当前时间是从`DateFormat`获得的，它使用时区标识符为 UI 适当地格式化它。计时器在每分钟的第 0 秒停止计时，以触发 UI 更新。

天气状况是从网络请求中获取的，我稍后会谈到。用户保存的城市列表存储为简单的逗号分隔字符串。

我使用了`SharedPreferences`,因为无论何时发生变化，它都可以向观察者发出信号，而且数据库也是多余的。

## 气象条件

我选择使用 [AccuWeather](https://developer.accuweather.com/) 来进行改造和试用 RxJava 呼叫适配器。

通过调用`Builder`对象上的`addCallAdapterFactory()`来创建带有适配器的客户端接口。

可以选择为适配器设置一个调度程序。调度程序用于多线程。查看[此处的](https://www.baeldung.com/rxjava-schedulers)了解更多信息。

## 单事件可观测量

标准的 Observable 是为处理一系列异步任务而构建的，但是网络请求只是一个单独的任务。虽然可以将 Observable 与 reform 一起使用，但 RxJava 中有一些特殊类型是为了处理一次性事件而构建的。

*   单一:发射`onSuccess` 或`onError`。onSuccess 是用单个项目发出的。
*   Completable:发出`onCompleted` 或`onError`，但不发出任何未完成的项目。
*   可能:会发出`onSuccess`、`onComplete`或`onError` 事件中的任意一个。`onSuccess` 有单品。

## 取消订阅

在处理天气状况时，我意识到，如果应用程序在网络请求完成前关闭，能够取消这些请求是非常重要的。

网络请求是内存泄漏的潜在来源，进而可能导致崩溃。在 ReactiveX 中，订阅者是表示订阅可观察对象的特殊类型，但在 RxJava 中，它们被称为一次性的。

一次性用品将在需要时用于取消请求。他们有一个`dispose()`方法，取消订阅 Observables。

一个可观察对象通过调用`subscribe()`返回一个可处置对象。`CompositeDisposables`帮助一次性处理一组一次性物品，类似于标准收集的 forEach 操作。

## 每分钟更新一次

interval observable 每分钟发出一个带有无用项的事件，通知 UI 更新当前时间。为了每分钟更新列表，我使用了`combineLatest()`操作符。

# 警告

我决定使用一个数据库来跟踪警报，以便查看、修改和删除它们，这样就为 RxJava 适配器留出了空间。

我从这篇文章[中发现](https://medium.com/androiddevelopers/room-rxjava-acb0cd4f3757)Observable，Maybe，Single 和 Completable 可以作为一个 Room DAO 方法的返回类型。另外，也可以使用`Flowable` 。

可流动是一种支持背压的可观察对象，背压是一种可观察对象发送事件的速度快于观察者处理它们的速度的情况。可以使用`toFlowable()`将可观察值转换为可流动值，并将背压策略作为参数。像 Maybe 和 single 这样的单个事件类型不将策略作为转换的参数。我只在代码中使用了`BackpressureStrategy.BUFFER`。查看更多关于[背压](https://www.baeldung.com/rxjava-backpressure)和[流量](https://www.baeldung.com/rxjava-2-flowable)的链接。

## 转换到 LiveData

所有可观察的类型都可以转换成 LiveData，这样它们就可以用于视图模型。它们必须被转换成可流动的形式，并被传递给`LiveDataReactiveStreams.fromPublisher()`方法。

我没有用 LiveData 实现的一件事是错误处理。来自可观察对象的错误事件将导致 LiveData 抛出 RuntimeException，这将导致应用程序崩溃。

在将可能的错误传递给 LiveData 之前，您应该将它们转换成有意义的 UI 状态对象。从 Google 示例中查看这个实现。

# 结论

我对 RxJava 有一些想法。这是一个强大的库，有很大的潜力，我觉得我仅仅触及了`ClockClone`的表面。然而，我确实学会了很好地利用它来观察 Android 上的模式。

我还喜欢它不是 Android 开发独有的这一事实，因此它可能在其他领域派上用场。我松了口气，我终于有时间来学习这个。我希望你也学到了一些东西。