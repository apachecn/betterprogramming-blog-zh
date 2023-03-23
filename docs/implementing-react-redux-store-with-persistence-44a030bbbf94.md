# 用持久性实现 React Redux 存储

> 原文：<https://betterprogramming.pub/implementing-react-redux-store-with-persistence-44a030bbbf94>

## 具有持久性的集中式应用状态存储

![](img/8ac1cce912f6aefb9c497251967bbfca.png)

照片由[蒂姆·埃文斯](https://unsplash.com/@tjevans?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/save?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

# 什么是 React Redux？

React Redux 是一个集中式状态，它允许您将状态从单个组件中分离出来。这使您可以轻松地跨组件共享和同步应用程序状态，从而消除不必要的状态链。

在本文中，我将介绍以下内容，以便在任何 React 应用程序中设置 Redux Store。这个项目的完整资源库将在文章末尾链接。

```
1\. [Creating the Redux Store](#d103)
2\. [Modifying State with Reducers](#4aca)
3\. [Importing State and Reducers into Components](#93cf)
    1\. [Function Component](#deb2)
    2\. [Class Component](#1c0c)
4\. [Adding Persistence](#fff7)
```

# 创建 Redux 存储

为了将 Redux 商店添加到我们的应用程序中，我们首先需要导入 React-Redux 和 Redux 工具包。

```
npm i react-redux @reduxjs/toolkit
```

我们现在准备建立我们的 Redux 商店。Redux 存储由两个主要组件组成，存储本身和一个数据片。store 本质上是一个包装器，负责将数据切片注入到应用程序中。数据片本身就是状态和归约者所在的地方。

让我们从创建数据切片开始。我们使用 Redux Toolkit 中的方法`createSlice`，来创建数据切片。该方法接受一个 JSON 对象，其中包含我们的数据片名称、initialState 对象和 reducers 对象。

当导出我们的数据切片时，我们创建两个导出语句。一个用于 reducer 方法，`DataSlice.actions`，另一个默认导出用于数据切片本身，`DataSlice.reducer`。这个默认的导出可能会有点混乱，因为它看起来似乎只应该是 reducer 方法。默认导出是传递到 redux 存储包装器中的内容。

反应数据切片

现在我们有了数据切片，我们可以创建我们的商店。这个存储非常简单，通过将我们的数据片传递给 Redux Toolkit `configureStore`方法来创建。然后，我们导出我们的商店，稍后注入到我们的应用程序中。

Redux 商店

一旦我们的商店被创建，我们需要把它注入到我们的应用程序。我们通过将应用程序包装在 React-Redux 的`Provider`组件中来实现这一点。提供者组件将我们的存储作为一个道具，允许我们稍后将其导入到我们的组件中。

将 Redux Store 注入应用程序

# 用减速器修改状态

减速器方法由两个参数组成，`state`和`actions`。状态将始终是应用程序的当前状态，并自动传入。Actions 是一个 JSON 对象，由两个变量组成，`type`和`payload`。类型将被自动设置为您的数据切片和 reducer 方法的名称，在我们的例子中是`dataSlice/updateMessage`。有效负载将由我们从组件传递到 reducer 方法的数据组成。

需要记住的一个要点是，有效载荷只能包含一个数据点。因此，如果我们需要将多个值传递给 reducer 方法，我们需要使用 JSON 对象将它们传递给有效负载。

当用 redux 更新状态时，我们不需要担心变异动作。Redux 将自动处理状态中的克隆和创建新对象。因此，我们能够直接更新我们的 reducers 中的状态。

# 将状态和减速器导入组件

现在我们已经设置好了状态和还原器，我们可以将它们导入到我们的组件中。根据函数或类组件的使用，有不同的方法来导入状态和归约器。我将从展示钩子在功能组件中的使用开始。

## 功能成分

我们将使用 React Redux 中的两个钩子`useSelector`和`useDispatch`将我们的状态和 reducers 导入到我们的函数组件中。`useSelector`钩子允许我们从数据切片中选择某个状态值，并将其保存为局部变量。`useDispatch`钩子让我们能够直接从组件内部调用 reducers。

将 Redux 状态和 Reducers 导入功能组件

## 类别组件

至于类组件，我们无法使用 hook，所以将使用不同的方法。这种导入状态和归约器的方法也适用于函数组件，但是，我强烈推荐使用 hook 方法。

对于类组件，我们将利用 React-Redux 中的`connect`方法。该方法类似于常用于导入样式的`withStyles`方法。connect 方法最多接受两个参数，一个用于状态导入，另一个用于还原器。

我们将分别使用`mapStateToProps`和`mapDispatchToProps`方法导入 state 和 reducers。这两种方法都将返回一个对象，该对象包含组件所需的状态和 reducers。mapStateToProps 有两个参数，`state`和`ownProps`。state 参数是自动填充的，是来自我们的数据切片的状态对象，ownProps 可用于传入本地状态属性。mapDispatchToProps 有一个单独的`dispatch`参数，该参数由类似于`useDispatch`钩子的分派方法自动填充。

然后，connect 方法将状态和 reducers 绑定到我们的 props，允许我们使用`this.props.<state or reducer>`访问它们。

将 Redux 状态和 Reducers 导入类组件

# 添加持久性

在这一节中，我将介绍如何向 Redux 存储添加持久性，以便在页面刷新时保存数据。这对于 Redux 本身来说是不必要的，只是取决于您的用例。

当向我们的应用程序添加持久性时，我们首先需要导入一个额外的库 Redux Persist。

```
npm i redux-persist
```

一旦我们安装了 Redux Persist，我们将需要对我们的 Redux 存储和 index.html 文件进行一些更改。让我们从我们的 Redux 商店开始。

Redux store with Redux Persist

乍一看，这与我们最初的商店实现有很大不同。主要的变化是分别在 Redux Persist `persistReducer`和`persistStore`方法中包装我们的数据切片和存储。

`persistReducer`方法允许我们传入我们的数据片和一个持久化配置。这个配置允许我们控制如何保存我们的存储。要记住的主要配置是存储将被持久化的位置。在这个例子中，我们通过使用下面的 import `import storage from ‘redux-persist/lib/storage'`来使用浏览器的本地存储。

我们的商店有很多选择，在这里都可以找到。

除了配置我们的数据切片，我们还需要向存储添加一些额外的配置。这种配置是中间件的形式。持久性使用可序列化检查来确保我们存储的数据是可序列化的。但是，我们不希望对 Redux Persist 本身执行的某些操作执行这种检查。

一旦我们的中间件建立起来，我们就把我们的存储包装在`persistStore`中，允许它被 Redux Persist 管理。我们还需要导出我们的存储以及从`persistStore`返回的这个新的持久化器。既然我们的商店已经为持久性重新配置了，我们需要将我们的 persistor 注入到我们的应用程序中。

将 Redux Persistor 注入应用程序

为了注入我们的 persistor，我们将使用 Redux Persist 提供的`PersitGate`组件。这个组件将是我们的`Provider`组件的一个子组件，包含我们应用程序的其余部分。

# 包装东西

我们已经成功地将一个 Redux 商店添加到 react 应用程序中，并为我们的商店添加了持久性。正如承诺的，这个项目的回购可以在下面找到。有两个分支，`main`和`react-persist`。Main 有基本的 redux 设置，而 react-persist 有持久性添加。

[](https://github.com/EJOzyazgan/React-Redux-Setup) [## GitHub-EJOzyazgan/React-Redux-Setup:React 中基本 Redux 设置的示例报告

### 这个项目是用 Create React App 引导的。在项目目录中，您可以运行:在…中运行应用程序

github.co](https://github.com/EJOzyazgan/React-Redux-Setup)