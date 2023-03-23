# 如何使用 RxJS 编写一个类似 Redux 的状态管理存储

> 原文：<https://betterprogramming.pub/how-to-write-a-redux-like-state-management-store-using-rxjs-33b6095c5a7e>

## 也许你不需要另一个包？

![](img/033ca5157c21bb50c505ab37a2979dfc.png)

照片由[卡拉·埃尔南德斯](https://unsplash.com/@karlahrnndz?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/puzzle?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在使用 Vue 多年后，最近我开始使用 Angular 11，并被介绍给 RxJS 和 NgRx。

当我了解了 RxJS 的基础知识并意识到它的强大时，我开始想，也许我们不需要安装另一个库来管理状态。

所以我决定冒险写一个小的状态管理模块来代替我的项目中的 NgRx。我提出的约束是基于使它类似于 Redux，并在下面列出:

*   用打字稿写的
*   基于缩减器函数更新状态
*   有一个调度方法来更新状态
*   分派方法将接收动作作为输入
*   动作将由两个键组成:类型和有效载荷
*   Action payload 将是可选的，这样我们就可以调度除了实际状态之外不需要外部信息的操作
*   `subscribe`让我对状态变化做出反应的方法
*   `select`让我对特定的状态键变化做出反应的方法
*   `dispatchAsync`异步更新状态的方法

最后一个约束显然不是受 Redux 本身的启发，但是众所周知，我们需要异步操作。在 Redux 中，我们通过使用像`redux-thunk`或`redux-saga`这样的插件来做到这一点，所以我决定尝试实现类似的东西。

定义好一切之后，我们就可以开始编写代码了。

我们将开始用名称`RxJsStore`定义我们的类。因为我们仍然不知道我们将要管理的状态的类型，我们将使用[泛型](https://www.typescriptlang.org/docs/handbook/2/generics.html#hello-world-of-generics)，这样用户可以在类实例创建的时候通知状态的类型。我们在 TypeScript 中通过在类名后添加一个`<T>`来实现这一点。

除此之外，我们已经可以创建私有变量来存储我们的缩减器和状态。我选择将状态设为私有，这样就不能从类外部直接更改它，只有类方法能够做到这一点。

在 RxJS 中，我发现可以用来管理状态的三种主要类型:`[Observable](https://rxjs.dev/guide/observable)`、`[Subject](https://rxjs.dev/api/index/class/Subject)`和`[BehaviorSubject](https://rxjs.dev/api/index/class/BehaviorSubject)`。在这种情况下，我更喜欢使用`BehaviorSubject`,因为我们可以随时获得最后发出的值。

带`Observable`和`Subject`发射值仅在发射时刻通过 subscribe 方法可用，这可能是个问题。

reducer 类型会稍微复杂一点，尤其是你不太懂 TypeScript(像我一样)。但是本质上 reducer 是一个函数，它接收两个参数，当前状态和动作(类型和有效负载)，并返回更新后的状态。

我们在类型描述中使用括号来告诉 TypeScript 哪个是函数参数(以及它们的类型)以及一个指向函数响应类型的箭头。这就像一个带有类型的箭头函数。

在类构造函数中，我们将收到两个类似 Redux 的参数:

*   将更新状态的减速器函数
*   初始状态

我们还必须定义`Action`接口，以便减速器类型定义是正确的。

所以我们从这个开始:

接下来，我们可以构建我们的订阅、调度和选择。subscribe 方法是最简单的方法，因为 RxJS 的`BehaviorSubject`已经实现了它。我们所要做的就是接收回调函数并订阅私有状态。回调函数将接收当前状态值，并且不返回任何内容(void)。该符号与我们用来定义减速器类型的符号非常相似。返回类型`Subscription`由 RxJS 提供。

dispatch 方法将接收动作，将当前状态和动作应用到 reducer，并用结果更新状态。为了更新一个`BehaviorSubject`的值，我们需要使用它的`next`方法。

我知道我可以用一行代码来完成，但是，我选择创建常量来使它更具可读性。另一点是，我必须将它声明为一个箭头函数，这样对`this`的引用就不会丢失。这是必要的，因为该方法将由`asyncDispatch`调用:

现在是`select`方法——我理解中最难的一种。

这种方法使得监听特定的状态键变化成为可能。这样做，回调函数就不会被不必要的调用。但是 RxJS 足够强大，可以为我们提供工作所需的工具。

首先，该方法将返回一个用户可以订阅并对变化做出反应的可观察对象。为了使这个`Observable`仅在所选键的值改变时发出新值，我们必须使用多个 RxJS 操作符。

为此，我们使用了一个特殊的操作符`[pipe](https://rxjs.dev/api/index/function/pipe)`。它可以接收任意数量的参数，每个参数都是另一个操作符，它将按顺序应用它们。

管道内的第一个操作员将是`[distinctUntilKeyChanged](https://rxjs.dev/api/operators/distinctUntilKeyChanged)`。它接收代表我们将观察其变化的键的字符串，并且将阻止`Observable`发出新的值，除非键值发生变化。第二个是`[pluck](https://rxjs.dev/api/operators/pluck)`。它接收一个密钥，并返回它在状态中的当前值。

对我来说，最棘手的部分是定义参数类型和返回值。我希望参数是一个字符串，并根据状态键自动进行验证。并且返回值与状态键的类型相同。为了实现这一点，我们将利用 TypeScript `[keyof](https://www.typescriptlang.org/docs/handbook/2/keyof-types.html)`和`extends`。

基本上，我们创建一个新的`type(K)`来扩展包含状态`(T)`键的枚举。因此，在参数中，我们将接收类型为`K`的键，并返回状态(`T`)键(`K`)的`Observable`。

现在是最后一部分，即`asyncDispatch`。为了实现这个功能，我们将接收动作类型、一个字符串和 async runner 方法。然后，我们将使用 await 来等待运行程序执行。之后，我们将发送动作，将收到的类型和 runner 返回值作为有效负载。够简单吗？我想。

对于类型，参数将是一个字符串——动作名——和一个异步函数 runner。运行器将接收当前状态值作为参数(因此，如果需要，可以根据状态值执行不同的操作)，并将返回一个`Promise`。

为了使方法类型更安全，我使用了泛型来接收 runner 返回类型`R`。返回类型将是 Promise，因为我们使用的是 void (nothing)类型的 async-await。我无法描述返回类型，但是因为我们使用的是 TypeScript，所以我更愿意这样做，即使它毫无用处。

为了不丢失对类的引用，我必须将它声明为一个箭头函数。我试图在构造函数中手动进行绑定，但是没有成功。

解释完所有部分，编写好所有需要从 RxJS 导入的内容，我们的类将看起来像下面的代码:

为了测试它，我写了一个简单的代码。`state`有两把钥匙:`counter`和`name`。减速器由三种型号组成，`INCREMENT`、`DECREMENT`(计数器)和`CHANGE_NAME`。这三者都依赖于有效载荷来运行。

我使用了一个延迟 5 秒的`asyncDispatch`,因此我们可以注意到，虽然这是我们调用的第一个分派，但它只在所有其他分派完成后运行。由于承诺解析为 5，计数器最终将被更新回零。

我们还可以注意到，select 方法按预期工作。我注意到的一个缺点是`BehaviorSubject`在创建时发出一个值，所以我们得到的不是 4 而是 6，但我认为这很好。

# 结论

源代码可以在我的 [GitHub](https://github.com/n0n3br/RxJsStore) 上获得，并作为 [npm 包](https://www.npmjs.com/package/@n0n3br/rxjs-store)提供。希望你能从这篇文章中学到一些有用的东西。感谢阅读。