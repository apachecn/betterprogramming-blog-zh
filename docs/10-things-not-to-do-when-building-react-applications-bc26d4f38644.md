# 构建 React 应用时不要做的 10 件事

> 原文：<https://betterprogramming.pub/10-things-not-to-do-when-building-react-applications-bc26d4f38644>

## React 应用中的最佳实践

## 根据我在生产部门的工作经验，应该避免什么

![](img/464603438c544cbfeab1c6c8e953d9dc.png)

照片由 [Alex Qian](https://www.pexels.com/photo/flat-screen-monitor-turned-on-in-office-2343475/) 从 [Pexels](https://www.pexels.com/photo/flat-screen-monitor-turned-on-in-office-2343475/)

React 是一个流行的开发工具，我相信你的 React 爱好者们会感到很幸运，能够亲手使用这样一个伟大的库。

不幸的是，生活中没有什么是完美的，React 也不例外。React 有自己的一套*陷阱*——如果您现在不处理好它们，其中一些可能会成为您的应用程序的严重问题。

下面是构建 React 应用程序时不要做的 10 件事。

# 1.花太多时间在自己的私人世界里

![](img/a7467a6623560b9e7a8b9183f1defba0.png)

照片由[布鲁诺拍摄](https://www.pexels.com/ru-ru/photo/2505333/)来自[像素](https://www.pexels.com/ru-ru/photo/2505333/)

如果你花太多时间编码，而没有花时间阅读社区中发生的事情，你就有可能遇到社区中报道的糟糕的编码实践。在你最终发现那些糟糕的编码实践代码是糟糕的之前，你可能会使用它们 20 次。

当这种情况发生时，您将不得不回头重构这 20 个代码实现，因为您发现得太晚了。当你这么做的时候，其他人都会走在你前面。当你还在追赶的时候，他们会转向更新的编码新闻。

当 React 松开[钩子](https://reactjs.org/docs/hooks-overview.html)的时候，我是如此的激动。我开始构建一些迷你项目来玩这些新玩具，每个人都被大肆宣传。在阅读了一些关于钩子将变得稳定的资料后，我开始在我的项目中更加认真地实现它们。我到处都在使用`useState`和`useEffect`*。*

*然后我发现有人链接到这条推文，这让我对`useReducer`做了更多的研究。这 30 分钟的研究足以让我回去重构大量代码。*

# *2.使用。绑定(不是类组件构造函数)*

*我想我们大多数 React 开发者都知道，如果我们想要引用`this` 来访问他们方法中自己的类实例，我们应该`.bind`我们的类方法。(除非你使用 [transpiler](https://babeljs.io/docs/en/babel-plugin-proposal-class-properties) 来传输你的类属性和方法。)*

*太棒了，我也更喜欢用*箭头函数*声明类方法。*

*但是我要说的是*内联函数。*这些是在 react 组件的 *render 方法*中定义的函数，并作为道具传递给子组件。*

*当在 render 方法中定义内联函数时，React 开始在每次组件重新呈现时指定一个新的函数实例。由于[浪费的重新渲染](https://www.freecodecamp.org/news/how-to-identify-and-resolve-wasted-renders-in-react-cc4b1e910d10/)，这已知会导致性能问题。*

*让我们来看看这个例子:*

*我们*知道*的`onClick={() => sayWhereTheMoneyIs("I don't know")}`和`onClick={() => showThemTheMoney(0.05)}`是*的内联函数*。*

*我看过一些鼓励这样做的教程(包括一个来自 Udemy 的教程):*

*这似乎缓存了引用并避免了不必要的重新渲染，因为他们没有在渲染方法*中使用箭头内嵌函数。但是他们实际上仍然在每个渲染阶段创建新的函数。**

*如果您在 React 生态系统中关注类组件的趋势，您可能已经知道这一点。*

*然而，自从 React 钩子发布后，关于`.bind` 的讨论就少了，因为类组件变得不那么流行了。当谈到`.bind`时，人们通常会讨论绑定类方法。此外，上面的例子根本没有绑定到类方法。如果你不小心的话，就更难注意到这里的后果。*

*新人尤其要注意这种反模式！*

# *3.将动态值作为键传递给子级*

*你有没有过被迫向被映射的孩子提供唯一的密钥的感觉？最好提供唯一的键:*

*现在假设`items1` 中的一些`to`值恰好与`items2`中的一些值相同。*

*履行*

*现在，如果你正在寻找一个支持多个用例的组件，我不推荐这样的解决方案。这是为了特定的用途，我们知道它将只在那个环境中被重用。支持多种用例的更动态的可重用解决方案可能会被编码得更通用。您仍然应该这样做*而不需要*改变实现:*

1.  *这样，我们现在允许开发人员根据需要随意扩展组件的任何部分，同时保留底层实现。*
2.  *6.初始化构造函数中的属性*

*在构造函数中初始化状态时:*

*你可能会遇到错误。这是因为构造函数只被调用一次，也就是组件第一次被创建的时候。*

# *下一次你试图改变`props`的时候，状态将保持它之前的值，因为构造函数在重新渲染时不会被调用。*

*如果你还没有遇到这个问题，希望这对你有所帮助！如果您想知道如何让`props` 与状态同步，更好的方法应该是这样的:*

*7.用&&进行条件渲染*

*当有条件地呈现组件时，一个常见的问题是使用 `&&`操作符。*

*如果某个条件不满足它的要求，React 将尝试将您提供的任何内容作为替代输出。因此，当我们看这个的时候:*

*当`items.length`为空时，这实际上会在屏幕上呈现一个数字 *0* 。JavaScript 认为数字 *0* 是一个[假值](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)，所以当`items`是一个空数组时，`*&&*`操作符不会计算它右边的表达式。它只会返回第一个值。*

*如果我想保留语法，我通常会使用双重否定:*

# *这样，如果`items`是一个空数组，React 将不会在屏幕上呈现任何内容，如果计算的输出是一个布尔值。*

*8.不传播以前的状态*

*偶尔会出现在我的错误列表中的东西来自于不小心实现的状态更新逻辑。*

*最近的一个情况涉及 React 钩子，特别是一个`useReducer` 实现。这是一个成为问题的基本例子:*

*当`something`函数调用并复制状态时，底层的`items`属性没有改变。当我们使用`.splice`对它进行变异时，它会变异`state.items`，并引入错误。*

*在更大的代码中，尤其会对此感到厌倦。我们都可能会忘记上面这个小例子，但是当事情变得混乱时，这个*总是*必须牢记在心。人们很容易忘记这一点，尤其是当您被迫将代码发布到产品中的时候！*

*9.没有显式地将属性传递给子组件*

*一般来说，建议您在传递给子组件的属性中要明确。*

# *这有几个很好的理由:*

*您有一个更容易的调试体验。*

*作为一名开发者，你知道每个孩子都接受了什么。*

*其他开发人员也会知道，并且会更容易阅读代码。*

*更容易理解一个组件将做什么。*

# *传递 props explicity 的另一个好处是，当你这样做的时候，它也以一种每个人都理解的方式记录了你的代码，而不需要正式的文档。这样可以节省时间！*

*为了确定组件是否应该重新渲染，需要的*道具会更少。**

*可以有一些非常简洁的用例来传播所有的道具。例如，如果在将道具传递给子组件之前，父组件很快需要一两件东西，那么他们(和您)很容易做到:*

*只要确保你不会发现自己处于这样的情况:*

*如果你这样做了，考虑将组件部分拆分成单独的组件，这样会更干净，更具可定制性。*

*10.支柱钻井*

# *将道具传递给多个子组件就是他们所说的*代码味道*。*

*如果你不知道什么是道具钻取，这意味着当一个父级将道具向下传递到树中的多个组件级别时。*

*现在，问题不在于父母和孩子。他们应该保持他们的实现不变。中间的组件可能会成为 React 应用程序中的一个问题。*

*这是因为现在中间的组件紧密耦合，暴露了太多他们不需要的信息。最糟糕的是，当父级重新渲染时，中间的组件也会重新渲染。这为链中的所有子组件产生了多米诺效应。*

*一个好的解决方案是使用[上下文](https://reactjs.org/docs/context.html)来代替。或者，`redux`代表道具(因此将被连载)。*

# *这篇文章到此结束。我希望这篇文章对你有帮助！*

*Generally, it’s recommended that you be explicit in the props you pass down to child components.*

*There are a few good reasons for this:*

1.  *You have an easier debugging experience.*
2.  *You as a developer *know* what is being passed to each child.*
3.  *Other developers will also know and will have an easier time reading the code.*
4.  *It’s easier to understand what a component will do.*
5.  *Another great thing about passing down props explicity is that when you do this, it’s also documenting your code in a way where everyone understands without needing formal documentation. And that saves time!*
6.  *There will be *fewer* props needed in order to determine if the component should re-render or not.*

*There can be some pretty neat use cases for spreading all the props. For example, if a parent quickly needed one or two things before passing the props down to child components, it can be easy for them (and you) to do so:*

*Just make sure you don’t find yourself in a situation like this:*

*And if you do, consider splitting the component parts to separate components so that it’s cleaner and more customizable.*

# *10\. Prop Drilling*

*Passing down props to multiple child components is what they call a *code smell*.*

*If you don’t know what prop drilling is, it means when a parent passes down props to multiple levels of components deep down the tree.*

*Now, the problem there isn’t the parent or the child. They should keep their implementation the same. It’s the components in the middle that might become an issue in your React apps.*

*That’s because now the components in the middle are tightly coupled and are exposed to too much information that they don’t need. The worst part is that when the parent re-renders, the components in the middle will also re-render. This creates a domino effect for all the child components down the chain.*

*A good solution is to use [context](https://reactjs.org/docs/context.html) instead. Or alternatively, `redux` for props (which consequently are going to be serialized).*

*That concludes the end of this post. I hope you found this article helpful!*