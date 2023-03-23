# 从 Redux Thunk 到 Redux-Saga 的重构

> 原文：<https://betterprogramming.pub/refactoring-from-redux-thunk-to-redux-saga-f9379a172a99>

## 提高 JavaScript 应用程序的可伸缩性

![](img/892dc11bbdf1d4f943c4d8f456fc62c9.png)

图片来自 [Redux-Saga](https://redux-saga.js.org/) 。

许多正在阅读这篇文章的人可能都有过使用 [Redux Thunk](https://github.com/reduxjs/redux-thunk) 的经历，知道它能做什么，也许正在考虑换成 [Redux-Saga](https://redux-saga.js.org/) 。

为了快速复习，让我们查阅一下 Redux Thunk 的文档:

> “对于普通的基本 Redux 存储，您只能通过分派动作来进行简单的同步更新。中间件扩展了商店的功能，并允许您编写与商店交互的异步逻辑。
> 
> Redux Thunk 中间件允许您编写返回函数而不是动作的动作创建器。thunk 可用于延迟动作的调度，或者仅在满足特定条件时才进行调度。"

好吧，所以 Redux Thunk 工作良好。那我们为什么要选择佐贺呢？和任何决定一样，每个决定都有利弊。我认为你选择 Thunk 而不是 Saga 的主要原因是为了快速和简单的项目，因为设置 Thunk 要容易得多。除此之外，佐贺几乎总是要走的路。

# 为什么是 Redux-Saga？

> “Redux-Saga 是一个库，旨在使应用程序副作用(即数据获取等异步操作和访问浏览器缓存等不纯操作)更易于管理、更高效地执行、更易于测试以及更好地处理故障。”— [Redux-Saga 的文档](https://redux-saga.js.org/)

出于可扩展性的原因，上面提到的所有特性都是 Saga 优于 Thunk 的原因——尤其是在测试方面。虽然测试 thunks 并非不可能，但它需要模仿在 thunks 中使用的 API 调用和其他函数，导致代码可读性更差、更混乱。

此外，虽然 Redux Thunk 使用回调函数来允许我们管理异步逻辑，但 Redux-Saga 就像是应用程序中的一个独立线程来管理它。由于线程可以从主应用程序启动、暂停和取消，这实质上意味着我们可以完全控制 Redux 应用程序的状态。

# 从 Thunk 到 Saga 的重构

要从 Thunk 重构到 Saga，我们首先需要更好地理解 Saga 是如何工作的。

假设我们想要一个按钮，当它被点击时从 API 请求一些数据。

## 1.派遣行动

```
fetchData() {
    const { data } = this.props
    dispatch({type: 'FETCH_REQUESTED', payload: data})
  }
```

## 2.创建一个 saga 来监视所有 FETCH_REQUESTED 操作，这些操作将触发 API 调用来获取数据

为了更好地理解上面的代码，请注意以下几点:

*   正如 [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield) 所指出的，“关键字`yield`暂停生成器函数的执行，关键字`yield`后面的表达式的值返回给生成器的调用者。”
*   `call(api.fetchData, action.payload.data)`调用接受`action.payload.data`作为参数的`fetchData`(异步)函数。
*   结果变得类似于`await [promise]`。
*   `put`指示中间件相应地分派动作。
*   注意`takeEvery`和`takeLatest`的区别。这就是 Redux-Saga 如此强大的原因！

现在我们已经了解了 Saga 的一般工作原理，让我们看看如何从 Thunk 重构到 Saga。

使用 Thunk，我们的`actions`文件将包含如下所示的函数:

在这里，我使用一个单一的函数作为一个例子，从以前的项目。只关注结构。

现在，要将我们的代码重构为 Saga，我们可以遵循以下步骤:

1.  像承诺的那样重构对一个单独文件的 API 调用。
2.  重构函数以返回一个普通的 action 对象，而不是 thunks(即回调函数)，其 action 类型声明请求了一个 API 调用(这里，我称之为`CREATE_USER_DATA`)。然后导出所有不同的动作类型。

# 创造传奇

为我们的传奇故事创建一个新文件:

结果是，每当我们想要创建新的用户数据时，我们只需简单地写下:

```
dispatch(createUser());
```

Redux-Saga 会完成它的使命！最后但同样重要的是，我们需要应用 Saga 中间件。

# 应用 Saga 中间件

目前，这是我使用 Thunk 的代码的样子:

```
import thunk from "redux-thunk";
import reducers from "./reducers";
const store = createStore(reducers, applyMiddleware(thunk));
```

为了应用 Saga，我们首先创建中间件并用 Thunk 替换它。请注意，同时使用 Saga 和 Thunk 是可能的。它们并不相互排斥，但是需要另一种配置。

```
const sagaMiddleware = createSagaMiddleware();
const store = createStore(reducers,applyMiddleware(sagaMiddleware));
```

我们完事了。这并不太难，是吗？希望这篇文章对那些从 Thunk 过渡到 Saga 的人有所帮助。感谢阅读！