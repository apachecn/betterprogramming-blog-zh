# 如何使用 ActionsSubject 侦听任何调度的 NgRx 操作

> 原文：<https://betterprogramming.pub/how-to-listen-for-any-dispatched-ngrx-actions-with-actionssubject-9916b69b1e93>

## 每当调度任何操作时得到通知

![](img/7d8ed7c355fe802e7f279a7703f6eeeb.png)

克里斯·莱佩尔特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

[NgRx](https://ngrx.io) 是一个广泛用于角度应用的反应式状态管理库。受 Redux 的启发，NgRx 使用 [RxJS](https://rxjs-dev.firebaseapp.com) 来允许用户以快速和一致的方式管理整个应用程序的全局状态。不是每个应用程序都需要状态管理解决方案，但是将应用程序的状态和逻辑集中起来可以实现强大的功能，比如撤销/重做、状态持久化等等。

为了使用 NgRx，需要理解一些关键概念，而[动作](https://ngrx.io/guide/store/actions)就是其中之一。在 NgRx 上下文中，动作描述了可以从任何地方(例如，从组件和服务)分派的唯一事件。下面的代码显示了一个简单的操作可能是什么样子。显示登录屏幕的组件可以分派这个动作来告诉正在监听的人(例如[效果](https://ngrx.io/guide/effects))应该向我们后端的登录端点发送一个 HTTP 请求。登录数据是通过调度的操作提供的:

一个[组件测试](https://medium.com/better-programming/how-to-write-better-ui-component-tests-with-testing-library-e80bc489051c)可以验证一个特定的动作已经被分派(例如，在提交登录表单之后),我推荐编写这种类型的测试。这种测试可以包含断言，即包含您想要共享的任何数据(例如登录数据)的特定动作已经被分派。

如果您的应用程序变得更大，您的应用程序状态也会随之增长。你可能从几个动作、一个[缩减器](https://ngrx.io/guide/store/reducers)和总共两个效果开始，但几年后，你可能突然有了分散在不同团队拥有的不同应用和库中的动作、缩减器和效果。

在这篇短文中，我想向您展示一种简单的方法来检测一个动作何时被调度。在这个简单的例子中，我将简单地将任何分派的操作记录到浏览器控制台。

# 如何用 ActionsSubject 检测调度的 NgRx 操作

NgRx 提供了一个名为`[ActionsSubject](https://ngrx.io/api/store/ActionsSubject)`的实用程序，我们可以通过依赖注入来访问它。这个名字已经表明我们正在处理一个`[Subject](https://rxjs-dev.firebaseapp.com/guide/subject)`。一只`Subject`是一种特殊类型的`[Observable](https://rxjs-dev.firebaseapp.com/api/index/class/Observable)`。因此，我们可以订阅它以获取它的最新值。在这种情况下，我们通过这个流获取的任何值都是一个已经被分派的 NgRx 动作。

通过订阅`ActionsSubject`，我们将被通知我们应用程序中任何地方发出的任何动作。我们可以记录传入的操作，以验证是否已经使用正确的数据并以正确的顺序分派了适当的操作。这有助于理解在整个会话中如何以及何时分派动作。

关于上面的代码，需要注意一些事情:

*   我添加了`skip(1)`来避免记录 NgRx 自己调度的第一个动作。
*   您应该考虑是否真的要记录任何调度的操作。毕竟，您可能不希望在生产中记录用户凭证。一种方法是在开发模式下只进行监听，从而禁止生产中的任何操作记录。
*   您应该取消订阅以避免内存泄漏。例如，您可以在 Angular 提供的`ngOnDestroy`生命周期钩子中保存对订阅和取消订阅的引用。
*   请不要用这种方法来代替减压器或效果。就个人而言，我认为`ActionsSubject`更像是一个高级特性，对调试很有用。

# 结论

感谢阅读这篇文章。在较大的应用程序中，这种方法可以通过记录任何调度的动作来帮助我们理解动作何时以及如何被调度。幸运的是，NgRx 让我们变得非常容易。不需要另一个软件包或浏览器扩展。

您知道在这种情况下使调试更容易的其他方法吗？请在评论中告诉我。