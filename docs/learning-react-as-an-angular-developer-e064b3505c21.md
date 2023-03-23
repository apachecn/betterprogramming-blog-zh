# 学习反应是一个角度的发展

> 原文：<https://betterprogramming.pub/learning-react-as-an-angular-developer-e064b3505c21>

## 在两个非常不同的框架之间寻找可转移的知识

![](img/706fe2b1634d2eefaca133c788985115.png)![](img/3e64bf89a708a32ef3802465a0fe0922.png)![](img/0066ede1c64ac4af5bb94335867b1eb0.png)

角度->反应

这个博客是关于我作为一个 Angular 开发者学习 React 的旅程。我知道:React 和 Angular 是橘子和苹果。我并不是要对这两者进行一对一的比较。然而，我正试图找到一些可转移的概念来缩短我的学习曲线。

# 开始之前

## 。jsx 或者。txs？

我注意到 React 支持。jxs 和. txs .我用 typescript 有一段时间了，非常熟悉了。这似乎也是社区的发展方向。所以我用 CLI [create-react-app](https://github.com/facebook/create-react-app) 创建了我的 React 应用程序，打字稿:【https://create-react-app.dev/docs/adding-typescript】T2。

# 第一步

第一步是设置样板文件。在 Angular 世界中，Angular CLI 非常强大，它设置了您需要的大多数东西。它包括路由、单元测试和开箱即用的林挺，并为您安装大多数 NPM 库。然而，react 是非常模块化的。对于 React，用 CLI [create-react-app](https://github.com/facebook/create-react-app) 创建 app 后，我需要手动安装很多 NPM 库。

## 样板文件

我需要手动设置以下内容:

*   路由器: [react-router-dom](https://github.com/ReactTraining/react-router/tree/master/packages/react-router-dom)
*   林挺和更漂亮的:[https://dev . to/Ben weiser/how-to-set-up-eslint-typescript-beauty-with-create-react-app-3675](https://dev.to/benweiser/how-to-set-up-eslint-typescript-prettier-with-create-react-app-3675)
*   单元测试:[https://jestjs.io/docs/en/tutorial-react](https://jestjs.io/docs/en/tutorial-react)
*   组件库(可选): [material-ui](https://material-ui.com/) (类似于角度材质，但用于反应)

我还需要为所有这些 NPM 库添加@types:

```
npm install --save react-router-dom @types/react-router-dom @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint-config-prettier eslint-config-react eslint-plugin-prettier prettier @material-ui/core
```

## 国家管理图书馆

对于 Angular，我简单的安装 [NgRx](https://ngrx.io/) 进行状态管理。这个图书馆是最受欢迎的。即使 NgRx 受 Redux 启发，也不需要开发者手动安装 Redux。它为开发者提供商店、效果和路由器商店。

然而，当我试图创建一个 React 应用程序时，当然，我需要做的第一件事就是安装一个状态管理库。在 React 的世界里，似乎没有像 NgRx 这样占主导地位的状态管理库。Redux 仍然是一个受欢迎的工具——然而，我读过一个使用 [MobX](https://github.com/mobxjs/mobx) 的教程。

我选择 Redux 作为我的状态管理方法，所以我需要手动安装 NPM 库 [redux](https://github.com/reduxjs/redux) 、 [react-redux](https://github.com/reduxjs/react-redux) 和 [redux-logger](https://github.com/LogRocket/redux-logger) 以及它们的类型。

```
npm install --save redux @types/redux react-redux @types/react-redux redux-logger @types/redux-logger
```

## 异步流

现在我需要一个相当于@ngrx/effects 的库，它可以处理异步流，比如调用 API。我阅读了在线教程，其中提到了这些库:

*   [redux-saga](https://github.com/redux-saga/redux-saga)
*   [redux-thunk](https://github.com/reduxjs/redux-thunk)
*   [重复承诺](https://github.com/acdlite/redux-promise)

同事推荐 redux-saga 所以我选了那个。

现在，我的 package.json 的依赖项如下所示:

```
"@material-ui/core": "^4.6.0",
"@types/jest": "^24.0.21",
"@types/node": "^12.12.5",
"@types/react": "^16.9.11",
"@types/react-dom": "^16.9.3",
"@types/react-redux": "^7.1.5",
"@types/react-router-dom": "^5.1.2",
"@types/redux": "^3.6.0",
"@types/redux-logger": "^3.0.7",
"@typescript-eslint/eslint-plugin": "^2.6.0",
"@typescript-eslint/parser": "^2.6.0",
"eslint-config-prettier": "^6.5.0",
"eslint-config-react": "^1.1.7",
"eslint-plugin-prettier": "^3.1.1",
"prettier": "^1.18.2",
"react": "^16.11.0",
"react-dom": "^16.11.0",
"react-redux": "^7.1.1",
"react-router-dom": "^5.1.2",
"react-scripts": "3.2.0",
"redux": "^4.0.4",
"redux-logger": "^3.0.6",
"typescript": "^3.6.4"
```

# 最后开始编码

## 命名约定/风格指南

最后，我将创建我的第一个文件。但是等等，我应该遵循什么命名约定呢？

与 Angular 不同，Angular 有一个[页面](https://angular.io/guide/styleguide)关于开发者应该遵循什么样的风格指南，对于 React 来说，它似乎是“[未个性化的](https://stackoverflow.com/a/43979817/2675714)”。

有人建议遵循 Airbnb React [风格指南](https://github.com/airbnb/javascript/tree/master/react)。

它与 Angular 非常相似，除了 React 组件遵循 PascalCase，如“MenuButton”，而 Angular 组件遵循 kebab-case，如“ng-menu-button”。

## 文件结构

我把我的文件放在哪里？React 提供了一个[指南](https://reactjs.org/docs/faq-structure.html)，但是它没有告诉我把 Redux 相关的文件放在哪里。

Redux 还提供了一个[指南](https://redux.js.org/faq/code-structure)。

文件结构似乎也是“非个人化的”。我在网上看过一些博客，每一个都提供了不同的解决方案。所以我决定边走边编码:首先创建文件，然后移动它直到感觉合适为止。

# 旧的东西

在 Angular 中有相同的概念仍然在 React 中使用(注意:这不是一对一的匹配):

*   `@Input()`和`@Output()` - > `props`
*   [生命周期钩子](https://angular.io/guide/lifecycle-hooks)(例如`ngOnInit` )- > [生命周期方法](https://reactjs.org/docs/react-component.html#commonly-used-lifecycle-methods)(例如`componentDidMount`)(注意:不要把这个和 React [钩子](https://reactjs.org/docs/hooks-intro.html)混淆)
*   [懒加载](https://angular.io/guide/lazy-loading-ngmodules)->-[react . Lazy](https://angular.io/guide/lazy-loading-ngmodules)
*   `ng-container`/`ng-template`->-

当然，Redux 的模式还是一样的。

# 新的东西

很多东西都是新的，我很难理解它们。

*   [函数组件](https://reactjs.org/tutorial/tutorial.html#function-components):你可以使用一个函数来创建一个组件，但是，我还是更喜欢使用一个类。
*   [高阶分量](https://reactjs.org/docs/higher-order-components.html) (HOC): “取一个分量，返回一个新分量的函数”。用于 Redux 的[连接](https://github.com/reduxjs/react-redux/blob/master/docs/api/connect.md#connect)。因为 HOC，React 的世界里没有`ng-content`这种东西的用处。
*   `this.state`:每个 React 组件都有`this.state`来处理组件状态。但是，我们还需要有 Redux 状态吗？[是](https://spin.atomicobject.com/2017/06/07/react-state-vs-redux-state/)。

# 反应没有的东西

[依赖注入](https://angular.io/guide/dependency-injection):依赖注入(DI)是 Angular 中使用的一种设计模式。但是，react 不需要 DI。这个[块](https://medium.com/@maxheiber/no-need-for-dependency-injection-in-react-components-641182760aaa)建议使用 HOC 来实现关注点分离。

# 进一步阅读

变更检测:[https://blog . angularindepth . com/what-every-every-front-end-developer-should-know-on-change-detection-in-angular-and-react-508 f83 f58 c6a](https://blog.angularindepth.com/what-every-front-end-developer-should-know-about-change-detection-in-angular-and-react-508f83f58c6a)

形式:【https://jaredpalmer.com/formik/ 

# 摘要

Angular 和 React 是目前最流行的两个前端框架/库。然而，它们仍然只是开发人员编写干净代码、实现业务逻辑和创建响应性 UI 的工具。

我希望这篇博文能为像我一样的其他 Angular 开发人员提供一些起点，让他们尝试 React。

快乐编码，快乐学习！