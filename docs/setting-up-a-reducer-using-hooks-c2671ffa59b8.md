# 如何设置由 useContext React 挂钩调用的 Reducers

> 原文：<https://betterprogramming.pub/setting-up-a-reducer-using-hooks-c2671ffa59b8>

## 使用挂钩设置减速器

![](img/bd444bb9b0b197336890abf9c1cdba99.png)

照片由[张秀坤·施罗德](https://unsplash.com/@wirhabenzeit?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在上一篇文章中，我写了如何通过创建一个`createDatacontext.js`文件来抽象状态逻辑。在那篇文章的最后，我还提供了一个例子，说明如何使用这个`createDataContext`来实际创建上下文，以便可以在任何地方调用这个状态。在本文中，我将介绍如何创建一个上下文文件，以及如何将它添加到一个组件中。

创建任何东西的第一步都是创建指定的文件—在这种情况下，它看起来像这样:

```
ExampleContext.js
```

该文件和 createDataContext 文件应该位于同一个上下文目录中。我们的下一步是从 createDataContext 导入上下文逻辑。

```
import createDataContext from './createDataContext'
```

在导入我们的上下文逻辑之后，我们将为我们的例子初始化 reducer。为此，我们将创建一个名为`exampleReducer`的函数，它将接收一个动作和一个有效载荷，然后使用 switch 语句修改状态，默认情况下，它将返回应用程序的状态。

```
import createDataContext from './createDataContext'const = exampleReducer = (action, payload)  =>{
  switch (action.type){
   default: 
     return state
  }
}
```

有了基本的缩减集 u，p，我们现在将创建一个导出语句，它将导出两件事:上下文和提供者。它将通过传入我们的 Reducer(例如 reducer)、我们的 actions(我们还没有创建它，所以现在我们将只添加一个空对象({}))和我们的初始状态(在本例中，是一个空数组([]))来调用`createDataContext`。

```
import createDataContext from './createDataContext'const = exampleReducer = (action, payload)  =>{
  switch (action.type){
   default: 
     return state
  }
}export const {Context, Provider} = createDataContex(
  exampleReducer,
  {},
  []
)
```

现在我们已经设置了导出和缩减器，我们可以开始添加修改状态的动作了。但首先，我想快速说明一下。我不打算添加任何获取请求，因为这更像是一个通用模板。

我们将创建的第一个动作是向 state 添加一个示例。为此，我们将在 switch 语句中添加一个`add_example` case，它将返回添加了新示例后的先前状态。然后我们将创建一个名为`addExample`的函数。在这种情况下，我们将用一个新的例子来调度我们的`add_example`(这将是一个 ID 和一个随机数的值)。最后，我们将把我们的`addExample`动作添加到导出中。

接下来，我们将添加一个删除示例的案例和操作。case `delete_example`将通过 state 进行过滤，并删除匹配有效负载(在本例中是一个 ID)的示例。`deleteExample`动作将接收一个 ID，然后发送`delete_example`，将 ID 作为有效载荷传递。初始化`deleteExample`动作后，我们将导出它。

最后，我们将添加一个编辑操作和案例，并将其导出。该案例将被命名为`edit_example`，它将映射整个州，找到我们正在编辑的示例，并替换它。我们的动作`editExample`将接收一个 ID 和一个新值，并发送`edit_example`。

这样，我们就创建了一个模板，用于在 React 应用程序中利用状态为大多数事物创建上下文。为此，我们可以输入一个组件来导入我们特定的上下文文件和`useContext`钩子，然后初始化我们的状态和助手方法:

非常感谢您的阅读。编码快乐！