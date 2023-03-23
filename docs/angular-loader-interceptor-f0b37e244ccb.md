# 装载机截击机在角度

> 原文：<https://betterprogramming.pub/angular-loader-interceptor-f0b37e244ccb>

## 如何通过拦截 HTTP 请求来显示加载状态

![](img/4a5d9016ae166103f9abe19a1ec9ea5c.png)

基思·约翰斯顿在 [Unsplash](https://unsplash.com/search/photos/catch?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

构建应用程序时，一个关键特性是显示 HTTP 请求的加载状态。

在本文中，我将带您了解如何使用拦截器来拦截所有 HTTP 请求。

有几种方法可以显示加载程序，比如动态显示或者使用结构化指令。我将向你们展示如何使用 NGXS store 来检索加载状态。

# 存储和拦截方法

## 拦截机

创建一个包含拦截器的新文件。我给我的取名`loader.interceptor.ts`。

如您所见，我们在拦截器中调度了两个动作。在我们的请求之前，我们将调度`ShowLoaderAction`，当请求完成时，我们将调度第二个动作`HideLoaderAction`。别急，下面我给大家展示一下 NGXS 商店的实现。

## 商店

NGXS store 非常简单(比 NGRX 简单，甚至包括最新的动作创建器等等)。).您需要创建两个文件:一个包含您的动作，`loader.actions.ts`，另一个包含您的状态类定义，`loader.state.ts`。

这里没有什么不寻常的，这和我们在拦截器中执行的两个动作是一样的。

*   `@State`让您定义商店内的装载机状态
*   `@Selector`为你的商店创建一个选择器(在这个例子中是加载器状态)
*   `@Action`改变监听调度动作的方法，然后执行给定的方法

如果你对此有任何问题，试着去读读 https://ngxs.gitbook.io/ngxs/的作品

## 测试一下

为了测试我们的存储和拦截器，使用选择器选择您的存储，然后在`app-loader`组件上使用`*ngIf`(我将把`LoaderComponent`实现留给您)。

# 动态组件创建和拦截器

## 动态组件—对话框

我正在用`@angular/material`打开我的加载器对话框。有多个选项可用，如`primeng`或定制构建。

## 拦截机

在这种情况下，我们不需要存储或在我们的应用程序组件中做任何事情。在我看来，它看起来更好——代码更少，更简单，等等。

# expressionchangedafterithasbeencheckedererror

对于那些在 Angular 社区呆过一段时间的人来说，这是一个著名的错误。有一篇关于它的很棒的文章[你应该看看](https://blog.angularindepth.com/everything-you-need-to-know-about-the-expressionchangedafterithasbeencheckederror-error-e3fd9ce7dbb4)。

我为什么要提它？因为每当您在一个生命周期挂钩(通常是`ngOnInit`)中调用 HTTP 请求时，您都可能会收到这个错误。

让我们看看如何摆脱它。

## 存储和拦截方法

不使用`setTimeout`将`ShowLoaderAction`分派到事件队列，一个更好的解决方案是使用`Promise.resolve(null).then()`。这导致在对变更进行角度双重检查之后进行 it 分派。

## 动态组件创建和拦截器

与前面相同的解决方案—将您的动态组件创建推送到事件队列。

# 摘要

有许多方法可以向用户显示 HTTP 请求的状态——全局解决方案似乎很适合这种情况。

你有更喜欢的方法吗？请在评论中告诉我！