# Observable 如何工作 JavaScript 中的 Observable 模式介绍

> 原文：<https://betterprogramming.pub/an-introduction-to-the-observable-pattern-in-javascript-3e6ae0ebc22>

![](img/526319dbd4792c951db2ce8da72d66c0.png)

Andrew Coop 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

如果你曾经使用过 JavaScript，那么你很有可能使用过`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)`。这是该语言的一个超级有趣和强大的特性，但它的局限性是它只能处理单一事件(当某件事情完成或失败时)。

正因为如此，`Observables`在很多情况下会更加有用。

# 可观与承诺

`Observables`可以提供`Promise`的特性，处理零个或多个事件，像流一样工作。默认情况下，`Observables`可以被取消，只有当你订阅时才开始，而不是像`Promises`那样立即开始。最重要的是，你可以使用操作符，如果需要的话甚至可以重试。

# 可观察的行话

当你在谈论`Observables`时，你需要熟悉几个概念:

*   `Observable`:懒惰的多值施法者。
*   `Observer`:来自`Observable`的价值消费者。
*   `Subjects`:一种特殊类型的`Observable`，其工作方式类似于事件发射器，可以向许多`Observers`多播值，因为它保存了所有`Observers`的列表，其中`Observable`只能单播。
*   `Subscription`:订阅时返回的对象。它代表一种可任意使用的资源。
*   `Operators`:用于管道`Observable`值或创建`Observables`的函数。
*   `Scheduler`:控制订阅开始时间和消息发送时间的部分。

# 理解可观察的 API

我想使用第三方库，但是我相信如果我展示一个简单得多的`Observable`代码会更好，这样你就可以理解在引擎盖下发生了什么。让我们先了解一下`Observable`是如何工作的。

```
const obs = new **Observable**((subscriber) => {
  // some logic
});
```

用函数初始化`Observable`。这个功能就是`observer`。`observer`被一个对象调用，即`subscriber`。一旦用`subscriber`对象调用了`observer`函数，它就可以调用这个订阅者中的三个方法——方法`next`、`error`、**、**和`complete`。

```
const obs = new **Observable**((subscriber) => {
  subscriber.**next**(20);
  subscriber.**next**(30);
  subscriber.**complete**(40);
});
```

上面的代码会发出`20`、`30`，然后用`40`完成订阅。您使用`next`方法来发出任何值。您可以使用`error`方法在出错的情况下发出一个错误。您可以用`complete`方法发出信号表示您已经完成了值的发出，该方法(像`error`方法一样)将*终止订阅*。

当您初始化一个`Observable`时，您得到一个`observable`对象，您可以调用 subscribe 方法来初始化值发出。

当您订阅一个`observable`时，您会得到一个`subscription`对象。`subscribe`方法有三个功能。只有第一个是必需的。也可以用包含`next`、`complete`和`error`方法的单个对象来调用它。

这些值仅在您订阅时发出，而不是在您创建`observable`时发出。这意味着，对于我们的例子，每次我订阅，我会得到三个值，并完成。因为`subscription`完成了，我不需要`unsubscribe`，这是一个你可以在订阅时返回的`subscription`对象上调用的方法。

# 创建一个基本的可观察对象

一个简单的`observable`实现可以是这样的:

这段代码显示，当您初始化`Observable`时，它会接受一个`observer`函数，但在调用`subscribe`方法之前不会对它做任何事情。这就是为什么`subscription`是触发数值发射的功能。

第一个`if`和`else`简单地覆盖了`complete`方法来调用`unsubscribe`，这可以是一个由`observer`返回的函数来帮助清理`subscription`。

然后我们用一个包含三个方法的`Observer`对象调用`observer`函数，这三个方法将调用传递给`subscribe`函数的三个方法。`Observer`可能看起来像这样:

正如您可能已经意识到的，这是一个回调系统，它以一种奇怪的前后方式将函数匹配在一起，从而创建了`subscription`系统。

实现可能要复杂得多，因为这甚至不包括调度器和管道值的能力。要开始使用`Observables`，考虑安装并使用 [RxJs 库](https://rxjs.dev/guide/observable)。它附带了大量的操作符和其他特性。

# 使用可观测量

用`Promises`不能做的事情是一遍又一遍地发出值。下面的代码显示了一个使用`[setInterval](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setInterval)`定时器函数的例子:

你也可以使用它作为一个简单的文件上传器，并监控进度，这比使用`Promise`更好。美妙的是停止上传非常容易。

用`Promise`实现这一点将需要`Promise`和回调的奇怪组合，这几乎让人难以理解。

查看本文的所有源代码。

# **结论**

打开一个完全不同的编程世界。它们使您的应用程序具有无限的能力和更多的控制力，具有反应性和事件驱动性。你甚至不需要把`Promises`留在后面，因为把它们和`Promises`结合起来也一样容易。

考虑冒险进入`Observables`的世界，用 JavaScript 等等改进你的编程方式。[react vex](http://reactivex.io/)是一个功能强大的库，拥有多种语言版本，如 Java、Python、Javascript、Kotlin、C++等。看看这个。

![](img/79122679204c8ac0aa65ca22857737ab.png)

***YouTube 频道*** *:* [*分号前*](https://www.youtube.com/channel/UCrU33aw1k9BqTIq2yKXrmBw)***网站****:*[*beforesemicolon.com*](https://beforesemicolon.com/)