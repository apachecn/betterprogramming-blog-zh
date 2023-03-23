# 通过记忆提升 React 应用的性能

> 原文：<https://betterprogramming.pub/boost-performance-of-your-react-app-with-memoization-e414f4f64c05>

## 关于`React.memo()`和 useMemo()钩子何时有用的全面的解释

![](img/b81f61c0843aab645ebb6bf910032742.png)

照片由 [Kushagra Kevat](https://unsplash.com/@kushagrakevat?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/boost?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

> “英国广播公司发现，他们的网站加载时间每增加一秒钟，就会多损失 10%的用户。”— [谷歌开发者](https://developers.google.com/web/fundamentals/performance/why-performance-matters)

我也不喜欢慢的网站，因为那是在浪费我的时间。当然，毫无疑问，一个网站的表现会对其受欢迎程度和收入产生影响。

如果你开始钻研你的网站的性能优化，你遇到一种叫做*记忆化*的技术只是时间问题。

在我们开始举例之前，让我解释一下什么是记忆以及什么时候应该考虑使用记忆。

# 什么是记忆化

这是一种特定形式的缓存，在这种缓存中，具有相同值的重复函数的执行速度要快得多，因为这些值存储在缓存中。

也就是说，如果我们向 memoized 函数中输入相同的值，它将从缓存中返回该值，而不是再次运行该函数。

它显著提高了性能，尤其是在使用计算量大的函数或者有大量 UI 元素的函数时。

## 这在反应方式上意味着什么？

在 React v16.8 及更高版本中，建议使用带钩子的功能组件。这意味着每个组件都是一个函数，可以被*记忆*。

使用记忆化，会使你的 React 应用程序性能更好，因为记忆化的组件只有在给定的属性改变时才会被重新渲染。

在功能组件中，我们可以使用高阶组件`React.memo()`或钩子`useMemo()`。

在基于类的组件中，使用记忆有两种选择:

*   使用生命周期挂钩`[shouldComponentUpdate()](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)`。
*   使用一个`[PureComponent](https://reactjs.org/docs/react-api.html#reactpurecomponent)`。

然而，我们将关注记忆功能组件，因为它们是 React 创建者的首选。

# 何时使用它

记忆化可以提高你的应用程序的性能，但是必须小心使用。在某些情况下，这种技术的实现可能是无用的，或者可能会产生相反的效果——对应用程序的运行产生负面影响。

这里有几个你可能会用到它的例子:

*   纯功能组件——给定相同的属性，它总是呈现相同的输出，而不会产生副作用。
*   组件经常使用相同的道具进行渲染。
*   大的或者繁重的计算功能——它包含大量的 UI 元素或者必须进行繁重的计算。

如果你不确定它是否会给你带来想要的好处，你可以使用 Chrome Performance 标签 *中的 [*评测组件来测试你的应用。*](https://reactjs.org/docs/optimizing-performance.html#profiling-components-with-the-chrome-performance-tab)*

# React.memo()

假设我们有一个简单的功能组件叫做`Employee`，它有道具`name`和`position`。我们将把这个组件包装到`React.memo()`中，所以它看起来像这样:

`React.memo(Employee)`返回新的记忆组件`MemoizedEmployee`。它将输出与原来的`Employee`组件相同的内容，但是有一点不同。

`MemoizedEmployee`渲染输出被记忆。只要`name`或`position`道具在下一次渲染中是相同的，记忆的内容就会被重用。

注意:也可以从 React 导入`memo`来提高可读性。

## 自定义检查道具的相等性

默认情况下，`React.memo()`使用浅层比较检查下一次重新渲染时所有道具是否相等。我们可以使用第二个参数来自定义应该检查哪些道具:

```
React.memo(Component, [areEqual(prevProps, nextProps)]);
```

如果`prevProps`和`nextProps`相等，则`areEqual(prevProps, nextProps)`函数必须返回`true`。

例如，让我们用一个函数来检查雇员的`name`是否等于:

```
const employeePropsNameEqual = (prevEmployee, nextEmployee) => {
  return prevEmployee.name === nextEmployee.name
}const MemoizedEmployee2 = React.memo(Employee, employeePropsNameEqual);
```

如果上一次渲染的`name`等于下一次渲染的`name`，则`employeePropsNameEqual()`函数返回`true`。这意味着只有当`name`属性改变时，组件才会被重新渲染。

# 使用备忘录挂钩

这个钩子相当于前面的技术。如果依赖关系保持不变，它会为昂贵的计算记忆并返回相同的值。该方法具有以下结构:

```
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

*   第一个参数总是一个函数(或组件)，在那里执行繁重的计算。
*   第二个参数是一组依赖项。当其中一个依赖关系改变时，`useMemo()`重新计算该值。如果没有，它返回最后一个记忆的值。

如果我们将这一知识实现到我们的`Employee`组件中，它将看起来像这样:

注意:这个钩子很像`[useCallback](https://reactjs.org/docs/hooks-reference.html#usecallback)`。不同之处在于`useCallback`返回一个内存化的回调函数，而`useMemo`返回一个内存化的值，这是函数调用的结果。

`useCallback(fn, deps)`相当于`useMemo(() => fn, deps)`。

# 包扎

记忆是提高性能的一项伟大技术。如果应用正确，它可以防止组件在下一个属性等于前一个属性时进行无用的渲染。你可以使用`React.memo()`或`useMemo()`挂钩。

但是，您应该采取预防措施并明智地实施这种技术。组件的呈现可能会影响子组件。

或者它会导致代码的可读性和理解性变差，这也可能对开发产生负面影响。

因此，一个好的开发人员应该首先考虑这样的实现，并为开发团队提供好的论据，说明为什么这样做是值得的。

# 接下来去哪里

记忆只是提高性能的众多技术之一。

如果你的应用中有很多路线，你也应该考虑实现一种叫做*代码分割*的技术。

[](https://medium.com/better-programming/routing-and-code-splitting-in-react-fc7315f0bde4) [## React 中的路由和代码拆分

### 用 React.lazy()和悬念正确实现 React 路由器

medium.com](https://medium.com/better-programming/routing-and-code-splitting-in-react-fc7315f0bde4) 

如果你想有一个快速的、非动态的网站，你应该考虑用 Gatsby 的*静态渲染*方法。

[](https://levelup.gitconnected.com/learn-gatsby-by-building-your-own-blog-website-ff5aaa8e90b8) [## 通过建立自己的博客网站来学习盖茨比

### 静态渲染方法介绍。

levelup.gitconnected.com](https://levelup.gitconnected.com/learn-gatsby-by-building-your-own-blog-website-ff5aaa8e90b8) 

感谢阅读！