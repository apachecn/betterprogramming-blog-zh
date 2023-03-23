# 如何用 JSX 创建 Web 服务器

> 原文：<https://betterprogramming.pub/how-to-create-a-web-server-with-jsx-1b3da3704502>

## 让我们把纯 JSX 和 Express 结合起来。你不会相信开发者的体验

![](img/8f0c231fb724fc8836ddc12ac4334626.png)

封面由 [**创作，作者**](https://medium.com/@candurmuss) |背景由 [**彼得·伦德布拉德**](https://dribbble.com/peterlundblad) 上[下](https://dribbble.com/)下 [**作者**](https://medium.com/@candurmuss)

2013 年，前端开发人员被这个名为 React 的新框架迷住了。因为与 jQuery 不同，它的目标不是简化 DOM 操作，而是将 DOM 引入 JavaScript。使用一种叫做 [JSX](https://reactjs.org/docs/introducing-jsx.html) (JavaScript XML)的技术，你可以用 JavaScript 编写 HTML 或者你的自定义标签。这允许开发人员以声明的方式编写客户端 JavaScript 代码。

在这个故事中，我要做的是，通过在 React 中使用相同的逻辑，我将以声明的方式编写 Express 代码。是的，我将在我的后端代码中使用 JSX，并从头开始创建一个 web 服务器。你可以在这里阅读更多关于快递[的信息。](https://expressjs.com/)

对于这个任务，我需要一个解析器，这样我就可以转换 JavaScript 的 JSX。这是神圣的巴别塔。它非常强大，几乎可以转换任何风格的 JavaScript。从打字稿到 ES6 和 JSX。

# JSX 和巴别塔是如何工作的？

React 的逻辑非常简单，这也是它如此受欢迎的原因。在 React 中，要在 DOM 中创建一个节点，必须用适当的参数反复调用`React.createElement`函数，这些参数是组件名、属性和子组件。

```
React.createElement("h1", { id: "can" },"This is a header!")
```

然而，由于每次创建一个组件时都会调用这个函数，所以 React 把它取出来，自己编写了它。

所以，如果你写下面的 JSX 代码，Babel 会把它转换成上面的 JavaScript 版本。

```
<h1 id="can">This is a header!</h1>
```

然而，巴别塔的使用并不仅限于对 JSX 做出反应。此外，它允许你根据自己的需要创建自己版本的 JSX，带有自定义的编译指示。Pragma 是 Babel 在解析 JSX 时使用的函数名。

例如，在 React 中，编译指令是`React.createElement`。通过一个特殊的注释，你可以改变这一点，从而改变 JSX 的行为。

```
/** @jsx customPragma */   => This is how to configure the pragma.function customPragma(Component, props) { ... }<Element attr="can" />
```

上面的 JSX 将使用 pragma `customPragma`转换成本地 JavaScript。

```
customPragma(Element, { attr: "can" })
```

# 整合 JSX 来表达

这部分是事情变得有趣的地方。我将创建自己的 pragma 来处理 Express 代码，并以类似 HTML 的声明方式创建我的路线。

首先，我需要创建必要的组件，

*   我们的应用程序的根。
*   `Router`:快车 app 的路由器。
*   `Get`、`Post`、`Delete`、`Put`:动作/路线方法。

这些组件将是作为第一个参数传递给 pragma 的唯一字符串，因为所有的逻辑都将封装在 pragma 中。

所以我将简单地使用数组析构将组件分配给字符串。

既然已经创建了基本组件，我就可以创建 pragma 了。Pragma 是一个普通函数，有两个显式参数`Component`和`props`。

此外，如果您从 pragma 返回某些内容，这将是其父代的子代。在 pragma 中，基于传递的组件，我会用 props 调用适当的函数。

让我们开始实现`App`组件。

在 pragma 体中，我将`arguments`对象转换为一个数组，并获取第三个元素，即 children。

然后我在 if 块的主体里启动了 app。下面，用一个 for 循环，我用从孩子那里得到的道具调用了孩子的方法，并返回了`app`。

正如我所说的，从 pragma 返回的将是其父代的子代。这意味着，要访问 for 循环中的方法(`method`、`path`、`handler`)，我必须从子体中返回它们。

因此，让我们来处理子组件。

现在，根据传递的组件，我们返回一个方法、一个处理程序和一个到父组件的路径。因此，应用程序组件现在功能齐全。

路由器的实现融合了 App 和 Action 组件。我将创建一个路由器，然后，我必须用来自其子节点的数据调用它。

除此之外，我必须返回它的`method`、`path`和`handler`，这是路由器本身，因为它将是`App`组件的子组件。

就是这样！我们为 Express 应用程序创建了 pragma。

# 用 JSX 创建 Web 服务器

现在我们已经有了 JSX 的设置，用普通的方式来说， [*REST*](https://en.wikipedia.org/wiki/Representational_state_transfer) 比创建一个 Express app 要容易得多。

我们可以像 *React 路由器*一样定义路由，而不是以命令的方式声明路由。

应用程序的根应该是`App`组件，因为我们没有从这个应用程序返回任何东西；因此，它不能是另一个组件的子组件。

同样，因为我们不处理 Action 组件(pragma)中的任何子组件，所以这些组件不能获得任何子组件。

创建适当的 pragma 后，创建 Express 应用程序就像做馅饼一样容易。您所要做的就是声明路由，类似于 React 路由器。

如果你想看完整的源代码，这里是我的 [GitHub](https://github.com/XenoverseUp/jsx-server) 。

[![](img/9617ff4f7d33119c4d4e3d20c90708a5.png)](https://mailchi.mp/a45f8fb96cc9/subscribe)

加入我的时事通讯，获取你的订户专属故事。

[](https://mailchi.mp/a45f8fb96cc9/subscribe) [## 订阅

### 嗨，我是 Can。我通常写编程、技术、设计和艺术。在这里看到你，我想你喜欢我的…

mailchi.mp](https://mailchi.mp/a45f8fb96cc9/subscribe) 

# 结论

恭喜你！现在，你知道 JSX 如何在幕后工作，并使用其机制来简化一个更复杂的任务。JSX 的使用案例不限于 React 或这个明确的示例。

在很多方面，实现 pragma 和使用 JSX 比您想象的要简单得多。如果你想到任何可以使用 JSX 简化的任务，请在评论区告诉我。