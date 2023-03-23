# 以角度控制数据流

> 原文：<https://betterprogramming.pub/controlling-data-streams-in-angular-3e43484bedb3>

## 在大型角度应用程序中的工作模式和查看模式之间切换

![](img/f7b335bf07bc9561dee8175278828c7f.png)

格雷格·罗森克在 [Unsplash](https://unsplash.com/s/photos/streams?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

如果你在 Angular 中开发前端应用程序，那么你肯定会以某种方式使用 [RxJS](https://angular.io/guide/rx-library) 。在这篇文章中，我想谈谈我在开发严重依赖 RxJS 的大规模应用程序时遇到的一个问题，以及我解决这个问题的步骤。

需求很简单:允许应用程序在两种模式下工作:工作模式和查看模式。当处于工作模式时，组件之间的每种通信都应该发生(相应地处理每个键盘和鼠标事件)，并且应该触发适当的动作。在查看模式下，应该禁用事件处理程序，这样就不会触发任何键盘或鼠标事件(否则感兴趣的订阅者应该无法听到)。最后，允许从一种模式切换到另一种模式的按钮也应该是可用的。

正如我希望对这个场景提供一个简单的解释，这个解决方案初看起来可能非常简单。当单击切换模式按钮(工作模式)时，事件发射器的每个订阅者都应该取消订阅，当切换到查看模式时，每个旧订阅者都应该再次重新订阅以侦听鼠标和键盘事件。

现在想象这样一个场景，您有多个对键盘或鼠标事件感兴趣的组件。当更多不同的事件发生时，比如 HTTP 调用、套接字订阅等等，会发生什么呢？将上述解决方案应用于每个用例是不合适的，也是不可扩展的。

我们需要的是一个集中的地方，我们可以通过切换它发出的值来控制数据流。每个感兴趣的组件都可以订阅受控数据流，并且只在数据流活动时接收值。另一方面，包含模式切换按钮的组件将启用或禁用受控数据流。

让我们用 Angular 编写一个简单的应用程序来展示这个场景。为了集中数据流控制，我们创建了一个可以注入到任何感兴趣的组件中的服务。出于演示的目的，我只使用了一个切换工作模式并订阅数据流的组件。让我们来看看这个应用程序:

主要焦点是集中服务 DataService，它提供对控制器数据流的访问。有两个主要组件，一个`toggleObservable`和一个`dataObservable`。

`toggleObservable`负责模式切换按钮事件流。它还控制如何将数据流中的值发送给感兴趣的订阅者。

另一方面，`dataObservable`负责由鼠标和键盘事件产生的数据流。

让我们看看下面的主要代码:

将受到模式切换影响的每个组件都将订阅由函数`getControllableDataStream()`返回的可观察值。它从`toggleObservable`发出的值开始(可选地，它从`true`值开始)。只有当发出的值是`true`时，它才能通过使用`[concatMap](https://rxjs-dev.firebaseapp.com/api/operators/concatMap)`将发出的布尔值映射到`dataObservable`来返回由`dataObservable`发出的值。

但是我们还没有完成，因为对于由`toggleObservable`发出的每个后续值，订阅者仍然会收到数据，即使它是关闭的(应用程序处于预览模式)。我们最终需要的是允许`dataObservable`发出所有值，直到`toggleObservable`发出一个`false`值。我们通过使用`[takeUntil](https://www.learnrxjs.io/learn-rxjs/operators/filtering/takeuntil)`操作符来做到这一点，当下一次`toggleObservable`发射发生时，该操作符将取消对`dataObservable`的订阅。

模拟在`AppComponent`中给出，每次模式切换(点击按钮)后都会发出数值，接收到的数据记录在控制台中。您可以通过几次点击*开/关*进行试验，并在控制台中查看结果。

我希望这篇文章能帮助你在处理不同的基于 RxJS 的应用时选择正确的方法。

保重。