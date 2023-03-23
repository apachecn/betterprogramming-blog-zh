# 如何将 Redux 与 React 挂钩一起使用

> 原文：<https://betterprogramming.pub/how-to-use-redux-with-react-hooks-5422a7ceae6e>

## 写更少的代码，但是更好的代码

![](img/addcc7961c37037b6aa5140dba5f16f9.png)

React Redux 发布了 7.1.0 版本的钩子。这意味着我们可以使用 React 的最新最佳实践。

钩子让我们为同样的功能编写更少的代码。我们需要编写的代码越少，我们就能越快地让我们的应用程序开始运行。

如果你不确定钩子，看看我之前写的这个可爱的教程。我保证你会更好地理解胡克。

[](https://codeburst.io/demystifying-react-hooks-a0b56a6254c2) [## 揭秘 React 挂钩

### 你可能听说过 React 的新概念，叫做钩子。钩子在 React 版本 16.8 中发布，它们让…

codeburst.io](https://codeburst.io/demystifying-react-hooks-a0b56a6254c2) 

[成为媒介会员直接支持我的工作](https://trevorlasn.medium.com/membership)。你也可以在媒体上看到所有的故事。提前感谢！

# 简单冗余组件

这里有一个非常基本和传统的 Redux 连接组件。你如何使用钩子来重构它？

上面是一个切换复选框的简单组件。为了简单起见，我们只有一个状态，即`toggle`，这是一个布尔值。

![](img/da41c9eeb4a5a22237c130e214c214b7.png)

用 Redux 切换复选框

如果你对钩子略知一二，你可能知道钩子需要一个功能组件。React 类不能使用钩子。

## 步骤 1:将我们的类组件重构为功能组件

将我们的 React 组件从类转移到函数是相当简单的。我们需要做的是:

注意这个已经短了多少。我们将类语法替换为更简单的函数语法。我们还从箭头函数参数中解构了`ui`和`toggleSwitch`道具。

如果这对你来说是新的，我之前也写过关于这个的文章。

[](https://medium.com/@wesharehoodies/use-these-javascript-features-to-make-your-code-more-readable-ec3930827226) [## 如何使用析构和箭头函数来改进您的 JavaScript 代码

### 事不宜迟，这里有一些日常“窍门”可以让您的 Javascript 代码更具可读性。

medium.com](https://medium.com/@wesharehoodies/use-these-javascript-features-to-make-your-code-more-readable-ec3930827226) 

为了确保安全，开关仍然可以正常工作。

![](img/da41c9eeb4a5a22237c130e214c214b7.png)

钩子通常会预先固定一个关键字`use`，比如`useState`或者`useSelector`。

我们目前从存储中读取状态的方式是通过`mapStateToProps`并将我们的功能组件包装在`connect` HOC 中。

## 第二步:使用选择器

让我们从用钩子读取状态开始。我们需要从`react-redux`包中导入`useSelector`。使用`useSelector`钩子，我们可以读取我们的状态。

注意我们移除了`ui`参数，而使用了`useSelector`钩子。`useSelector`的第一个论点是商店的状态。

下面是我们的 Toggle 组件现在的样子:

## 第三步:`useDispatch`

`useDispatch` hook 让我们开始重复动作。让我们导入`useDispatch`挂钩。

使用`useDispatch`相对简单——我们将钩子实例保存在一个变量下。我们可以在任何事件侦听器上调用调度函数。

注意这一行:

我们在这里做的是用类型常量`TOGGLE`调用我们的调度函数。

我们在 Redux 常量中定义了这个类型，并将其导入到组件中。

如果你想把一个有效载荷传递给调度程序，就像我们一直做的那样。

我们的组件现在应该是这样的:

## 恭喜你！您使用钩子成功地从类重构到了

为了确保一切正常，让我们再测试一次开关。

![](img/414f7cc11e26e8134b0c10280cb44d1e.png)

是的，像预期的那样工作。

# 在这里使用源代码

[](https://codesandbox.io/s/react-redux-hook-by-indrek-lasn-utc6h) [## Indrek Lasn 的 React Redux 挂钩- CodeSandbox

### 为 web 应用程序定制的在线代码编辑器

codesandbox.io](https://codesandbox.io/s/react-redux-hook-by-indrek-lasn-utc6h) 

# 从这里去哪里？

既然您已经理解了钩子的基本知识，并在 redux 中使用钩子，我建议您通读文档，以便更深入地理解这些概念。

[](https://react-redux.js.org/api/hooks) [## 钩子反应 Redux

### React 的新“钩子”API 给功能组件提供了使用本地组件状态、执行副作用和…

react-redux.js.org](https://react-redux.js.org/api/hooks) 

另外，我建议通读“[Functional React:quick start with React Hooks，Redux 和 MobX](https://amzn.to/2T07zrK) ”一书，深入了解 Redux、React 和 MobX。

以下是我以前的一些文章，你可能会喜欢:

[](https://medium.com/@wesharehoodies/how-to-fetch-data-from-an-api-with-react-hooks-9e7202b8afcd) [## 如何用 React 钩子从 API 获取数据

### React 钩子让我们不用使用类语法就可以编写纯函数组件。通常，但不总是…

medium.com](https://medium.com/@wesharehoodies/how-to-fetch-data-from-an-api-with-react-hooks-9e7202b8afcd) [](https://medium.com/free-code-camp/the-secret-to-being-a-top-developer-is-building-things-heres-a-list-of-fun-apps-to-build-aac61ac0736c) [## 成为顶级开发人员的秘诀是构建东西！这里有一个有趣的应用程序列表！

### 只有付出努力，你才能成为伟大的开发者。想象一下——你不可能身体健康…

medium.com](https://medium.com/free-code-camp/the-secret-to-being-a-top-developer-is-building-things-heres-a-list-of-fun-apps-to-build-aac61ac0736c) [](https://medium.com/@wesharehoodies/5-react-performance-tips-f6e65295fba5) [## 这些技巧将提升 React 代码的性能

### React 之所以受欢迎，是因为 React 应用程序伸缩性好，而且使用起来很有趣。一旦您的应用扩展，您可能会…

medium.com](https://medium.com/@wesharehoodies/5-react-performance-tips-f6e65295fba5) [](https://medium.com/@wesharehoodies/how-to-setup-continuous-integration-ci-with-react-circleci-and-github-e0efd5040b03) [## 如何使用 React、CircleCI 和 GitHub 设置持续集成(CI)

### 为了确保最高等级的代码质量，我们需要对每个提交/拉取请求进行多次检查。运行代码…

medium.com](https://medium.com/@wesharehoodies/how-to-setup-continuous-integration-ci-with-react-circleci-and-github-e0efd5040b03) [](https://medium.com/@wesharehoodies/here-are-some-of-the-best-resources-to-improve-your-coding-skills-d97aa0e48fdd) [## 这里有一些提高编码技能的最佳资源

### 网上有许多高质量的编码教程、书籍和全部资源。我已经收集了最好的…

medium.com](https://medium.com/@wesharehoodies/here-are-some-of-the-best-resources-to-improve-your-coding-skills-d97aa0e48fdd) [](https://medium.com/@wesharehoodies/how-to-setup-a-powerful-api-with-graphql-koa-and-mongodb-339cfae832a1) [## 如何用 GraphQL、Koa 和 MongoDB 建立一个强大的 API

### 构建一个 API 超级好玩！尤其是当您可以利用 Koa、GraphQL 和 MongoDB 等现代技术时。

medium.com](https://medium.com/@wesharehoodies/how-to-setup-a-powerful-api-with-graphql-koa-and-mongodb-339cfae832a1)