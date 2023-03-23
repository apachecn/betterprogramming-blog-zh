# 如何使用反应跟踪

> 原文：<https://betterprogramming.pub/how-to-use-react-tracked-c2b13706ff68>

## 用 immer 反应面向钩子的待办事项列表

![](img/003288633a7bae1f38f78a5b6eecddbc.png)

# 介绍

React 钩子改变了我们编写组件的方式。这篇文章将展示一个面向钩子的例子。

我们将使用两个库:react-tracked 和 immer。immer 使得以不可变的方式更新状态变得容易，而 react-tracked 使得通过跟踪读取状态以进行优化变得容易。更多详情请查看回购协议。

[https://github.com/dai-shi/react-tracked](https://github.com/dai-shi/react-tracked)

我们展示的例子来自 Redux: [Todo List](https://redux.js.org/basics/example) 。

# 文件夹结构

```
- src/
  - index.tsx
  - state.ts
  - hooks/
    - useAddTodo.ts
    - useToggleTodo.ts
    - useVisibilityFilter.ts
    - useVisibleTodos.ts
  - components/
    - AddTodo.tsx
    - App.tsx
    - FilterLink.tsx
    - Footer.tsx
    - Todo.tsx
    - VisibleTodoList.tsx
```

我们有两个文件夹:`components`和`hooks`。组件基本上是视图，钩子包括逻辑。

# src/state.ts

在这个例子中，我们没有使用减速器。我们只定义一个状态和一些类型。

注意最后一行——可能有点复杂。`SetState`是`setState`的一种类型。

# src/hooks/useAddTodo.ts

这是负责添加项目的钩子。我们这里用 immer，但没必要。

# src/hooks/useToggleTodo.ts

同样的想法，用这个钩子来切换一个项目。

# src/hooks/usevisibilityfilter . ts

这个钩子用于返回当前的`visibilityFilter`和一个 setter 函数。我们为此使用`useTracked`。是组合式挂钩，组合`useTrackedState`和`useDispatch`。

# src/hooks/useVisibleTodos.ts

这个钩子处理 Todo 项目的过滤。

# src/components/AddTodo.tsx

除了从`hooks`文件夹导入`useAddTodo`之外，这里没有什么特别需要注意的。

# src/components/Todo.tsx

这是一个没有挂钩依赖的组件。

# src/components/visibletodolist . tsx

这和原来的例子不一样。我们把过滤逻辑移到了钩子上。

# src/components/FilterLink.tsx

这使用了`useVisibilityFilter`挂钩。注意钩子返回一个元组、一个值和一个 setter 函数。

# src/components/Footer.tsx

这个组件没有什么特别需要注意的。

# src/components/App.tsx

这是将其他组件组合在一起的组件。

# src/index.tsx

最后，我们需要入口点。

注意`<Provider>`从 state.ts 通过了`useValue`。

# 在线演示

[回购中的源代码](https://github.com/dai-shi/react-tracked/tree/master/examples/07_todolist)

# 结束语

在我写这篇文章的时候，我注意到了一些事情。我最初的动机是展示如何使用反应跟踪。然而，这个例子也展示了 setState 和定制钩子如何在没有 reducers 的情况下分离关注点。我的另一个发现是，至少在这个例子中，immer 在定制钩子方面没有太大帮助。

我们没有过多讨论性能优化。还有改进的余地。最简单的一个就是用`React.memo`。优化可能是未来文章的主题。

这篇文章最初发表于[这里](https://blog.axlight.com/posts/how-to-use-react-tracked-react-hooks-oriented-todo-list-example/)。