# React 18 中有什么？

> 原文：<https://betterprogramming.pub/whats-coming-in-react-18-d56832fc0ef3>

## 这可能是迄今为止用户体验的最大改进

![](img/c6f1fa08efa56809845944116de6b874.png)

Joshua Woroniecki 在 [Unsplash](https://unsplash.com/@joshua_j_woroniecki?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

React 18 带来了一些新的开箱即用的改进，更专注于用户体验和内部架构的变化。新功能如下。

# 1.新的根 API

我们知道`<App />`组件通常是如何在`index.js`文件中呈现到`index.htm` l 文件中的根元素上的。

在 React 18 之前，组件是如何在根目录中呈现的

在 React 18 中，我们需要使用`createRoot()`方法来创建一个根元素，然后呈现所需的组件，如下所示:

**React 18**中组件如何在根中渲染

用于渲染的旧方法也将得到支持，它将被称为遗留根 API。

# 2.较少渲染的自动批处理

## 什么是批处理？

[*批处理*](https://github.com/reactwg/react-18/discussions/21) 是 React 将多个状态更新分组到单个重新渲染中以获得更好的性能。当我们有大量没有批处理的应用程序时，由于多种状态变化，我们可能会遇到很多页面的重新呈现。这可能会导致一些不必要的错误，导致糟糕的用户体验。

在 React 17 中没有配料

以下示例展示了 React 18 中引入自动批处理后重新渲染的变化:

React 18 中的自动配料

*自动批处理*本质上是指在超时内状态更新的情况下，任何承诺、本地事件处理程序和任何其他事件都将被自动批处理。需要使用前面提到的新的根 API 来启用这个特性。

**批处理自动发生的例子。**

在代码片段`examples.js`中提到的所有例子中，批处理将自动发生，因此 React 将只为批处理重新渲染一次，而不是两次。

## 既然是自动的，那如果我们不想用这个功能呢？

我们可以通过使用 react-dom 的`flushSync()`函数退出批处理。

**当我们不想要批处理的时候。**

这是 React 的一个很好的新增功能，因为这可以通过避免不必要的重新渲染来提高应用程序的性能。

# 3.悬念的服务器端渲染支持

## 什么是服务器端渲染？

通常，当我们打开一个网站时，网络浏览器请求一个页面，然后 JavaScript 包被加载，然后网页就可见了。初始加载时请求的 HTML 文档是稀疏的，只包含脚本标记，然后请求 JavaScript 包。加载包，然后发出 JSON 请求，使网站可访问。

因此，在向用户显示任何内容之前，我们发出三个请求。

在服务器端渲染中，当我们打开网站时，HTML 文件被加载，用户在初始加载时得到一个可用的可视屏幕。(服务器进行渲染)。因此，我们在用户屏幕上显示 HTML 框架，同时在后台加载 JavaScript 包。

当所有这些都完成后，JavaScript 和 HTML 就连在一起了。这个过程被称为 [*水合*](https://github.com/reactwg/react-18/discussions/37) 。

这种方法的缺点如下:

*   在我们水合任何东西之前，我们需要取回所有的东西。
*   在我们与任何东西互动之前，我们必须水合所有东西。即使框架 HTML 加载并可见，组件也不是交互式的。

这在 React 18 中用`<Suspense />`组件解决。

我们可以在获取所有数据之前流式传输 HTML。这种流式传输没有特定的顺序。

假设我们有一些组件(例如，`<Comments/>`)需要很长时间来加载。这个组件可以用`<Suspense>`包裹。服务器在服务器中合成这个组件，然后将它发送给客户端。因此，其他组件(在本例中为`<NavBar />`、`<SideBar />`和`<Post />` )可以独立流式传输，而不依赖于`<Comments/>`组件。当服务器正在水合`<Comments/>`组件时，显示回退，在本例中为`<Spinner />` **，**。

这被称为*选择性水合*。其他组件渲染，React 不需要等待`<Comments/>`组件加载补水。使用`<Suspense>`，我们还可以在装载`<Comments/>`组件之前水合其他组件。

如果需要，它还允许通过排除`<Comments />`组件来捆绑 HTML。当评论的数据在服务器上准备好了，React 将发送额外的 HTML 到同一个流中，以及一个最小的 inline `<script>`标签来把 HTML 放在“正确的地方”

现在服务器中的`<Comments />`水合作用不再阻碍浏览器做其他工作。因此，其他组分(`<NavBar /`、>、`<SideBar />`和`<Post />`)将完全水合，并且这些组分将是可相互作用的。

因此，有了这个新特性，用户也可以与呈现的组件进行交互，而不是等待加载整个包。

# 4.新过渡 API

这个新特性允许我们告诉 React 哪些更新是紧急的，哪些不是，这实质上有助于区分更新的优先级。在 React 18 之前，所有的更新都被认为是紧急的。

假设我们有一个带有验证的表单。我们有两种状态，一种存储用户的表单输入，另一种存储错误状态。当我们进行大屏幕更新时，状态更新之间可能会有一些延迟。有了这个新特性，我们可以使输入的变化变得紧急，而错误状态验证变得不紧急。

围绕`startTransition()` 方法的所有更新都被视为非紧急更新，如果出现更紧急的更新，如点击或按键，就会被中断。

在 React 18 之前，上述两种状态将同时呈现，并将阻止用户看到来自他们交互的反馈，直到所有内容都呈现出来。

# 结论

React 18 带来了一些有前途的新功能。由于它仍处于 alpha 版本，脸书已经建立了一个 [React 18 工作组](https://github.com/reactwg/react-18)来为新版本的顺利和逐步采用准备生态系统。总之，我们有:

1.  新的根 API
2.  自动配料
3.  SSR 的悬念
4.  新的转换 API

## 如何尝试 React 18

您可以通过运行以下命令来安装 React 18:

```
npm install react@alpha
npm install react-dom@alpha
```

公众测试版预计将在几个月后推出。

**你觉得最刺激的特点是什么？**

感谢您的阅读！

# 参考

React 18 的计划:[https://React js . org/blog/2021/06/08/the-Plan-for-React-18 . html](https://reactjs.org/blog/2021/06/08/the-plan-for-react-18.html)

自动配料:[https://github.com/reactwg/react-18/discussions/21](https://github.com/reactwg/react-18/discussions/21)

转场:[https://github.com/reactwg/react-18/discussions/41](https://github.com/reactwg/react-18/discussions/41)

SSR 的新悬念:【https://github.com/reactwg/react-18/discussions/37】T2