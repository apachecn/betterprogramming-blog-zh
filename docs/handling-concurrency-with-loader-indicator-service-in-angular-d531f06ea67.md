# 用 Angular 中的加载器指示器服务处理并发

> 原文：<https://betterprogramming.pub/handling-concurrency-with-loader-indicator-service-in-angular-d531f06ea67>

## 编写一个容忍并发的角度加载器服务

![](img/5b949789d1ee7fb010234a4266e92177.png)

照片由[诺德伍德主题](https://unsplash.com/@nordwood?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

每当 web 应用程序执行一个异步任务时，比如一个 HTTP 请求或动画页面转换，添加一个加载器动画是一个好主意，可以通过设置用户期望来提高 UX(比如说，“我正忙着执行一个请求”)。

# 简单案例

因此，我们可以编写一个简单的“加载器”服务，在一个简单的角度/离子应用程序中完成这项工作:

每当一个组件需要一个加载指示器来显示时，我们会通过依赖注入(DI)将这个服务导入到这样的组件中，我们会根据需要调用它，就像这样:

这适用于简单的应用程序，但随着应用程序变得越来越大，就变得非常混乱，因为对于每个潜在需要“加载微调器”UX 的函数，我们都需要在代码中处理显示/隐藏逻辑。

# 拦截器和路由器

为了避免混乱的代码，一种方法是在我们应用的大多数异步功能的核心实现显示/隐藏处理: [HTTP 拦截器](https://angular.io/api/common/http/HttpInterceptor)和[路由器](https://angular.io/api/router)。向拦截器和路由器添加逻辑将确保对于每个网络请求和页面转换，我们得到一个负载指示器。

我们可以在应用程序的头部实现一个简单的非阻塞的小型旋转器动画(例如 [ion-spinner](https://ionicframework.com/docs/api/spinner) ),而不是沉重的“阻塞”(带背景)加载 spinner。但是出于本文的考虑，我们将考虑重的情况——我们使用异步构造的 [Ionic 的加载控制器](https://ionicframework.com/docs/api/loading)。

下面是我们如何向拦截器添加隐藏/显示加载器逻辑，方法是导入我们的加载器服务，并在每次请求开始时调用`.showLoader()`，使用 [finalize RXJS 操作符](https://www.learnrxjs.io/learn-rxjs/operators/utility/finalize)调用`.hideLoader()`，确保在请求完成后调用`hideLoader()`(无论成功还是出错):

下面是我们如何为路由器做同样的事情，我们将订阅[路由器事件](https://angular.io/api/router/RouterEvent)，过滤它们以只关注`NavigationStart`和`NavigationEnd`事件，并调用我们的加载器服务来相应地显示和隐藏加载器:

现在已经实现了逻辑，但是一旦我们开始测试我们的应用程序，我们就会意识到有许多可能导致错误的“竞争”条件:

*   如果有两个以上的 API 请求并行运行。
*   如果有一个 API 请求正在运行，我们的用户转换到另一个页面。
*   如果 API 请求的页面转换发生得太快，并且在创建离子加载时，我们已经对它调用了“解除”。

即使我们使用简单的负载指示器，我们也可以观察到它在上述情况下闪烁。所有这些意味着我们当前的解决方案不能容忍并发请求。

# 处理并发性

那么，我们如何战胜并发性并避免竞争情况呢？一个流行的解决方案是实施“反制”策略。我们可以通过递增一个反映请求数量的计数器来显示加载指示器，而不是立即创建一个加载器组件。这种方法仍然不能处理并发性，因为在很多情况下，这种计数器会从某个数值快速下降到零(尽管它确实部分地改善了这种情况)。

我发现的最好的解决方案是利用 [RXJS](https://www.learnrxjs.io/) 的力量，因为它在 Angular 中得到本地支持，并且因为它是用来处理事件流的，并且具有处理并发性的工具。

## 第一步

让我们重构我们的加载器服务，将对`showLoader` / `hideLoader`的请求从实际的“创建加载器”和“解除加载器”逻辑中分离出来，这样我们就不必在`Interceptor`和`Router`的基础服务中更改我们的实现:

为此，我们将使用 [RXJS Subject](https://www.learnrxjs.io/learn-rxjs/subjects/subject) ，它一旦被订阅，将启用一系列`false`和`true`值，这些值将反映我们的应用程序中隐藏或显示加载指示器的请求:

*   我们使用 so-loading`RequestsStream$.next(value)`这样的主语来传递请求的动作。
*   我们将初始化转移到一个单独的方法中，`initValues()`，，并从构造函数中调用它(它将在应用程序启动时调用一次)。
*   在`initValues()`中，我们简单地订阅了`Subject`,并基于它随时间接收的值，我们调用创建加载器或解除加载器。

所以现在当我运行应用程序时，它有点工作，但并发仍然是一个大问题。以上只是为我们提供了实际操作的“管道代码”。

## **第二步**

处理并发性将要求我们确保请求流是序列化的。我们可能还希望消除一个接一个的相同请求，保持流的独特性:

当前流可以是:

`***true>true>false>false>true>false>true>true>false>false…***`

我们需要的是:

`***true>>>>false>>>>true>false>true>>>>>false>>>…***`

为此，我们将在管道中导入并使用`[DistinctUntilChanged](https://www.learnrxjs.io/learn-rxjs/operators/filtering/distinctuntilchanged)` RXJS 操作符:

```
this.loadingRequestsStream$.pipe( distinctUntilChanged(),).subscribe();
```

如果我们的加载器指示器是一个简单的离子旋转器，我们可能已经有了一个闪烁的指示器，它根据 API 和路由器请求的活动来开关。在我们的例子中，我们正在处理的事实是，Ionic 的加载控制器异步旋转加载指示器。因此，我们需要一些东西来确保在之前创建或解散加载程序的承诺完成之前，不会发生新的此类操作。

[RXJS ContactMap](https://www.learnrxjs.io/learn-rxjs/operators/transformation/concatmap) 操作符确切地告诉我们，它会一直等待每个请求完成，然后再开始新的请求。实现如下:

现在，我们可以用许多请求来轰炸我们的服务，它可以很好地容忍它！现在的问题是，当我们有多个请求在彼此之间很快发生时，我们仍然会看到我们的负载指示器显示/隐藏几次，而不是在这种紧密间隔的请求的持续时间内显示指示器。

# 进一步改进它

在这最后一章，我们将解决多个请求间隔很近的问题。

一种方法是确保在我们收到对`hideLoader`的请求后，我们不会马上拒绝它，而是引入一个计时器，它会在一定时间后触发`dismiss()`方法，在我的例子中，我选择了 700 毫秒(这是我们的用户观察 loader 的最小时间)。我们这个定时器`lazyDismissTimer`，我们在我们的代码中检查它的状态，当定时器被清零时，我们就使它无效。现在我们只需要记住，我们的`createLoader`和`dismissLoader`方法应该总是返回一个`Promise`，以确保`concatMap`可以基于它的排序和步调。服务的完整和最终实现如下:

# 结论

加载指示器是 UX 的重要组成部分，我最初很天真，认为实现这样的加载器服务是微不足道的，特别是如果你使用异步加载器组件，比如 Ionic 的离子加载。好消息是 RXJS 提供了许多有用的操作符，使得这样的实现成为可能。

通过这种方法，我们还可以让我们的大多数组件没有显示/隐藏请求代码，并避免跨所有组件导入`loader.service.ts`的需要，只关注那些真正需要它的组件。

感谢您的阅读和快乐编码！