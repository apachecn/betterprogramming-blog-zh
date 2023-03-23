# 角度 6 Url 参数

> 原文：<https://betterprogramming.pub/angular-6-url-parameters-860db789db85>

## 如何在 Angular 中从 URL 和查询字符串中检索参数

![](img/fc7c37218bf34e5def567dba30a1ca81.png)

我注意到有些人对 Angular 中的路由感到困惑。具体来说，从 URL 和查询字符串中检索参数存在一些混淆。这是一个相当简单的任务，但它可能有助于一起完成一个示例。幸运的是，我创建了一个 [StackBlitz](https://stackblitz.com/edit/get-url-parameters) 来帮助解释使用快照和订阅之间的区别。让我们来看看吧。

# 路线

出于我们的目的，让我们关注路由参数。我们将创建路线`"animals/:animal"`。有了这条路线，我们可以导航到`animals/dog`或`animals/cat`，从`:animal`参数中检索`dog`或`cat`。

冒号就是证据。这就是 URL 的第二部分成为参数的原因。

# 访问 URL

Angular 为我们提供了`ActivatedRoute`对象。我们可以通过这个对象访问 URL，但是首先，您必须将它注入到您的组件中。像任何其他服务一样注入它:

# 访问 URL 参数

有两种普遍接受的访问 URL 参数的方法。一个是通过`route.snapshot.paramMap`，一个是通过`route.paramMap.subscribe`。两者之间的主要区别在于，订阅将随着特定路由的参数变化而不断更新。这比解释容易，所以请确保使用 [StackBlitz](https://stackblitz.com/edit/get-url-parameters) 示例。

## 快照

顾名思义，使用快照是一次性事件。一个典型的用例是在组件加载时获取参数。显式读取代码；当我加载组件时，我将获得 URL 参数。

如果同一组件内的参数发生变化，此策略将不起作用。更明确地说，从`animals/dog`更改为`animals/cat`不会破坏和初始化`AnimalComponent`，所以`ngOnInit`方法不会被第二次调用。

## 签署

使用订阅与使用任何其他订阅是一样的。如果有变化，那么可观察值将被推送到回调函数。*如果这不清楚，那么做些功课，仔细阅读*[*RxJS*](http://reactivex.io/intro.html)*。我也曾* [*写过关于它的*](https://medium.com/@christo8989/the-not-so-daily-blog-007-c0c84bedc1ef) *。*仅当 URL 参数在当前路线内发生变化时，此策略才有效。

这种策略可能有些矫枉过正，但是如果你仍然困惑的话，这绝对是最安全的策略。

此外，[没有必要取消](https://stackoverflow.com/a/41359138/2573621)对`paramMap`的订阅。`ActivatedRoute`随路由组件一起消亡，因此订阅也随之消亡。

# 访问查询参数

访问查询字符串参数类似于访问 URL 参数。这只是`ActivatedRoute`对象上的不同属性；`queryParamMap`。因此，所有相同的原则都适用，但要确保使用正确的属性。

# 高级—使用开关映射

在我的研究中，我发现了一个[角度示例](https://angular.io/guide/router#route-parameters-in-the-activatedroute-service)，它通过一个`switchMap`传递参数。什么是`switchMap`？长答:不知道。简而言之:它基本上是在新请求到来时取消一个请求。

例如，如果我订阅了`paramMap`，并开始发送对路由参数的更改，`switchMap`将取消任何未决的请求，并获取新的请求。这在大多数情况下是多余的，但它可能是有用的。

等等！上面的代码没有反映出[角度示例](https://angular.io/guide/router#route-parameters-in-the-activatedroute-service)。我在`switchMap`中忽略了他们的 HTTP 请求。让我们用下面的例子更深入一点。

在上面的例子中，我在`paramMap`订阅中发出一个 HTTP 请求。希望大家都知道 HTTP 请求是比较贵的。如果我滥发参数变化，我会想取消任何以前的请求。在这种情况下，使用`switchMap`就可以做到这一点，从而提高性能。

# 结论

总而言之，从 URL 获取参数非常简单。如果参数可以在同一组件/路由中更改，请使用快照进行一次性抓取或订阅。如果您在订阅中发出 HTTP 请求，那么使用`switchMap`来取消任何不必要的未决请求。

*如果您发现任何错误或只是想说声嗨，请留下评论或建议。:)*