# 角度为 6+的先前路线

> 原文：<https://betterprogramming.pub/previous-route-in-angular-6-484288410d99>

## 构建一个简单的服务，在 Angular 应用程序中提供以前路线的信息

![](img/f76dd236e56ebc30bae39e7846de10d3.png)

Javier Allegue Barros 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Angular 的特点是功能强大、用途广泛的[路由器](https://angular.io/api/router)。最近，我需要确保我的组件能够访问用户在我的应用程序中访问的前一条路线的信息。令我惊讶的是，路由器目前并不支持这种开箱即用的功能。但它仍然提供了快速构建这一功能的必要手段。

# 范围

我的用例假设我只需要访问我的各种组件的`ngOnInit()`生命周期钩子内的前一条路径，并且我不需要在页面重载过程中保持信息的持久性。所以我决定:

*   创建一个名为`PreviousRouteService`的简单根范围服务
*   利用路由器事件来跟踪和捕获以前和当前的路由信息

# 服务

首先，我创建了一个简单的角度服务，其作用范围在应用程序的根级别。然后，我导入了路由器，并为`previousUrl`添加了属性和一个`getter`方法。然后，我订阅了所有路由器事件，以查看路由更改期间发生了什么:

在路由改变期间，路由器触发[多种类型的事件](https://angular.io/api/router/RouterEvent)，您可以针对符合您要求的事件。在我的例子中，我决定使用`[NavigationEnd](https://angular.io/api/router/NavigationEnd)`,因为我的应用程序使用了`canLoad` / `canActivate`守卫，并且我想只统计导致用户实际导航到这样一个页面的成功的路由更改:

# 进一步改进它

上面的内容通常是可行的，但是挑战在于，通过使用`NavigationEnd`，我们面临一个*竞争条件:*当导航循环仍在进行并且`NavigationEnd`事件尚未触发时，页面用户被重定向到对我们的构造函数或`ngOnInit`钩子内部的服务的调用。

所以这里的选择是要么使用另一个在调用`getPreviousUrl`之前可靠结束的`RouterEvent`(比如`[ResolveEnd](https://angular.io/api/router/ResolveEnd)`)，要么利用订阅(比如`[BehaviorSubject](https://www.learnrxjs.io/learn-rxjs/subjects/behaviorsubject)`)而不是简单的 getter:

现在，有了订阅，我们可以这样在应用程序的组件中使用我们的服务，记住要在组件销毁挂钩上取消订阅:

现在，解决方案工作可靠，我可以实现进一步的功能，这需要用户知道以前访问过的路线。

# 结论

希望你喜欢这篇短文，祝大家编码愉快。