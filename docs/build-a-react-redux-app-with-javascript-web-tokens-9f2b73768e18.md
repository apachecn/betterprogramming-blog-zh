# 使用 JavaScript Web 令牌构建 React Redux 应用程序

> 原文：<https://betterprogramming.pub/build-a-react-redux-app-with-javascript-web-tokens-9f2b73768e18>

## 设置应用程序来管理 JWT

![](img/4b2926a9985259d9a21a319a983440b9.png)

Redux、React 和 JWT 徽标

这是从头开始创建 React Redux 应用程序的指南。重点是处理用户注册和登录，然后用 JSON Web 令牌(JWT)对他们进行认证和授权。请记住，这里介绍的文件结构和方法只是其中之一。

对于本指南，我们将向已经创建的 Ruby on Rails API 发出请求。作为参考，这里指的是[产生的 Rails API](https://medium.com/better-programming/build-a-rails-api-with-jwt-61fb8a52d833)和 [repo](https://github.com/reireynoso/jwt-rails-api-template) 。

# 设置

让我们用 Create React App 生成一个 React 应用程序。在终端中，运行:

```
npx create-react-app jwt-react-redux
```

将目录更改为`jwt-react-redux`，并打开它。从目录中，运行以下命令来安装 Redux、React Redux 和 Redux Thunk。

```
npm install redux react-redux redux-thunk
```

在包方面，我们已经安装了将 Redux 集成到 React 应用程序中所需的所有东西。

提前考虑 React 应用程序扩展和需要更多数据的时候，最好将我们的 actions 和 reducers 组织到两个独立的文件夹中。在`src`下，我们创建两个文件夹，一个叫做`action`，一个叫做`reducers`。我们可能还想考虑为应用程序可能拥有的多个组件创建一个`components`文件夹。现在，我们将把这些文件夹放在一边，专注于集成 Redux。

# 集成冗余

导航至`src/index.js`。在文件的顶部，我们要添加:

```
import {Provider} from 'react-redux'
```

`Provider`将允许我们向组成我们应用程序的所有组件提供 Redux `Store`(它将包含状态)，这意味着`Store`不必手动传递。相反，想要访问`Store`的单个组件可以直接访问它。

我们将`Provider`视为一个组件，在`Provider`标签中，我们提供了应用程序的其余部分，在本例中是`App`组件。需要将单个道具传递给`Provider`—`Store`以便与应用程序的其余部分共享。

我们现在需要使用来自`redux`的方法创建`Store`。在顶部，让我们添加:

```
import {createStore} from 'redux'
```

`createStore`是在应用程序开始时调用一次以创建 Redux `Store`的函数。它接受一个参数，一个返回对象的函数。这个功能就是所谓的`reducer`。

## 还原剂

我们可以有多个 reducer 函数，每个函数负责状态中的一条信息。在本指南中，我们想要一个登录用户的信息和一个额外的例子，一个评论列表(只是一个空数组)。最好为每种类型的信息创建一个 reducer 函数。

在`src/reducers`下，我们创建两个文件:`userReducer.js`和`commentReducer.js`。先说`userReducer.js`。我们想定义一个变量`userReducer`，并将其设置为一个函数。

src/reducers/userReducer.js

这个函数将默认状态作为第一个参数，并将一个动作对象作为第二个参数。在代码块中，action 对象将有一个属性:type。基于该类型，状态将发生某些变化。通常使用 switch 语句来处理不同的情况。

这个缩减器必须在另一个文件中使用，所以我们必须导出它。因为我们只想导出一个函数，所以我们使用`export default`。

现在让我们导航到`commentReducer.js`。这个函数只是一个例子，所以不包含任何信息。同样，让我们创建一个变量`commentReducer`，并将其设置为一个函数。默认状态是一个空数组作为第一个参数，一个动作对象作为第二个参数。我们还将默认导出这个函数。

src/reducers/commentReducer.js

现在我们已经设置了两个 reducers，我们可以将它们都包含在我们的 Redux `Store`中。但是，`createStore`只能带一个减速器。为了避免这种困境，我们使用 Redux 的另一个函数，它允许我们组合多个 reducers。

在`src/reducers`下再创建一个文件，命名为`index.js`。在这个文件中，我们从 Redux 和 reducers 导入`combineReducers`。

src/reducers/index.js

`combineReducers`接受一个参数，一个提供键值对的对象。键将代表根状态名，值将是管理它的缩减器。当我们`export default`这个`rootReducer`的时候，这将代表我们传入`createStore`的对象。

让我们导航回`src/index.js`，导入`rootReducer`，从 Redux 导入`applyMiddleware`，从`redux-thunk`导入 Thunk。简单来说，`applyMiddleware`和 Thunk 将用于处理调度中的异步获取。`rootReducer`将是`createStore`的第一个参数，`applyMiddleware(thunk)`将是第二个参数。

src/index.js

我们现在已经创建了 Redux `Store`。下一步是生成更新 reducers 的动作。

## 行动

由于`commentsReducer`作为一个例子，它的状态不会改变。我们将关注于为`userReducer`创建动作。在`src/actions`下，我们创建一个名为`userActions.js`的文件。在这个文件中，我们将创建所谓的*动作创建器，以及返回动作对象的*函数。它们将包含一个`type`属性，我们将使用该属性对状态进行适当的更改。更多信息，请参考[文档](https://redux.js.org/basics/actions/)。

我们也有处理异步请求的方法。在这些方法中，我们进行 fetch 调用，并从发送回来的数据中调用 dispatch，使用适当的 action 对象和返回的数据(如果需要的话)作为参数。

src/actions/用户操作/js

`fetchUser`、`signUserUp`和`autoLogin`是处理对服务器的获取请求的例子。提醒一下，本指南的服务器是从另一个指南生成的 Rails API，目前运行在`http://localhost:4000`上。

**注意:**请求成功后，服务器会返回一个带有`user`和`token`属性的对象。`token`代表服务器生成的 JWT，这个会通过`localStorage.setItem("token", data.token)`存储在浏览器中。当向服务器发出另一个要求认证/授权的请求时，可以通过`localStorage.getItem("token")`拉出令牌。

**提醒:**动作创建者是一个返回对象的函数。简单的称之为不会改变我们的状态。Redux 中的一个函数`dispatch`必须使用一个对象作为参数来调用，以便与`Store`进行通信。`dispatch`既可以从组件调用，也可以从动作文件调用。

由于在创建`Store`时集成了`applyMiddleware(thunk)`，所以`fetchUser`、`signUserUp`和`autoLogin`的方法结构是可能的。还没有展示出来，但是这两个方法会从一个`dispatch`内部的组件中调用。但是，如前所述，`dispatch`只能接受一个对象。使用`applyMiddleware(thunk)`，我们能够传递一个函数定义。

有关`applyMiddleware`功能的更多信息，请参考 Thunk 的 [GitHub 页面。](https://github.com/reduxjs/redux-thunk/blob/master/src/index.js)

因为我们有多个函数，所以除了`setUser`之外，我们想对每个函数都使用`export`。该功能仅在`fetchUser`、`signUserUp`和`autoLogin`的上下文中使用，它们在同一个文件中。现在我们已经设置好了`Store`、减速器和动作。下一个重点是如何将 React 组件连接到`Store`。

# 使用“连接”将组件连接到商店

对于本指南，我们将只关注三个组件:生成的`App`、`LoginComponent`和我们将创建的`SignUpComponent`。让我们对`App`组件进行一些调整，并设置到商店的连接。

src/App.js

我们将在`src/components`下创建`LoginComponent`和`SignUpComponent`，但是现在，我们可以导入它们。但是我们将导入的另一个关键资源是来自 React Redux 库的一个命名导出`connect`。

## 探索“connect()”

让我们花几分钟观察一下`connect()`是如何工作的。调用`connect()`实际上是返回一个函数。我们希望调用返回的函数，传入我们希望将`Store`连接到的组件——在本例中，它将是`App`。一旦建立起来，`App`组件就被连接到`Store`。

在`connect()`中，传入的第一个参数是一个函数，通常称为`mapStateToProps`。`mapStateToProps`接受一个参数，它代表状态。该函数需要返回一个对象，其中包含状态所需的信息。在这种情况下，我们只需要`userReducer`信息。为了访问那个对象，我们引用`this.props.userReducer`。

`connect()`还接受第二个参数，通常称为`mapDispatchToProps`。类似于`mapStateToProps`，它接受一个参数，这个参数代表`dispatch`。目标是返回一个定义各种道具的对象，这些道具要调用`dispatch`。道具通常用箭头函数来定义。在这些`dispatch`调用中，我们可以调用我们在`actions`中定义的函数，比如`fetchUser`和`signUserUp` ——但是这些必须首先被导入。

**注意:**并非所有组件都需要来自`Store`的信息或向`Store`发送一个`dispatch`。如果我们只需要信息，我们只需在`connect(mapStateToProps)`中传入`mapStateToProps`。否则，如果我们只需要组件发送一个`dispatch`，我们将第一个参数`null`留下，并将`mapDispatchToProps`作为第二个参数传入`connect(null, mapDispatchToProps)`。

# 用户登录/注册

既然我们能够用`connect()`访问`Store`，让我们开始从服务器获取用户信息，并将其存储在我们的状态中。除了有一个额外的字段外，`LoginComponent`和`SignUpComponent`将非常相似，并且它们将向两个不同的方法发送一个`dispatch`。先说`LoginComponent`。

## 注册

在我们的 Ruby on Rails API 中，当用户登录时，我们需要传递用户名和密码。我们将在这个组件中实现一个表单，并希望它是可控的。我们将包含一个本地组件状态来跟踪输入。对于这个组件，我们只需要从 React Redux 导入`fetchUser`动作和`connect`。

src/components/log in component . js

该组件将只需要向`Store`发送一个`dispatch`。它不需要从中获取信息。所以我们用`(null, mapDispatchToProps)`设置`connect`。当表单被提交时，一个`dispatch`被发送到`fetchUser`方法。`fetchUser`将把存储在本地状态的凭证作为一个参数。

提醒一下，在`fetchUser`函数中，我们向服务器发出一个获取请求，让用户登录。如果凭据匹配，将返回一个包含用户属性和令牌(JWT)的对象。令牌将通过`localStorage`存储，并将代表我们被认证/授权的方式。

另一个`dispatch`在函数中被调用，接受从`setUser`动作创建器返回的对象。该对象被发送到`Store`并与`reducers`通信，转换用户状态信息。

## 签名操作

在我们的 Ruby on Rails API 中，当用户注册时，我们需要传递用户名、密码和年龄。`SignUpComponent`与`LoginComponent`相同，带有一个年龄输入字段。此外，我们从`actions`导入命名的导出`signUserUp`。

src/components/sign up component . js

和`LoginComponent`类似，这个组件只关注`dispatch`。然而，当提交表单时，一个`dispatch`被发送给`signUserUp`方法，但是也接受存储在本地状态中的凭证作为参数。同样的过程发生在`signUserUp`函数内部——将 JWT 存储到`localStorage`中，并将包含`action`类型和用户信息的`dispatch`发送到`reducers`。

## 自动登录

回到`App`组件，我们包括了生命周期方法`componentDidMount`。这个想法是当应用程序加载时，我们希望自动验证/授权一个已经登录的用户。为了实现这一点，我们利用存储在`localStorage`中的 JWT，并将它发送给为我们提供相关用户的服务器。

首先，我们从 actions 导入命名的 export，`autoLogin`。我们在`connect`中设置了`mapDispatchToProps`，并将道具命名为`autoLogin`。在`componentDidMount`内部，我们想要调用这个道具。我们不需要传递任何参数。

在`actions`的`autoLogin`函数中，我们向服务器发出一个 GET `fetch`请求。在消息头中，我们将一个授权密钥集传递给`Bearer <token>`。载体和令牌之间的间隔很重要，因为我们的服务器希望它是这种格式。

服务器接收请求，并根据 JWT 对用户进行身份验证/授权。一旦响应被发送回来，一个类似的过程将 JWT 存储到`localStorage`中，一个包含`action`类型和用户信息的`dispatch`被发送到`reducers`。

## 注销用户

`logOut`的功能不会在应用程序中实现，但它的动作是在`actions`中创建的。根据我们希望哪个组件具有该功能，我们将从`actions`导入名为`logUserOut`的导出。然后可以将一个`dispatch`发送给`Store`，将`logUserOut`作为参数，因为它是 object。在`userReducer`中，用户对象的属性以及被调用的`localStorage.clear()`将被清除，从而移除 JWT。

# 额外奖励:React Redux 挂钩替代方案

React Redux 提供了一组钩子作为现有`connect()` 函数的替代。这些钩子允许您连接到 Redux `Store`并分派动作，而不必将组件包装在`connect()`中。

我们将把现有的`App`类组件重新格式化成一个功能组件。由于这将是一个功能组件，我们无法使用生命周期方法。

我们必须从 React、`useEffect`导入一个命名的导出，以及从 React Redux 而不是`connect`导入`useSelector`和`useDispatch`。

src/App.js(钩子替代)

代替`componentDidMount`，我们将实现`useEffect`函数。它接受两个参数，一个函数和一个空数组。该函数在组件每次呈现时运行。该数组包含依赖项列表。如果其中一个依赖关系的值改变，`useEffect`将再次执行。我们将它作为一个空数组，以避免可能出现的渲染无限循环。

有关 React 挂钩的更多信息，请参考本文。

`useSelector`相当于`mapStateToProps`。接受一个函数，其中状态是参数，我们隐式返回 state 中的一个特定值。`useDispatch`相当于`mapDispatchToProps`。调用它将返回一个`dispatch`，它将接收`autoLogin`。

关于 React Redux 挂钩的更多信息，请参见[本文](https://levelup.gitconnected.com/react-redux-hooks-useselector-and-usedispatch-f7d8c7f75cdd)。

# 结束语

现在你知道了！我们已经创建了一个 React Redux 应用程序，它管理用户验证/授权的 JWT。这涵盖了一个非常基本的设置，没有考虑到边缘情况，比如如果请求没有返回用户怎么办？如果您准备好了，请扩展基本构建模块，包括注销功能和解决边缘情况。感谢您的阅读！

有关详细信息，请参考我的存储库:

*   [GitHub 回购](https://github.com/reireynoso/jwt-react-redux-template)