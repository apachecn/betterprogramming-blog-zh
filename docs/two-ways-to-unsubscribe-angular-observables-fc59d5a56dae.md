# 取消订阅角度可观测量的两种方法

> 原文：<https://betterprogramming.pub/two-ways-to-unsubscribe-angular-observables-fc59d5a56dae>

## 以一种简单的方式清理有角度的观察点

![](img/5dcd2b5d201e9c1c234febd61881c293.png)

照片由 [Charisse Kenion](https://unsplash.com/@charissek?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄，由作者编辑

Angular 充满了可观察的事物。但是，有没有一种机制可以在组件被破坏时自动取消订阅您的可观测量？

我读了很多关于清理你的订阅的不同帖子。有这么多不同的方法，但我错过了一个帖子，告诉我你应该如何处理你的订阅和什么时候。

为了防止你像我一样阅读所有这些帖子，这里有一个实用的帖子，解释如何轻松取消订阅你的 Observables。

# TLDR；

简短的版本，给那些不想深入了解的人😅。以下是解决方案。

## 异步管道

异步管道让你可以处理 HTML 模板中的可观察对象。在组件的销毁过程之后，异步管道自动运行取消订阅过程。

## 直到

`takeUntil`可以在你订阅之前在`.pipe()`方法中调用。通过这种方法，您可以将您的订阅添加到`Subject`。如果你有一些订阅，你可以通过`.next()`和`.complete()`方法在`ngOnDestroy`事件中取消订阅。

# 1.具有异步管道的 HTML 模板中的可观察对象

Angular 提供了一种简单的方法，通过异步管道`| async`来处理 HTML 模板中的观察值。最棒的是，Angular 替你完成了退订过程，而不是自己动手。

对于奇异值，可以使用异步管道。

或者可以将它用于对象或数组的值。

所以在这个组件中，你不需要用`ngOnDestroy`触发什么东西，但是它会在组件的销毁过程中自动退订订阅。

> 如果你想了解更多关于 Angular 中异步管道的知识，可以看看这篇由 Erxk 撰写的文章“ [Angular 和 RxJS:深入研究异步管道](/angular-rxjs-async-pipe-deep-dive-2510b56f793a)

# 2.使用 takeUntil 方法的可观察值

假设您正在通过`HttpClient`发出多个 AJAX 请求。你不会直接把它传递给 HTML，而是先对数据做些别的事情。所以`| async`管不适合这种情况。

现在您有多个订阅！

怎样才能一次全部退订，而不是一个一个退订？

首先，我们必须在组件中创建一个`Subject`。

这个`Subject`将用于存储我们的订阅。

现在让我们用`HttpClient`在我们的`ngOnInit`进行一些订阅。

如你所见，在`get()`方法之后还有一个`pipe(takeUntil(this.unsubscribe$))`。使用`takeUntil`,我们从这个可观察对象添加一个引用到`unsubscribe$`主题。

在订阅过程中,`Subject`保存对这两个可观测量的引用。

在一个组件将要被销毁之前，调用`ngOnDestroy()`方法。

在这个方法中，我们调用两个方法。

`next()`将向订阅传递一个空值。使用`complete()`，我们告诉订阅它已经完成了对新值的监听。

现在我们不必担心通过`HttpClient`发出一个或更多的请求；我们可以立刻阻止他们。

> 小心使用这个方法，不要忘记给你的组件添加`ngOnDestroy`方法。我们是人，所以我们忘了他们。

[**通过电子邮件获取我的文章点击这里**](https://blog.byrayray.dev/subscribe) **|** [**购买 5 美元中等会员**](https://blog.byrayray.dev/membership)

# 结论

这两个策略在十之八九的情况下都是可用的，所以这是一个在角度应用中实现的好计划。

如果你对如何以简单的方式处理角度应用中的可观测量有其他想法，请告诉我。