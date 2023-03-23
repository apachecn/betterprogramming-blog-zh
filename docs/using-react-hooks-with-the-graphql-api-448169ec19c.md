# 如何在 GraphQL API 中使用 React 钩子

> 原文：<https://betterprogramming.pub/using-react-hooks-with-the-graphql-api-448169ec19c>

## 集成前端开发中最热门的两个东西:Hooks 和 GraphQL

![](img/6d6e0de9d1502f6e7d75d14e64d75ccb.png)

照片由 [JJ 英](https://unsplash.com/@jjying?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/integration?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# **什么是挂钩？**

钩子是让你在不使用类组件的情况下进入“状态”的函数。

不用考虑使用哪种生命周期方法，您现在可以编写考虑如何以及何时使用数据的组件。

React Hooks 于 2018 年 10 月推出，2019 年 2 月发布。React 16.8 及更高版本现已提供。

# **挂钩为什么受欢迎？**

1.  没有样板。您不需要导入新的库或编写任何样板代码，您可以在 React 16.8 及更高版本中直接开始使用钩子。
2.  不需要使用类组件来使用状态。即使我们想在 React 组件中使用状态，也不再需要编写类组件。以前，如果我们写了一个函数组件，我们想在其中使用状态，我们必须把它改成类组件。通过钩子，我们可以在功能组件中使用状态。
3.  安装和清理功能。你不再需要考虑 React 什么时候挂载一个组件，你应该在`componentDidMount`做什么，记得在`componentWillUnmount`清理。钩子会把这些东西从盒子里拿出来。

钩子说够了吗？让我们转向商业…

# **入门**

```
npm install @apollo/react-hooks
```

这个包导出了`ApolloProvider`组件，将客户端连接到 React 应用程序，就像旧的 API 一样。

```
npm install graphql-tag
```

将 GraphQL 查询字符串解析成标准 GraphQL AST 的 JavaScript 模板文字标签。

# **一些常见的钩子**

## 1.使用状态

`useState`用于设置和更新状态，如类组件中的`this.state`。

```
const [ state, setState] = useState(initialState);
```

## 2.使用效果

效果挂钩允许您在功能组件中执行副作用。副作用可能包括 DOM 操作、订阅和 API 调用。

```
useEffect(() => {
  document.title = 'Hello World' 
});
```

## 3.用户教育

`[useState](https://reactjs.org/docs/hooks-reference.html#usestate)`的替代方案。接受类型为`(state, action) => newState`的缩减器，并返回与`dispatch`方法配对的当前状态。(如果你熟悉 Redux，你已经知道这是如何工作的。)

```
const [ state, dispatch ] = useReducer(reducer, initialArg, init);
```

## 4.使用上下文

接受一个上下文对象(从`React.createContext`返回的值)并返回该上下文的当前上下文值。当前上下文值由树中调用组件上方最近的`<MyContext.Provider>`的`value`属性决定。

当组件上方最近的`<MyContext.Provider>`更新时，这个钩子将触发一次重新渲染，将最新的上下文`value`传递给那个`MyContext`提供者。

不要忘记`useContext`的参数必须是*上下文对象本身*:

*   正确:`useContext(MyContext)`
*   不正确:`useContext(MyContext.Consumer)`
*   不正确:`useContext(MyContext.Provider)`

# **graph QL 是什么？**

根据[官方文件](https://graphql.org/):

> " G raphQL 是一种 API 查询语言，也是一种用现有数据完成这些查询的运行时。
> 
> GraphQL 为 API 中的数据提供了完整且易于理解的描述，使客户能够准确地要求他们需要的东西，使 API 更容易随时间发展，并支持强大的开发工具。"

有两种主要类型的操作，查询和突变。

*查询*用于检索数据，而*变异*用于创建/更新/删除数据。

GraphQL 的另一个重要特性是可以使用单个端点和单个网络请求执行多个操作。这减少了往返和整体数据传输的次数，这在移动设备和糟糕的网络情况下非常重要。

# **例题**

添加新用户(并选择在哪个字段上获得结果)的 GraphQL 变体:

```
mutation CreateUser($name: String!){
  createUser(name: $name) {
    name
  }
}
```

获取用户列表的 GraphQL 查询:

```
{
  users {
    name
  }
}
```

## **真实世界查询示例**

首先，我们将创建一个名为`GET_DOGS`的 GraphQL 查询。记住将查询字符串包装在`gql`函数中，以便将它们解析成查询文档:

接下来，我们将创建一个名为`Dogs`的组件。在其中，我们将把我们的`GET_DOGS`查询传递给`useQuery`钩子:

当我们的查询执行并且`loading`、`error`和`data`的值改变时，`Dogs`组件可以根据查询的状态智能地呈现不同的 UI 元素:

*   只要`loading`为`true`(表示查询仍在进行中)，组件就会显示一个`Loading...`通知。
*   当加载为`false`且没有`error`时，查询完成。该组件呈现一个下拉菜单，其中填充了服务器返回的狗品种列表。

## **真实世界突变示例**

现在我们知道了如何查询数据，让我们看看如何创建突变。

现在我们将看到如何调用我们上面创建的突变来保存记录。

# **结论**

钩子是反应的一个强大的附加物。此外，React Hooks 是一个强大的库，可以在 React 应用程序中使用带有 GraphQL 的钩子。我建议你现在就用钩子弄脏你的手，因为你已经有基础了。

你今天学到新东西了吗？评论和反馈总是让作者开心！