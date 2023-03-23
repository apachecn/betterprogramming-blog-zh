# 如何在没有上下文的情况下，用钩子开发 React 应用程序的全局状态

> 原文：<https://betterprogramming.pub/how-to-develop-global-state-for-your-react-app-with-hooks-without-context-709769ed8505>

## 支持并发模式

![](img/75bd0a2bb2ae13d5dd1062afecefd166.png)

照片由 [Panos Deligiannidis](https://unsplash.com/@panosskier?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/drama?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

# 介绍

用 React 钩子开发很有趣！

我已经开发了几个库。第一个是全球国家图书馆。它被天真地称为“react-hooks-global-state”，结果太长了，读不下去。

该库的初始版本于 2018 年 10 月发布。从那以后，我学到了很多，现在我已经发布了 1.0.0 版的库:

[](https://github.com/dai-shi/react-hooks-global-state) [## 戴式/反应钩式-全局-状态

### 这是一个用 React 钩子提供全局状态的库。它有…

github.com](https://github.com/dai-shi/react-hooks-global-state) 

这篇文章一步一步地展示了代码的简化版本。希望它能帮助你理解这个库的目标，因为真正的代码在 TypeScript 中有点复杂。

# 步骤 1:全局变量

```
let globalState = {
  count: 0,
  text: 'hello',
};
```

让我们有一个像上面这样的全局变量。我们假设这种结构贯穿始终。我们创建一个 React 钩子来读取这个全局变量:

```
const useGlobalState = () => {
  return globalState;
};
```

这实际上不是一个 React 钩子，因为它不依赖于任何 React 原语钩子。这不是我们通常想要的，因为它不会在全局变量改变时重新呈现。

# 步骤 2:更新时重新渲染

我们需要使用 React `useState`钩子使其具有反应性:

这允许从外部更新反应状态。如果更新全局变量，需要通知侦听器。让我们创建一个更新函数。

这样，我们可以改变`useGlobalState`来返回一个类似`useState`的元组:

# 步骤 3:容器

通常，全局变量在文件范围内。让我们把它放在一个函数作用域中，缩小一点作用域，使它更加可重用。

在这篇文章中我们不会详细讨论 TypeScript，但是这个表单允许我们通过推断`initialState`的类型来注释`useGlobalState`的类型。

# 步骤 4:限定范围的访问

尽管我们可以创建多个容器，但我们通常会将几个项目放在一个全局状态中。

典型的全局状态库具有仅限定状态的一部分的功能。例如，React-Redux 使用一个选择器接口从全局状态获取一个派生值。

这里我们采用一种更简单的方法，即使用一个全局状态的字符串键。在我们的例子中，就像`count`和`text`。

为了简单起见，我们在这段代码中省略了`useCallback`的使用，但是对于一个库来说，通常推荐使用它。

# 步骤 5:功能更新

反应`useState`允许[功能更新](https://reactjs.org/docs/hooks-reference.html#functional-updates)。让我们实现这个特性。

# 第六步:减肥药

熟悉 Redux 的人可能更喜欢 reducer 接口。React Hook `useReducer`也有基本相同的接口。

# 步骤 6:并发模式

为了从并发模式中获益，我们需要使用反应状态而不是外部变量。当前的解决方案是将一个反应状态链接到我们的全局状态。

实现起来很复杂，但是本质上，我们创建一个钩子来创建一个状态，然后链接它。

需要`patchedReducer`来允许`setGlobalState`更新全局状态。`useGlobalStateProvider`钩子应该在一个稳定的组件中使用，比如一个应用程序根组件。

请注意，这不是一个众所周知的技术，可能有一些限制。例如，实际上并不建议在 render 中调用侦听器。

为了以适当的方式支持并发模式，我们需要核心支持。目前`useMutableSource`挂钩在[本 RFC](https://github.com/reactjs/rfcs/pull/147) 中提出。

# 结束语

这就是[react-hooks-global-state](https://github.com/dai-shi/react-hooks-global-state)的实现方式。库中的真实代码在 TypeScript 中稍微复杂一点，包含用于从外部读取全局状态的`getGlobalState`，并且对 Redux 中间件和 DevTools 的支持有限。

最后，我围绕全局状态和反应上下文开发了一些其他库，如下所示:

*   [https://github.com/dai-shi/reactive-react-redux](https://github.com/dai-shi/reactive-react-redux)
*   [https://github.com/dai-shi/react-tracked](https://github.com/dai-shi/react-tracked)
*   [https://github.com/dai-shi/use-context-selector](https://github.com/dai-shi/use-context-selector)