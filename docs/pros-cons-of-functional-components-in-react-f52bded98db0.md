# React 中功能组件的优缺点

> 原文：<https://betterprogramming.pub/pros-cons-of-functional-components-in-react-f52bded98db0>

## 功能组件比类组件更好实现吗？如果有，为什么？

![](img/d4eb9c1809b9e9e3e786ed3f6320f330.png)

[蒂姆·约翰逊](https://unsplash.com/@mangofantasy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

在这篇文章中，我将解释什么是功能组件，以及何时应该在应用程序中使用它们！首先，我将快速概述一下类组件。然后，我们将进入功能组件。

# 什么是类组件？

类组件有时被归类为智能组件或有状态组件，因为它们实现逻辑和状态。它们是 ES6 类，包含 React 中的`Component`类。生命周期方法可以在这些组件内部使用(`componentDidUpdate`、`componentDidMount`等)。).类组件有点复杂，因为你不仅要跟踪生命周期方法，还要跟踪它们的副作用，比如重新渲染和数据流管理。

# 功能组件

功能组件是返回 React 元素的 JavaScript(或 ES6)函数。它们更容易理解，因为不像类组件那样会发生很多事情。它们也可以写成简单的 JS 函数或使用 ES6 语法的 arrow 函数，它们的属性作为参数(如果有的话)传入。

与类组件不同，功能组件是无状态的，这意味着没有生命周期方法或状态管理。然而，React 钩子为我们提供了这样做的函数，比如`useState()` & `useEffect()`。要了解更多关于钩子的信息，请查看文档。

## 赞成的意见

*   更容易测试:您不必担心隐藏状态，而且在涉及功能组件时也不会有太多副作用，因此对于每个输入，函数都有一个输出。
*   更容易读/写:语法没有类组件复杂，而且更容易阅读，因为你已经知道你不能用功能组件做什么了。prop 析构的使用使得查看组件发生了什么和输出了什么变得非常有用。
*   更容易调试:同样，功能组件依赖于它们被赋予的道具，不会在`state`上回复。没有理由让你的`state`不断了解正在发生的变化。

## 骗局

*   重新学习新的语法:语法乍一看可能不寻常，并且很难掌握，因为类组件已经存在了很长时间。在类中，声明一个呈现函数。有了函数，你就没有了。为了在类中传递属性，您可以将它们作为类属性发送给组件，或者在组件下声明默认属性。相比之下，功能组件将道具作为参数发送。如果其他开发人员不习惯以这种方式编写他们的应用程序，理解这样的差异可能会很困难。
*   性能优化:在哪些组件对您的性能更有好处方面，实际上并没有什么不同；然而，由于功能组件不能访问像`shouldComponentUpdate`和`PureComponent`这样的方法，为了性能而优化它们可能有点不方便。

# 结论

就功能性而言，功能组件变得和类组件一样了。开发人员通常使用功能组件，如果他们只是渲染一些东西，不需要传递状态或使用生命周期方法。

虽然如前所述，钩子是在 React 版本中引入的。这允许开发人员使用生命周期方法和功能组件状态！不建议回到你的应用程序，把你的类转换成功能组件，因为状态的操作方式和生命周期的工作方式是不同的。

一定要在新的应用程序中尝试功能组件，你会惊讶地发现你能创造出什么！

# 资源

[](https://overreacted.io/how-are-function-components-different-from-classes/) [## 函数组件和类有什么不同？

### React 函数组件与 React 类有何不同？有一段时间，标准答案是类…

反应过度了](https://overreacted.io/how-are-function-components-different-from-classes/) [](https://reactjs.org/docs/hooks-intro.html) [## 介绍钩子-反应

### 钩子是 React 16.8 中的新增功能。它们允许您使用状态和其他 React 特性，而无需编写类。这个…

reactjs.org](https://reactjs.org/docs/hooks-intro.html)