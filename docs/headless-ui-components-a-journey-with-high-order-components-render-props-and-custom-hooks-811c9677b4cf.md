# 无头 UI 组件:高阶组件、渲染道具和定制钩子之旅

> 原文：<https://betterprogramming.pub/headless-ui-components-a-journey-with-high-order-components-render-props-and-custom-hooks-811c9677b4cf>

## 没有 UI 但具有 UI 功能的无头 UI 组件

![](img/99e9bd63cc49c2291ffc243c712c78ff.png)

照片由[韦斯·希克斯](https://unsplash.com/@sickhews?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/future?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我们已经介绍了[React-Table](https://medium.com/better-programming/an-introduction-to-react-table-6ebd34d8059e)——一个定制钩子，用于构建一个可扩展的表，而不需要实际的 UI 组件。这种类型的组件被称为无头 UI 组件，它将组件的逻辑和行为与其可视化表示分离开来。

无头 UI 组件经历了与高阶组件、渲染道具和定制挂钩的漫长旅程。这些概念将在本文中解释。

# 高阶组件

我们有一个组件可以打印出一个简单的 UI:

![](img/a8d791bae44d82f57bffd69cf467119d.png)

对于本文，源代码在`[CodePen](https://codepen.io/)`中得到验证:

我们想把文本变成蓝色。这可以通过将样式添加到第一行或将样式传递到第三行来完成。然而，我们希望这种风格转换逻辑也可以被其他组件重用。

高阶元件(HOC)是完成这一任务的好选择。它是一个接受一个组件并返回一个新组件的函数，带有额外的逻辑和行为。它通常定义如下:

```
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```

因此，我们在下面的代码中创建一个特设:

这个特设被命名为`withColor`(第 1-8 行)。它取`props`，如果定义了的话，就提取出`color`。否则，它使用`red`作为默认颜色(第 2 行)。`rest`道具被传递给原始组件(第 5 行)。重要的是使用组合，而不是改变原始组件。

这个 HOC 用一个`div`包装原始组件，样式为`color`(第 4 行)，在第 14 行定义为`blue`。

现在我们看到蓝色的文字:

![](img/7ad63d702e63b53a432f4682950fd43a.png)

你可能会问为什么我们在`App`之外创建`TransformedComponent`，而不是在渲染中直接改变原始组件，如下所示:

```
const App = () => {
  const TransformedComponent = withColor(BaseComponent);
  return <TransformedComponent value={1} color="blue"/>;
}
```

React 的 diffing 算法(也称为协调)使用组件引用来确定是应该更新现有的子树还是丢弃它并挂载一个新的子树。如果从 render 返回的组件严格等于来自前一次渲染的组件，React 通过将其与新的子树进行差分来递归更新子树。如果它们不相等，前一个子树将被完全卸载。

如果`TransformedComponent`是在 render 中生成的，它对每个渲染都有一个新的引用，这会导致不必要的卸载。

如果你必须把`TransformedComponent`放在渲染里面，另一种方法是应用`useMemo`来保持渲染之间的引用。

`withColor`变换颜色。如果我们也想改变字体样式为斜体呢？

![](img/ca1539edabf43f0ac125986d3f6eec1d.png)

创建另一个 HOC 来转换字体样式:

这个附加的特设被命名为`withFontStyle`(第 1-8 行)。它使用`props`并提取出`fontStyle`(如果定义了的话)。否则，它使用`normal`作为默认的字体样式(第 2 行)。`rest`道具被传递给原始组件(第 5 行)。

这个 HOC 用一个`div`包装原始组件，样式为`fontStyle`(第 4 行)，在第 24 行定义为`italic`。

这里我们在第 21 行写了两个 hoc。

如果我们想在原始组件中转换道具，比如原始值的倍数，会怎么样？

![](img/2e11db7cc98c3c4489f30067e1281323.png)

再来一杯也行。

这个新的特设被命名为`withMultipleValue`(第 1 - 9 行)。它采用`props`并提取出`factor`(如果已定义)。否则，它使用`1`作为默认因子(第 2 行)。在第 3 行计算一个新值，该值替换第 6 行的原始值。`newprops`被传递到原始组件(第 8 行)。

该 HOC 应用于第 31 行，乘以第 36 行定义的因子`5`。

我们在 31-33 行组成了三个 hoc。这些行看起来有点拥挤，但是如果你安装了`[ramda](https://github.com/ramda/ramda)` ，它们可以被格式化成这样:

```
import compose from "ramda";const TransformedComponent = compose(
  withMultipleValue,
  withFontStyle, 
  withColor,
)(BaseComponent);
```

如果使用 [Create React App](https://medium.com/better-programming/10-fun-facts-about-create-react-app-eb7124aa3785) ，`[lodash](https://medium.com/better-programming/lodash-create-react-apps-built-in-library-for-debounce-and-throttle-with-hooks-3418087f44d8)` [是内置的](https://medium.com/better-programming/lodash-create-react-apps-built-in-library-for-debounce-and-throttle-with-hooks-3418087f44d8)，第 31-33 行可以写成:

```
import flowRight from "lodash/flowRight";const TransformedComponent = flowRight(
  withMultipleValue,
  withFontStyle,
  withColor,
)(BaseComponent);
```

# 渲染道具

hoc 运行良好。为什么我们需要渲染道具？

嗯，hoc 用的是静态构图，但是渲染道具用的是动态构图。

渲染道具指的是一种使用道具在 React 组件之间共享代码的技术，该道具的值是返回 React 元素的函数。具有呈现属性的组件调用此函数，而不是实现自己的呈现。

使用渲染道具的库有 [React Router](https://reacttraining.com/react-router/web/api/Route/render-func) 、[down down](https://github.com/paypal/downshift)和 [Formik](https://github.com/jaredpalmer/formik) 。

> 据说大部分 hoc 都可以用带渲染道具的常规组件来完成。

从概念上讲，hoc 为现有组件提供了额外的逻辑和行为。渲染道具为组件提供了预期的逻辑和行为，可以应用于任何视觉表示。

让我们看看如何将 HOC、`withColor`转换为渲染道具实现:

第 1-4 行定义了采用渲染道具的`ColorComp`。在第 3 行，`color`逻辑被设置为外部的`div`，它可以应用于任何渲染的组件。

第 8-10 行部署`ColorComp`，渲染属性设置为`BaseComponent`。

第 12 行将`color`初始化为`blue`。

虽然这种模式被称为渲染道具，但并不一定要命名为渲染。任何作为渲染函数的道具在技术上都是渲染道具。

我们在第 3 行和第 9 行将`render`重命名为`test`。它的功能也和它被称为`render`一样。

如果出于某种原因你真的需要一个特设，你可以通过在渲染道具上创建一个特设来添加到上面的代码中。

第 6-8 行是渲染道具(第 7 行)之上的特设。此 HOC 用于第 12 行。它和普通的特设工作组一样有效。

我们已经尝试了各种方法来使用渲染道具实现`withColor`。把`withFontStyle`也转换成渲染道具怎么样？

下面是源代码:

第 1-4 行定义了采用渲染道具的`FontStyleComp`。在第 3 行，`fontStyle`逻辑被设置为外部的`div`，它可以应用于任何渲染的组件。

第 14-19 行部署`FontStyleComp`，渲染属性设置为`ColorComponent`，渲染属性设置为`BaseComponent`。

第 23 行将`fontStyle`初始化为`italic`。

把`withMultipleValue`也转换成渲染道具怎么样？

下面是源代码:

第 1-4 行定义了采用渲染道具的`MultipleValueComp`。在第 3 行，它提供了基于`factor`计算新值的逻辑，然后将渲染道具中的任何内容渲染为可视表示。

第 24-37 行部署了设置为`FontStyleComponent`的渲染属性`FontStyleComp`，这是一个带有渲染属性的嵌套组件。

第 41 行将`factor`初始化为`5`。

我们已经给出了 hoc 和渲染道具的例子。当应用程序有大量嵌套组件时，您会看到通常称为“包装器地狱”的问题

# 无头 UI 组件

无头用户界面组件是通过不提供界面来提供最大视觉灵活性的组件。它将组件的逻辑和行为从其可视化表示中分离出来。

一个著名的无头实现是`[<CoinFlip/>](https://www.merrickchristensen.com/articles/headless-user-interface-components/)`。它使用`children`来实现渲染道具，并强调了*Unix 哲学的基础*:

> 分离原则:政策与机制分离；将接口与引擎分开。
> 
> 埃里克·雷蒙德

## **带渲染道具的无头 UI 组件**

无头 UI 组件使得渲染道具流行起来，因为它们需要一种方法来动态地将逻辑和行为应用于任何组件。与 hoc 相比，它为大规模组件提供了更大的灵活性。

无头 UI 组件可以提供它的`children`方法和值:

`CombinedComp`在第 1-7 行创建一个无头 UI 组件，为其子组件提供三个方法:`getFactor`(第 3 行)、`getColor`(第 4 行)和`getFontStyle`(第 5 行)。

当组件在第 12 -18 行使用时，这些方法对它的`children`元素可用(第 14-16 行)。逻辑和行为被重用不是很美好吗？

## 带有自定义挂钩的无头 UI 组件

接下来是 [React 钩子](https://reactjs.org/docs/hooks-intro.html)，它创建 React 组件，而不从类组件扩展。除了内置钩子之外，还有[自定义钩子](https://medium.com/better-programming/everyone-can-build-a-custom-hook-e0f1c6802ffe)，它是围绕现有钩子的一个包装函数。这是一种重用有状态逻辑和行为的机制，这些逻辑和行为是针对每种用法而隔离的。

钩子是反应世界里的龙卷风。它们实现了函数式编程的承诺。钩子很容易编写，也很容易使用。他们结束了渲染道具造成的“包装地狱”。

然而，无头 UI 组件继续存在。在定制钩子的帮助下，无头 UI 组件变得更加流行。

以下是[牛逼 React 无头组件](https://github.com/jxom/awesome-react-headless-components)列表:

*   [React Albus](https://github.com/americanexpress/react-albus) : React 组件库，用于构建声明式多步骤流程(向导)。
*   [Dayzed](https://github.com/deseretdigital/dayzed) :构建简单、灵活、符合 WAI-ARIA 的 React 日期选择器组件的原语。
*   [降档](https://github.com/paypal/downshift):构建简单、灵活、符合 WAI-ARIA 标准的增强型输入反应组件的原语。
*   [React Ranger](https://github.com/react-tools/react-ranger):React 中构建范围和多范围滑块的无头渲染道具组件。
*   [React Selected](https://github.com/jxom/react-selected):React component 构建灵活易访问的单选按钮/元素。
*   [React T-Minus](https://github.com/jxom/react-t-minus) : React 组件处理简单的倒计时。
*   [React Toggled](https://github.com/kentcdodds/react-toggled):React component 构建简单、灵活、可访问的 toggle 组件。
*   [React Values](https://github.com/ianstormtaylor/react-values) :一组微小的、可组合的 React 组件，用于处理带有渲染道具的状态。
*   [React Table](https://github.com/tannerlinsley/react-table) :挂钩，用于搭建轻便、快速、可扩展的桌子。

我们用一个自定义挂钩重写了前面的无头 UI 组件示例:

`useCombinedComp`在第 1-10 行用三种方法创建一个无头 UI 组件:`getFactor`(第 4 行)、`getColor`(第 5 行)和`getFontStyle`(第 6 行)。

当在第 15 行使用自定义钩子时，这些方法在`App`组件中可用(第 16-20 行)。

定制钩子使得构建过程简单明了。您准备好构建自己的无头 UI 组件了吗？

# 结论

React 诞生于 2013 年 5 月。历史是一派胡言。

以下事实仍然成立:

*   大部分 hoc 可以通过渲染道具来完成。
*   hoc 使用静态合成，而渲染道具使用动态合成。
*   React 钩子是动态的，没有包装器地狱。
*   反应钩子结束渲染道具，但是 hoc 继续。
*   无头 UI 组件很神奇:它们没有 UI，但是有 UI 功能。

感谢阅读。我希望这有所帮助。你可以在这里看到我的其他媒体出版物。