# 扔掉 Redux，使用 Redux 工具包

> 原文：<https://betterprogramming.pub/throw-out-redux-use-redux-toolkit-aa247f18520e>

## 如何在 TypeScript 中使用 Redux Toolkit

![](img/d376e848e7da4ed309d2f93baaaf01ad.png)

照片由[特雷西·亚当斯](https://unsplash.com/@tracycodes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 辞旧迎新

不久前，当我第一次开始使用 Redux 时，我简直不敢相信我需要写这么多代码才能让任何东西工作。坦白说，这让我很害怕。尽管从内存中编写所有的设置代码可以让您感觉非常聪明，但过一段时间后，它会变得很无聊，您希望使用更直观、更简单的方法。

Redux 对初学者来说也很难掌握，对我来说也很难，直到我发现了 [Redux 工具包](https://redux-toolkit.js.org/)。我仍然感到惊讶的是，它并没有得到应有的广泛讨论，人们似乎还停留在旧的 Redux 上。

如果你想开始学习 redux，或者想要一种更直观的方式来编写 Redux，RTK 是一个不错的选择。

在本文中，我将向您展示如何用 TypeScript 和 React 设置 RTK。

# 为什么要用 Redux Toolkit？

*   **类型定义** —你可以通过 TypeScript 轻松使用它。
*   **直接改变状态** —你不再需要复制状态或者传播状态。RTK 带有 [Immer](https://immerjs.github.io/immer/docs/introduction) ，它允许你直接在代码中改变状态，而 Immer 则负责所有的繁重工作。
*   **Redux Thunk** —在常规 Redux 中编写异步代码需要安装额外的中间件，如 Redux Saga 或 Redux Thunk。不再是了。RTK 与 [Redux Thunk](https://www.npmjs.com/package/redux-thunk) 捆绑在一起，提供所有的异步功能。
*   **一个文件** —所有的缩减器、操作和操作创建者都有一个文件。
*   **Redux Dev Tools**——我无法告诉你有多少次使用 [Redux DevTools](https://github.com/reduxjs/redux-devtools) (你需要在你的浏览器中安装扩展才能使用它)帮助我更快地调试代码，并阻止我有想砸键盘的日子(不，我永远不会那样做——希望如此)。RTK 附带了 Redux DevTools，这意味着您永远不需要添加任何额外的中间件来使用它。

你可以在 GitHub 上看到这个应用程序完成的回购:[https://github.com/onedebos/typescript-with-redux-example](https://github.com/onedebos/typescript-with-redux-example)

# 让我们开始吧—设置

首先，像往常一样，使用 CRA 和 TypeScript 模板创建一个名为 rtk-app 的新应用程序。

```
 npx create-react-app rtk-app — template typescript
cd rtk-app 
```

在你最喜欢的代码编辑器中打开应用程序。

# 设置 Redux 工具包

让我们从安装所需的包开始。我们将构建一个简单的应用程序，向 [NASA APOD API](https://github.com/nasa/apod-api) 发出请求。

我们需要好的 ol' [React Redux](https://react-redux.js.org/introduction/quick-start) 和 redux-toolkit 本身。 [Redux Toolkit](https://github.com/reduxjs/redux-toolkit) 是用 TypeScript 编写的，所以不需要为包安装@types。

```
 npm install [@types/react-redux](http://twitter.com/types/react-redux) react-redux [@reduxjs/toolkit](http://twitter.com/reduxjs/toolkit) 
```

您可能需要再运行一次 npm install，以便 CRA 在您的`package.json`中选取其他@types 声明。

# **配置商店**

Redux 基于单个存储的概念，也就是说，您的整个状态都存储在一个地方。

在`src/index.js`内部，我们将创建我们的商店。

现在，有几件事需要注意:

我们使用 RTK 的`configureStore`方法来创建商店。该方法将一个包含所有 reducers 的对象作为参数。

我们正在使用我们的`photoSliceReducer`，它是从一个`features/photos/PhotoSlice`文件(尚未创建)中导出的。

RTK 文档鼓励我们将所有状态逻辑写在一个名为“SomethingSlice.ts”的 features 文件夹中。

将我们所有的状态逻辑放在一个文件中使我们易于维护我们的应用程序并提高可读性。

注意:你可以传递任意多的减速器到商店。

# 设置切片

在`src`目录中，创建一个名为`features/photo`的新文件夹。在这里面，创建一个`PhotoSlice.ts`文件。

这就是奇迹发生的地方。所有这些神奇之处只依赖于一次进口。

首先，我们导入`createSlice`，这是一个带有一个参数的函数:一个带有片名、初始状态和所有 reducer 函数的对象。

我们还将导入`PayloadAction`来帮助我们定义有效载荷的类型。这样，我们可以确保我们的有效负载总是接收正确的类型。

由于使用了`createSlice`方法，我们导出了在`photoSlice`中自动可用的 reducer 对象。这是我们之前使用`configureStore`创建商店时传递给商店的减速器。

RTK 还自动为我们生成动作，我们可以在应用程序中使用的`photoSlice.actions`对象之外析构它们。

`photoSelector`将允许我们从 Redux 商店中选择我们想要的任何状态。我们稍后将回到`photoSelector`。

# 异步操作

接下来，我们需要向 NASA APoD API 发出请求，以获取照片供我们显示。我们将使用 [axios](https://github.com/axios/axios) 来完成这项工作。

记住，RTK 提供了 Redux Thunk 来允许我们编写异步代码。

我们已经在我们的`PhotoSlice.ts`文件中导入了 axios，但是我们需要安装它。使用以下内容安装 axios:

```
 npm install axios 
```

在导出`photosSelector`的那一行下面，我们可以开始编写一些异步动作。

# 将组件连接到 Redux 存储

让我们将我们的`App`组件连接到 Redux 商店。

为了将`App`(或任何其他组件)连接到商店，我们不需要任何`connect`功能、HOCs、`mapStateToProps`或任何旧的东西。辞旧迎新，记得吗？

我们需要做的就是导入`useSelector`和`useDispatch`钩子，然后我们就可以开始了。

为了从状态中选择我们想要的任何元素，我们将状态(导出为`photoSelector`)传递给我们的`useSelector`钩子。

我们还可以访问从`PhotosSlice.ts`文件中导出的 thunk 操作(`getPhotos`)。

只需几行代码，我们就可以将我们的`App`组件连接到 Redux 商店。这难道不令人惊讶吗？还要注意阅读代码是多么容易？漂亮！