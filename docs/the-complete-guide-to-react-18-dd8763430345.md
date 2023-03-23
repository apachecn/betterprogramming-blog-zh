# React 18 完全指南

> 原文：<https://betterprogramming.pub/the-complete-guide-to-react-18-dd8763430345>

## React 新特性的最终文档

![](img/5ea39f0ce61526328126a29d8b1e3186.png)

[Aaron Burden](https://unsplash.com/@aaronburden?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

*更新:2022 年 3 月 29 日，React 18 发布。自从我们写了基于 React 18 Alpha 版本的文章以来，已经过去了 9 个月。本学习指南在正式版本的基础上进行了全面更新。*

React 18 可以用以下命令安装:

```
npm i react@latest react-dom@latest
```

React 版本在`package.json`的`[dependencies](/package-jsons-dependencies-in-depth-a1f0637a3129)`中更新:

如果你安装一个全新的 [Create React App](/an-in-depth-guide-for-create-react-app-5-cra-5-b94b03c233f2) ，React 版本会自动指向 React 18，虽然不包括`createRoot`的改动。

```
npx create-react-app react-release-18
cd react-release-18
```

React 18 是一个主要版本，它在客户端和服务器端都带来了变化。它提高了内存使用率，并放弃了对 Internet Explorer 的支持。

React 18 具有以下新功能:

*   用`createRoot`替换渲染。
*   给`StrictMode`添加严格的特效。
*   更少渲染的自动批处理。
*   `startTransition`保持用户界面的响应性。
*   `useDeferredValue`生成延期值。
*   并行的`Suspense`改进。
*   更新了服务器渲染 API。
*   减少对警告的反应。

# 用 createRoot 替换 Render

React 18 提供了两个根 API:遗留根 API 和新根 API。

*   遗留根 API——这是用`render`调用的现有 API，从`'react-dom'`导入。这将创建一个在传统模式下运行的根目录。它的工作方式与 React 17 完全相同。

*   新的根 API——新的根 API 用从`'react-dom/client'`导入的`createRoot`调用。这个根增加了 React 18 的所有改进，并支持并发特性。这应该是向前发展的根 API。

在第 4 行，`root`是一个指向顶级数据结构的指针，React 使用它来跟踪要呈现的树。在 React 18 中，它可以随时连接到不同的组件(第 3 行)。

对于新的根，建议使用`root.unmount()`，而不是`unmountComponentAtNode(container)`。

由于两个原因，`render`和`createRoot`都在 React 18 中提供:

*   平稳升级—允许用户花时间升级。
*   实验—允许用户比较旧根和新根之间的差异。

类似于`render`被`createRoot`取代，`hydrate`被`hydrateRoot`取代，也是从`react-dom/client`输出。

# 向 StrictMode 添加严格效果

`StrictMode`是一个突出应用程序中潜在问题的工具。`StrictMode`不呈现任何可见的用户界面。它会为其子代激活额外的检查和警告。

随着 React 18 的发布，`StrictMode`获得了一个额外的行为，称为严格效果模式。当启用了严格效果时，React 故意为开发模式中新挂载的组件*双击效果(`mount` - > `unmount` - > `mount`)。*

为什么 React 增加了严格效果？

有一些特征要求部件被弹性地安装和拆卸不止一次。

第一个这样的特性是快速刷新，它在 [Create React App](https://medium.com/p/b94b03c233f2) 中。它通过在重新加载之间准确地保留组件状态来提供良好的开发体验。

`StrictMode`中的特效不能关闭。如果有问题，移除`<StrictMode>`直到问题解决。

# 较少渲染的自动批处理

批处理是将多个状态更新组合到一个重新渲染中以获得更好的性能。

在第 8 行，`count`状态被更新。在第 9 行，`flag`状态被更新。出于性能原因，第 8 行不触发重新渲染。因为第 9 行是最后一次状态更新，所以第 8 行和第 9 行同时被重新渲染。此行为节省了一轮渲染，从视觉上看，它同时更改了计数值和颜色。

React 17 和早期版本为事件处理程序正确地做到了这一点。但是，如果更新在 promises、`setTimeout`、本地事件处理程序(例如`addEventListener`)或任何其他事件中，默认情况下它们不会被批处理。

从 React 18 开始，如果`rootElement`被`createRoot`调用，所有的更新都会自动批处理。如果`rootElement`被`render`调用，旧的行为将被保留。

如果使用了`createRoot`，并且需要立即改变状态，`ReactDOM.flushSync()`提供了退出批处理的功能:

# 开始转换保持用户界面响应

你能说出下面这段代码的意图吗？

第 2 行在输入字段中设置一个新的输入值。第 6 行设置显示更新的查询 UI 的搜索查询状态的输入值。

如果`setSearchQuery`需要时间渲染，可能会延迟整个 UI 的更新，导致屏幕死机。为了让用户感觉到 UI 是响应性的，我们将第 6 行放入超时回调或者使用常见的变体，比如节流和去抖动。然后，击键立即显示在屏幕上，随后发生随后的屏幕重新呈现。

React 18 提供了一个 API`startTransition`来执行这种功能。我们可以使用 API 将非紧急更新标记为转换，以保持浏览器的响应，而转换发生在之后。

状态更新分为两类:

*   紧急更新——它们反映了直接的交互，比如打字、点击、按压、拖动等。
*   转换更新—它们将用户界面从一个视图转换到另一个视图。

有了`startTransition`，我们不再需要`setTimeout`黑客。

`startTransition`本身立即被调用。执行代码的时间由 React 决定，与任意延迟相比，这是一个改进。

还有一个`useTransition`挂钩提供`isPending`和`startTransition`。当转换开始时，`isPending`转动`true`。当转换完成时，`isPending`转动`false`。`isPending`可用于在 UI 上显示过渡，如微调器。

# useDeferredValue 生成延迟值

`useDeferredValue`是一个钩子，它接受一个值并返回该值的一个新副本，该副本将服从更紧急的更新。在紧急更新完成之前，会保留先前的值。然后，呈现新值。这个钩子类似于[使用去抖动或节流来延迟更新](/lodash-create-react-apps-built-in-library-for-debounce-and-throttle-with-hooks-3418087f44d8)。

第 4 行创建`text`。第 5 行基于`text`创建了`deferredText`。第 11 行是对输入字段的紧急更新。第 13 行将延迟值用于非紧急更新。

`useDeferredValue`可与`startTransition`和`useTransition`配合使用。

除了`useDeferredValue`和`useTransition`，React 18 中还有其他新的钩子:`useId`、`useSyncExternalStore`、`useInsertionEffect`。详细描述见[5 React 18](/5-new-hooks-in-react-18-300aa713cefe)中的新挂钩。

# 并发暂记变化

`Suspense`让组件在渲染之前等待。它可能是一个延迟加载的组件，或者正在等待一个异步 API 调用来获取一些数据。

在 React 18 中，`Suspense`的工作方式与之前的版本略有不同。React 18 中的新行为叫做并发`Suspense`。

下面是典型的`Suspense`代码:

2 号线是悬挂部件。第 3 行是正常组件。

`ComponentThatSuspends`对于遗留`Suspense`和并发`Suspense`的工作方式相同。React 显示`Loading`组件(第 1 行),直到`ComponentThatSuspends`中的数据解析。

对于`Sibling`组件，它的工作方式不同。在传统版本`Suspense`中，它会被立即挂载到 DOM 中，并且它的效果/生命周期会被触发。然后组件被 React 隐藏。在并发的`Suspense`中，它不会被挂载到 DOM 中，它的效果/生命周期也不会被触发，直到`ComponentThatSuspends`解决。

Concurrent `Suspense`等待`Suspense`边界内的一切(挂起的组件及其所有兄弟组件),直到挂起的数据被解析。然后整个树被挂载，并且它的效果/生命周期在一个单一的、一致的批次中同时启动。

这种新的行为降低了实现的复杂性，并产生了更可预测的行为。对于副作用和突变来说，这是一个小的突破性变化。

并发的`Suspense`也会改变从父节点传入的`[refs](/most-things-you-want-to-know-about-react-refs-29901ebf28c6)`的时间。

在传统的`Suspense`中，`refPassedFromParent.current`在`ComponentThatSuspends`解析之前的初始渲染中直接指向一个 DOM 节点。

在并发`Suspense`中，`refPassedFromParent.current`为`null`，直到`ComponentThatSuspends`解决并且`Suspense`边界未被阻塞。

并发`Suspense`由正在使用的`createRoot`启用。

# 更新的服务器渲染 API

React 18 改版服务器端 API，放入`react-dom/server`。这些 API 完全支持服务器上的`Suspense`和流式 SSR。

*   `renderToNodeStream`被*弃用*。
*   `renderToString`将一个 React 元素呈现给它的初始 HTML，但是它对`Suspense`的支持*有限。*
*   `renderToStaticMarkup`类似于`renderToString`，除了它不会创建额外的 DOM 属性。它对`Suspense`有*有限的*支持。
*   `renderToStaticNodeStream`类似于`renderToNodeStream`，除了它不会创建额外的 DOM 属性。它继续用于呈现电子邮件。
*   `renderToPipeableStream`节点环境中的流。React 18.0.0 中还没有这个新的 API。
*   `renderToReadableStream`是针对现代的 edge 运行时环境，比如 Deno 和 Cloudflare workers。React 18.0.0 中还没有这个新的 API。

# 较少反应警告

对于那些不想要警告/错误消息的人，React 18 删除了一些烦人的输出:

*   React 不再警告组件返回`undefined`。
*   React 在卸载的组件上调用`setState`时不再警告内存泄漏。
*   React 不再记录端到端测试的`act`警告。
*   React 18 不再记录严格模式的第二次渲染。
*   `renderToString`和`renderToStaticMarkup`不再出现服务器暂停时的错误。

# 结论

[React 18 现正式发布](https://reactjs.org/blog/2022/03/29/react-v18.html)。您可以升级项目，以利用新功能获得更好的性能和更可靠的行为。

感谢阅读。我希望这有所帮助。如果你有兴趣，可以看看[我的其他媒体文章](https://jenniferfubook.medium.com/jennifer-fus-web-development-publications-1a887e4454af)。