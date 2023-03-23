# 从 React 到 Solid.js:平稳过渡

> 原文：<https://betterprogramming.pub/react-to-solidjs-a-smooth-transition-c30a618ba261>

## SolidJS 非常有效地做到了这一点，尤其是对于 React 开发人员

![](img/d344561ca130916fec96ae324d2e7095.png)

Web 框架来来去去是事实。虽然，大约 10 年来 Angular 和 React 一直主导着市场。但是你在面试中听到过多少次以下的问题:

你能比较 Angular 和 React 吗？两者各有什么利弊？何时使用一个而不是另一个？

但是等等…那么我们真的只有两个选择吗？一些过分务实的人可能会说这是真的。押注于大公司支持、数百万人使用的框架/库似乎是合理的。它们更可靠，我们也更有保证它们会得到长期支持。

然而，你认为高风险的赌注是什么？

我建议查看 Solid.js，这是一个开源的 JavaScript 库。这个库的提议是带来我们在 React 上发现的好东西，并改进一些其他部分，带来一个不同的算法来管理组件渲染(这是惊人的快)。观察下面 SolidJS 在性能方面与普通 Javascript 的接近程度。那绝对是惊人的！

![](img/067520c321d0885a05012db4bbb79408.png)![](img/8e97c91e6368d6e1391a76bd78afbc4e.png)

[https://krausest . github . io/js-framework-benchmark/current . html](https://krausest.github.io/js-framework-benchmark/current.html)

但是从 React 切换到 SolidJS 有多难呢？小菜一碟！在这篇文章中，我将试着展示如何快速地转移到 SolidJS！

# 基本结构和语法

实体仅支持功能组件。观察下面的例子:

还有比这更相似的反应吗？结构是一样的，并且支持 Typescript。不过我们可以注意到一个不同之处:`class`代替了`className`(它还支持[条件类](https://www.solidjs.com/tutorial/bindings_classlist))。

说到事件监听，SolidJS 非常接近 DOM 事件。我们可以通过在上添加*作为前缀来监听原始事件。看看如何在上面的代码片段中捕获`mousemove`事件。*

当更深入地探索这个库时，您会注意到一些其他的小差异，但是总的来说，我们能同意它看起来几乎和 React 一样吗？

# 状态管理

有几种方法可以管理 React 应用程序中的状态。您可以使用`useState`或`useReducer`在本地完成这项工作，或者您可以在组件树的分支中实现一个上下文。作为一种外包解决方案，您可以利用像 Redux 或 Mobx 这样的状态管理库。

我的目标不是为这些解决方案辩护，而是向您展示 SolidJS 如何实现它们。

## 在组件级别

我们从 React 中知道的`useState`变成了 SolidJS 中的`createSignal`。要访问状态的值，我们需要调用它。

也可以创建衍生信号，就像我们用`doubleCount`T13 做的那样。通过访问组件状态，通过触发依赖于它们的任何效应，导出值将携带与它们的反应性。

与 React 和我们期望的`useState`行为不同，`count`状态不仅仅是一个数字。这是一个返回一个数字的函数，通过调用这个函数，我们最终得到了这个值。

## 使用备忘录与创建备忘录

React 中的`useMemo` 就像缓存最后的结果一样简单，直到被监视的值发生变化。

有了 Solid 的`createMemo`，事情变得简单了一些。

不需要观察`count`值的变化。因为`count`实际上是一个提供订阅的函数，所以`createMemo`函数创建了一个与它的绑定。`createMemo`返回的值也是一个函数，所以当我们想要使用它的值时，我们需要调用它。

## 全球状态

有时只做地方州管理会导致混乱，对吗？为了避免过多的财产钻探，SolidJD 有一个超级简单的解决方案。由于我们正在处理信号的概念，我们可以简单地提取所有本地信号，并在一个`store.ts`文件中声明它们。

那有多好？！这有多简单？！真的，这太神奇了。你只需要声明你所在的州，导出它们，无论你在哪里使用它们，都会有人订阅！

这种方法提供了多种不同的策略。你可以创建一个全球商店，你可以创建一些部分商店，你可以组成商店。这就是自由！

# 控制流程

在我们的应用程序开发过程中，有一种结构是我们永远无法摆脱的:列表。当使用 React 时，我们通常通过简单地创建一个`map`并返回 JSX 来实现。在 React 中，我们需要担心向 key 属性传递一个惟一的值，所以 React 不必在单个元素改变时重新呈现整个列表。

SolidJs 做的有点不同，使用 [*作为*](https://www.solidjs.com/tutorial/flow_for) *标签*:

Solid 的方法的好处是我们不需要担心键和性能。列表中的索引( *i)* 也是一个*信号。*因此，列表订阅了索引，当发生变化时，Solid 确切地知道在哪里更新。

除此之外，SolidJS 还提供了另一个呈现列表的选项: [*Index*](https://www.solidjs.com/tutorial/flow_index) 标签。如果您迭代的值是原语，那么它会提高性能。

# 路线

如果你用`react-router-dom`在你的 React 应用上实现 routes，我必须说你在这里几乎没有工作要做。换成`solid-app-router`就好！(有一个小的区别:一个`Link`组件将有一个属性`href`而不是`to`)

学习新的框架/库或技术可能具有挑战性。如果我们可以选择重复使用我们的知识，并在我们的清单上增加一项技能，那就更好了。Solid.js 非常有效地做到了这一点，尤其是对于 React 开发人员。

你很好奇为什么 SolidJS 这么快吗？它没有使用虚拟 dom，而是使用细粒度的反应式解决方案，将模板直接编译到 DOM 中。[谁能比 SolidJS 的创造者更好地解释它是如何工作的](https://indepth.dev/posts/1269/finding-fine-grained-reactive-programming)。

干杯！

```
**Additional video**: [Replacing React with SolidJS](https://www.youtube.com/watch?v=OqcHoLWyyIw).
```