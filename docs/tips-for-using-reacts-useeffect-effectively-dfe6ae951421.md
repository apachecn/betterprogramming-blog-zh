# 有效使用 React 的使用效果的提示

> 原文：<https://betterprogramming.pub/tips-for-using-reacts-useeffect-effectively-dfe6ae951421>

## 依赖数组、代码优化、useCallback 等等

![](img/f18e907b4a0d2abc1995bbc736cd5870.png)

詹姆斯·哈里森在 [Unsplash](/s/photos/developer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

钩子是从一个功能组件访问 React 的状态和生命周期方法的好方法。`useEffect`钩子是一个函数(`effect`)，在渲染之后和每次 DOM 更新时运行。

在本文中，我们将讨论一些更好地使用`useEffect`钩子的技巧。

# 1.儿童特效先开火

把`useEffect`钩子想象成`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`的组合。

因此`useEffect`钩子的行为类似于类生命周期方法。需要注意的一个行为是，子回调在父回调之前被触发*。你可以在这里检查这个行为[。](https://codesandbox.io/s/autumn-monad-p3wdo?file=/src/App.js)*

假设您必须自动触发支付。这段代码写在 render 之后运行的子组件中，但是实际的细节(总金额、折扣等。)是在父组件的效果中获取的。在这种情况下，由于支付是在设置所需的详细信息之前触发的，所以不会成功。

请记住这一点，并相应地构建您的代码。

# 2.依赖数组

让我们从基础开始。`useEffect`钩子接受第二个参数，称为依赖数组，来控制回调何时触发。

## 在每次 DOM 更新时运行效果

不传入依赖数组将在每次 DOM 更新时运行回调。

## 初始渲染时运行效果

传入一个空数组只会在初始渲染后运行效果。此时，状态用其初始值更新。DOM 中的进一步更新不调用这个效果。

这类似于`componentDidMount`和`componentWillUnmount`(在`return`上)生命周期方法。

这是添加页面所需的所有侦听器和订阅的地方。

## 特定道具变化的运行效果

假设您必须根据用户对哪个产品感兴趣来获取数据(产品细节)——例如，所选产品有一个`productId`。我们需要在每次`productId`改变时运行回调——不仅仅是在初始渲染或每次 DOM 更新时。

这基本上复制了`*componentDidUpdate*`生命周期方法。

我们还可以向依赖数组传递多个值。

一个经典的反例将帮助我们更好地理解这一点:

在上面的例子中，`counter1`或`counter2`的更新会触发下面的效果。

## **传递依赖数组中的对象**

现在，如果你的回调依赖于一个对象。如果你这样做，我们的效果会成功吗？：

答案是否定的——因为对象是引用类型。依赖数组不会注意到对象属性的任何变化，因为只检查引用，而不检查里面的值。

有几种方法可以用来在对象中执行深度比较。

*   `JSON.stringify`对象:

现在，我们的效果可以检测到对象的属性何时改变并按预期运行。

*   `useRef`与 Lodash 进行比较:

您也可以使用`useRef`编写自己的自定义函数进行比较。它用于在组件的整个生命周期中在其`current`属性中保存可变值。

如果你不熟悉它，你可以阅读更多关于 useRef 的内容。

*   外部包装

如果您的对象太复杂，无法自己进行比较，您可以使用 npm 包来进行比较。一个流行的建议是`[use-deep-compare-effect](https://www.npmjs.com/package/use-deep-compare-effect)`:

只有当对象`obj`发生变化时，`useDeepCompareEffect`才会进行深度比较并运行回调。

# 3.清洁剂代码的多个`useEffect`

既然我们已经知道了依赖数组，我们可能需要分离效果，以便在组件的不同生命周期事件上运行，或者只是为了代码更简洁。

正如在前面的[演示](https://codesandbox.io/s/snowy-flower-9elps?file=/src/App.js)中已经看到的，在单个组件中编写多个`useEffect`是可能的。

# 4.处理函数中的依赖关系

假设您想将代码分解成更小的函数，并从 effect 调用它，如下所示:

这不会给我们预期的行为— `doSomething`依赖于不包含在`useEffect`数组中的`data`。对数据的任何更新都不会触发我们的回调。

这就是为什么 React 建议我们在`useEffect`中使用这个函数，因为它更容易跟踪依赖关系。

但是如果你想写可重用的函数呢？还是从一个父组件传下来？

当您想要将一个函数作为道具传递给它的子组件时，您需要将该函数添加到子组件效果中的依赖数组中。但是每当 parent 中发生变化时，就会创建这些函数的新实例，并触发我们的回调函数。这是低效的。

对于这样的场景，`useCallback` 就来救援了。

类似于`useEffect`,`[useCallback](https://reactjs.org/docs/hooks-reference.html#usecallback)`接受一个回调和一个依赖数组。它将返回一个内存化版本的回调函数，只有当任何依赖关系发生变化时，它才会改变自己的标识，从而确保我们不会在每次父对象重新呈现时都创建一个新的函数实例。

你可以在这里用上面的[做实验](https://codesandbox.io/s/currying-shape-2dv3l?file=/src/App.js)。

# 5.总是在顶层调用钩子

不能在条件、循环或嵌套函数中调用`useEffect`(或任何钩子)。

你可以阅读更多关于[钩子](https://reactjs.org/docs/hooks-rules.html)的规则。

# TL；速度三角形定位法(dead reckoning)

上面讨论的所有要点都在[这个](https://codesandbox.io/s/cool-cloud-jmlrx?file=/src/App.js)代码中。你可以拿着它到处玩。

感谢阅读。编码快乐！