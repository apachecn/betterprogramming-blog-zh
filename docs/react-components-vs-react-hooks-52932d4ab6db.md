# React 组件与 React 挂钩

> 原文：<https://betterprogramming.pub/react-components-vs-react-hooks-52932d4ab6db>

## components 类和 React 挂钩之间的区别列表

![](img/961286e888f682072de57bfe4e4d3f3f.png)

在过去的几个月里，我一直在钻研 React hooks，这个新功能一经推出就引起了广泛的关注，直到现在我还没有花过多的时间来研究它。

如果你在最近几个月被冷冻过，你可以在下面看到它们首次出现的对话:

# 到目前为止我尝试了什么？

为了开始习惯使用React 钩子，我定义了一个具有以下特征的项目:

*   脸书登录
*   对外部 API 的调用(特别是 https://reqres.in/api 的，如果你想弄乱 API 调用，这是一个真正的发现)
*   通过 Redux 进行状态管理
*   实体创建和编辑表单
*   通过 React 路由器和专用路由保护的应用程序路由
*   样式组件的使用

我用两种不同的方式发展它:

*   使用组件——即旧方法
*   使用钩子——我发现在有趣的地方使用它们，用函数替换所有的类，利用`useEffect`和`useState`钩子

这两种开发还有另一个不同之处:对于第一种开发，我以最纯粹的老派风格配置 webpack。但对于第二种方式，我使用了 create react app 工具，以查看这两种方式中哪一种我感觉更舒服。(我最终选择了第二个选项，原因很简单，它允许我们忘记所有的初始配置和依赖项的安装。)

此外，我已经通过 TypeScript 编写了第二个项目，以便了解它是如何用钩子承载的。我尝试将它作为流和 PropTypes 的替代。

总的来说，迁移很简单，尽管在输入时遇到了一些其他问题——正如所料。然而，它提供的错误预防使得这个工具对于相对较大的项目是有价值的。

现在，我将写一份使用 React hooks 的第一次严肃体验的简要总结，列出我最喜欢的方面和那些比较复杂的方面。

如果你喜欢看项目，你可以去文章的结尾，我给你留了两个到仓库的链接，这样你就可以看到完整的代码，从而直接看到不同之处。

# 我最喜欢 React Hooks 的地方是

![](img/0650f4b9238b1c28fd215139aa85a158.png)

照片由 [Aaron Burden](https://unsplash.com/@aaronburden?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 清除器代码

在我看来，当你开始用基于钩子的功能组件替换类时，最引人注目的是代码看起来是多么的整洁和有组织。

如上面的演示视频所示，行开始自动下降，功能被分组并按顺序排列，这使我们不必在不同部分之间跳转文件。

事实上，这也是 React 团队开发钩子的原因之一:

[](https://reactjs.org/docs/hooks-intro.html#complex-components-become-hard-to-understand) [## 介绍挂钩—反应

### 钩子是 React 16.8 中的新增功能。它们允许您使用状态和其他 React 特性，而无需编写类。这个…

reactjs.org](https://reactjs.org/docs/hooks-intro.html#complex-components-become-hard-to-understand) 

## 再用

不知道是不是因为开始用 hooks 实现项目的时候特别有动力，总体感觉是他们非常青睐代码重用。

例如，在开发用户创建和编辑部分时，想到了利用`useReducer`钩子来维护表单状态的各种方法。你可以看到其中一个是在自定义钩子`useApiCall`中实现的，它负责管理调用外部 API 的整个过程。

事实上，我开始重新思考使用 [Redux Thunk](https://github.com/reduxjs/redux-thunk) 调用 API 的必要性。现在，我更多地沿着其他开发人员提出的路线，在组件中进行调用，并通过 Redux 将结果传递给动作。

无论如何，我的感觉是这个新功能打开了一个无限可能的世界。

## 使用效果

对我来说，这是最强大和最有用的挂钩之一，因为它简化了我们的代码。

我们可以说`useEffect`是一个集合了以下类生命周期事件的一体化系统:`componentDidMount`、`componentDidUnmount`和`componentDidUpdate`。

通过这种方式，多亏了`useEffect`，我们可以将过去在这些事件中复制的所有逻辑集中到一个方法中。我们可以这样做，因为我们传递的方法将总是在组件的每次渲染后执行。

我建议您详细阅读文档以深入了解其特征，因为除了其他方面，我们还可以:

*   根据某些道具是否已经改变来决定是否要执行该效果
*   在执行效果之前，执行一个函数作为`cleanup`
*   仅在安装和卸载组件时执行该效果

## 这

钩子的集成和向功能组件的过渡带来的一个结果是避免了与变量`this`的斗争，这是许多开发人员，尤其是最初级的开发人员会喜欢的。

的确，随着[巴别塔插件](https://babeljs.io/docs/en/babel-plugin-proposal-class-properties)的到来，这个问题减少了很多，但是在我们开发的时候，不必担心它的价值是非常令人欣慰的(虽然，在我看来，在舒适中获胜不应该阻止学习它如何工作以及它的预期价值是什么)。

## 大量的例子

社区似乎非常鼓励发布钩子，并且已经有一些页面收集了最有趣的目录，例如:

[](https://usehooks.com/) [## 使用挂钩

### 我们给你带来了简单易懂的 React 钩子代码，这样你就可以了解 React 钩子是如何工作的，并且感觉更舒服…

usehooks.com](https://usehooks.com/) 

我建议您看一看，因为它可以让您了解其他开发人员是如何使用它们的——这总是很有用的。

# 我最不喜欢 React 钩子的地方

![](img/6ce53b67a9e8356274cd1f72531cbc1c.png)

约瑟夫·格鲁恩塔尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## 学习曲线

现在让我们转到消极的方面，在我看来，我们获得的好处超过了消极的方面。但还是有必要提一下。

首先，从用类组件编程跳到用钩子编程是一种范式转变。一开始，我们会先担心一些显而易见的事情，比如:

*   用`useEffect`替换生命周期事件
*   用`useState`钩子替换状态对象

然而，我怀疑这更进一步，这种变化的背后是理解组件和使用它们的新方式，因为正如我在上一节中提到的，随着我们对它们越来越熟悉，我们将倾向于创建我们自己的定制钩子，并寻找超越简单合成变化的用途。

事实上，已经有收集社区创建的钩子的页面，React Router、Redux Forms 或 Redux 本身等大型库采用它们只是时间问题。

因此，我想说，如果我们想从钩子带来的所有积极方面获益，那么转向使用钩子将是我们必须承担的额外努力。

## 当心使用效果

我认为使用钩子时的一个问题是你必须非常小心地使用`useEffect`,以避免下面的情况导致无限循环:

```
const [item, setItem] = useState(null);useEffect(() => {
    const res = await fetch('fromSomeApi');
    const json = await res.json;
    setItem(json);
 })
```

在这种情况下，在第一次渲染之后，将执行`useEffect`函数，在该函数中，通过使用`useState`钩子启动一个遵循相同过程的新渲染来调用修改状态的`setItem`方法，最终导致无限循环。

也就是说，强制渲染到`useEffect`将导致效果被重新执行到无限远。

另一个我们需要注意的方面是依赖于`useEffect`、`useCallback`和`useMemo`钩子的步骤，因为 React 需要以下规范:我们要在一个会改变的钩子中使用的所有东西，我们必须在第二个参数(甚至是函数)中作为依赖传递。

为了帮助我们遵守这条规则，React 团队自己开发了一个`useMemo`插件来验证我们没有违反这条规则:

[](https://www.npmjs.com/package/eslint-plugin-react-hooks#installation) [## eslint-plugin-react-hooks

### React 挂钩的 ESLint 规则

www.npmjs.com](https://www.npmjs.com/package/eslint-plugin-react-hooks#installation) 

## 使用状态

最后，我还想提请注意`useState`挂钩的工作方式。第一次使用它时，您可能期望得到类似于`setState`的结果，但事实并非如此。

假设您有以下内容:

```
const [person, setPerson] = useState({name: '', email: ''});
```

当需要更新`person`对象的一个字段时，我们倾向于以与`setState`相同的方式使用`setPerson`，即:

```
setPerson({ name: 'Gerardo' });
```

希望以此作为我们的状态:

```
{ name: 'Gerardo', email: ''}
```

然而，我们将得到的是:

```
{ name: 'Gerardo' }
```

这是因为`setPerson`方法完全覆盖了对象。

因此，从 React 文档来看，如果我们的状态变得非常复杂，我们建议使用`useReducer`钩子(尽管已经有第三方钩子允许我们以与使用`setState`相同的方式管理状态)。

# 仓库

正如我所说的，我将留给您两个存储库，以便您可以下载它们并更仔细地查看它们的差异。

如果您时间紧迫，我建议您特别关注我开发的定制钩子`useApiCall`，以及它简化 API 调用和通过`useReducer`管理调用状态的方式。

我希望它对你有用。

[](https://github.com/ger86/learning-react-components) [## ger 86/学习-反应-组件

### 对组件进行反应。在 GitHub 上创建一个帐户，为 ger 86/learning-react-components 的开发做贡献。

github.com](https://github.com/ger86/learning-react-components) [](https://github.com/ger86/learning-react-hooks) [## ger 86/学习-反应-挂钩

### 在 GitHub 上创建一个帐户，为 ger86/learning-react-hooks 的开发做贡献。

github.com](https://github.com/ger86/learning-react-hooks)