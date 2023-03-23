# 为 REST APIs 投入 React 悬疑式即取即用渲染

> 原文：<https://betterprogramming.pub/diving-into-react-suspense-render-as-you-fetch-for-rest-apis-bf2d20ea7c59>

## 取代基于 useEffect 的数据提取

![](img/b99365b305bb64751a19ac7a8a19205a.png)

Lukasz Szmigiel 在 [Unsplash](https://unsplash.com/s/photos/render?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 介绍

React 释放实验通道上的并发模式和[暂停数据获取](https://reactjs.org/docs/concurrent-mode-suspense.html)。*此版本面向库作者，尚未面向生产应用。*提出的新数据提取模式被称为*即取即用*。

这篇文章主要讨论了基本 Fetch 调用的即取即用，比如调用 REST APIs。但是，有些讨论并不局限于休息。

人们可以通过简单的获取调用来调用 [GraphQL](https://graphql.org/) 端点。对于 GraphQL 更复杂的用例，也值得查看一下[中继文档](https://relay.dev/docs/en/experimental/a-guided-tour-of-relay#advanced-data-fetching)。

# 基于 useEffect 的数据提取的问题

让我们首先讨论典型解决方案的问题，即在`useEffect`中开始取数据。

## 装载指示器太多

典型的基于 useEffect 的数据提取如下:

如果我们在各种组件中使用这种模式，用户最终会在他们的屏幕上看到许多加载指示器。

我们可以通过在父组件中拥有一个加载计数器并在子组件之间共享它来解决这个问题。

`Suspense`组件是这个问题的本地解决方案。

## 获取调用运行太晚

在上面的例子中，`fetchData`在`useEffect`中运行。只有在浏览器中绘制了所有组件后，它才会运行。这可能会很晚，也可能不会很晚，取决于应用程序。

这在使用`React.lazy`时至关重要。Fetch 调用只能在组件加载后调用。

我们希望启动一个 fetch 调用，同时加载一个组件。

## 获取呼叫瀑布

由于上面描述的时序，有一个特定的行为称为“瀑布”

如果父组件处于加载状态，子组件将不会呈现，因此不会在`useEffect`中启动获取调用。只有当父组件中的获取调用完成时，子组件中的获取调用才能开始。

请参考[React 文档](https://reactjs.org/docs/concurrent-mode-suspense.html#approach-1-fetch-on-render-not-using-suspense)中的瀑布示例。

## 麻烦的 useEffect deps/useCallback

建议将`useEffect`中使用的道具放在`useEffect`第二个参数的 deps 中。出于某种原因，如果你需要提前创建一个函数，它应该由`useCallback`来包装。

典型的自定义挂钩如下所示:

这种模式对于初学者来说不是很容易。可以说`useEffect`被过度用于数据抓取，或者更准确的说，在`Suspense`落地之前，一直没有其他手段。

# 用“反应悬念”进行不同的思考

“随取随渲染”需要你以不同的方式思考。否则，很难理解新模式的库。以下是理解新格局的一些随机点。

## 不要使用效果

不要把远程数据当成道具的效果。在创建元素的同时创建它。

伪代码是这样的:

```
const fetchRemoteData = ...;
const Component = ...;const remoteData = fetchRemoteData();
<Component remoteData={remoteData} />
```

## 将远程数据作为道具传递或存储在状态中

将提取数据作为道具与其依赖的道具一起传递。

伪代码是这样的:

```
const Component = ({ useId, userData }) => {
  // userData is remote data fetched with `userId`
  // ...
};
```

或者，直接保持在状态。

## 像对待本地数据一样对待远程数据

由于悬念，渲染代码不需要关心数据是本地可用还是远程获取。你可以直接用它。

伪代码是这样的:

```
const Component = ({ localData, remoteData }) => (
  <div>
    <div>Local Name: {localData.name}</div>
    <div>Remote Name: {remoteData.name}</div>
  </div>
);
```

# 提取即渲染的用例

现在，让我们考虑一下，如果我们有一个好的库，我们如何使用“取即渲染”模式。

我们假设我们有一个允许创建可挂起结果的库，它可以像本地数据一样使用。这意味着，如果结果没有准备好，它将抛出一个承诺。

## 单次提取

最简单的例子是一个取一次的调用。

## 多重提取

如果我们需要并行运行两个 fetch 调用，我们可以同时创建它们。

至于结果是一次显示还是一个接一个显示，这完全取决于你如何在树中放置`<Suspense>`。

查看 API 文档以了解更多关于如何使用`Suspense`和`SuspenseList`的信息。

## 动态获取

数据获取并不总是静态的，我们可能需要动态地获取数据。例如，如果用户单击一个按钮来重新运行 fetch，我们需要这样一个状态。

这是回调中预取的一个例子，但是这种模式可以应用于所有非 React 回调。只是简单地把它当作把可挂起的结果输入到一个反应树中。

## 增量提取

如果两个 fetch 调用是相关的，并且我们想要向用户显示中间状态，我们需要增量加载。

它一可用就显示`Person Name`，并显示`Loading films…`直到它们准备好。

这需要一个窍门。预取中的`person => person.films`功能可以暂停，就像 React 渲染可以暂停一样。不然我们不知道什么时候开始取片。

# 代理的使用

如果我们想像对待本地数据一样对待远程数据，避免间接性是很重要的。[代理](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Proxy)允许这样的接口。

使用代理，我们可以像下面这样做:

```
console.log(result.name); // throws a promise until it's resolvedconsole.log(result.name); // works as expected after that
```

# 缓存注意事项

我们如何处理缓存很重要。我们目前的方法是不提供全局缓存。缓存是一个难题。相反，我们只是像普通数据一样存储结果。它非常直观，适用于简单的用例。

对于数据规范化的复杂缓存方法，请查看这些不同的项目。

*   [阿波罗客户端](https://www.apollographql.com/docs/react/)
*   [SWR](https://swr.now.sh/)
*   [继电器](https://relay.dev/)

# 实验项目

我们上面描述的不是梦想，我们一直在开发一些实验库。它们是正在进行的项目，将来不会反映这篇文章中所描述的内容。

## 反应-暂记-提取

[](https://github.com/dai-shi/react-suspense-fetch) [## 代时/反应-悬念-获取

### REST APIs 的即时渲染模式新的即时渲染模式令人震惊。到目前为止…

github.com](https://github.com/dai-shi/react-suspense-fetch) 

该项目提供了上述的`prefetch`。它的实现实际上与 React 无关，但它遵循了抛出承诺的约定。

请注意，API 可能会很快发生变化。

## 反应-挂钩-获取

[](https://github.com/dai-shi/react-hooks-fetch) [## 戴式/反应钩取

### 如果你正在寻找用于 Fecth API 的 React 钩子，请访问…

github.com](https://github.com/dai-shi/react-hooks-fetch) 

这个项目是为反应悬念提供挂钩。虽然不是现在，它将基于反应-暂停-获取。

API 也将很快改变。

# 结束语

提取即渲染是一种全新的模式，基于`useEffect`的数据提取将会过时。

如果很多开发者讨论这个话题，提出各种想法和用例，那就好了。