# 在 React 并发模式下实现预取模式

> 原文：<https://betterprogramming.pub/implementing-the-prefetch-pattern-in-react-concurrent-mode-c10c9813e2e7>

## **渲染时提取，提取后渲染，提取时渲染**

![](img/919a0793b9afcdb8b31bd7eb91d99758.png)

[弗兰基·查马基](https://unsplash.com/@franki?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

最近，React 公布了 React 生态系统的一个功能——[并发模式](https://reactjs.org/docs/concurrent-mode-intro.html)。这将允许我们在需要的时候停止或延迟组件的执行。这将有助于 React 应用保持响应，并根据用户的设备功能和网络速度进行适当调整。

并发模式包含一系列新特性——其中最大的特性之一是*暂停*和一种新的数据获取方法。

基本上，有三种方法可以做到:

*   渲染时提取:我们开始渲染组件，这些组件中的每一个都可能在其效果和生命周期方法中触发数据提取。一个很好的例子是`useEffect`中的`fetch`。
*   获取-然后-渲染:尽可能早地开始获取下一个屏幕的所有数据。当数据准备好时，呈现新的屏幕。在数据到达之前，我们什么也做不了。例如，有一个`Container`组件，它处理数据获取，并在我们收到所有东西后有条件地呈现子表示组件。
*   获取即渲染:尽早开始获取下一个屏幕所需的所有数据，并立即开始渲染新屏幕*，甚至在我们得到网络响应*之前。当数据流进来时，React 重新尝试渲染仍然需要数据的组件，直到它们都准备好。

我相信前两种方法的概念是众所周知的，并且肯定会在您的代码中出现。让我们直接进入“随取随渲染”的方法。

# **随取随渲染**

您可能已经注意到对这种方法的解释有两个部分:

*   尽早开始加载数据。
*   开始尝试呈现可能仍然需要数据的组件。

## 早点取

我们一起搭建一个加载主要股指的 app 吧。为此，我们有一个简单的“加载”按钮。一旦你点击它，我们立即开始加载数据:

`prefetchQuery`是一个函数，它执行`fetch`请求并返回一个对象，我们将把这个对象传递给`<IndexList />`组件。这个例子的关键是我们从`onClick`事件中触发 fetch，而不是在渲染阶段。

## 带着悬念提前呈现

上面例子的第二部分是我们将对象从`prefetchQuery`保存到状态，并立即开始渲染`<IndexList />`。

另一方面，我们也不希望用空数据来呈现列表，所以理想情况下，我们希望能够暂停呈现，直到我们获得所有数据，而无需编写`if (isLoading) return null`。幸运的是，我们有`[Suspense](https://reactjs.org/docs/concurrent-mode-suspense.html)`[组件](https://reactjs.org/docs/concurrent-mode-suspense.html)正是为了这个目的。

暂停是一种机制，用于数据获取库进行通信，以对组件正在读取的数据尚未就绪做出反应。React 然后可以等待它准备好并更新 UI。

让我给你看一个例子:

为了利用悬念，你只需要用它包装你的组件。它接受一个`fallback` prop:您希望在等待数据时显示的元素。

# 如何与悬念同步取数据？

现在你知道了悬念和预取的做法，你想知道这一切是如何一起工作的。这是这个拼图的最后一块。要解决它，我们最后来看看`prefetchQuery`函数。

不要被它的复杂性吓到，它实际上相当简单。

首先，我们获取一个 URL 并将其传递给本机`fetch`函数，接收一个承诺，然后将其传递给`wrapPromise`函数。该函数使用`read()`方法返回一个对象:

*   如果一个承诺仍然悬而未决，我们抛出这个承诺。
*   如果一个承诺被错误地解决，我们抛出错误。
*   如果解决了一个承诺，只需返回数据。

事实上，与传统的获取实践相比，我们唯一的区别是抛出一个待定的承诺。

当`IndexList`中有`usePrefetchedQuery`时，它只执行`read()`方法。如果数据还没有出现，它会在实际渲染之前抛出一个承诺，而`Suspense`会捕捉到它。

# 这个怎么实验？

React 团队引入了带有现代 API 的[一个](https://reactjs.org/docs/concurrent-mode-adoption.html) `[experimental](https://reactjs.org/docs/concurrent-mode-adoption.html)` [版本](https://reactjs.org/docs/concurrent-mode-adoption.html)分支。

为此，您需要运行`npm i react@experimental react-dom@experimental`并在本地使用它。我还在 [CodeSandbox](https://codesandbox.io/s/prefetch-suspense-6q4gj) 上为你创建了一个实例，展示了我在一个工作项目中一起做的所有事情。

# 我可以在我的生产项目中使用它吗？

编号并发模式仍在开发中，一些实施细节可能会发生变化。使用实验版本来熟悉新的概念，也许可以提出你自己的想法。例如，如何在路由器中集成预取实践或提供一种缓存数据的好方法。

# 更多资源

*   [带悬念的接力实验版](https://relay.dev/docs/en/experimental/a-guided-tour-of-relay)。
*   很快，你就能把预加载功能连接到路由器上了。看看[https://github.com/ReactTraining/react-router/pull/7010](https://github.com/ReactTraining/react-router/pull/7010)或者[导航路由器](https://github.com/frontarm/navi)。
*   [并发模式介绍](https://reactjs.org/docs/concurrent-mode-intro.html)。
*   [数据提取暂停介绍](https://reactjs.org/docs/concurrent-mode-suspense.html)。
*   [带实例的真人沙盒](https://codesandbox.io/s/prefetch-suspense-6q4gj)。