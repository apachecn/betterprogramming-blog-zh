# 如何构建用于获取数据的自定义 React 挂钩

> 原文：<https://betterprogramming.pub/how-to-build-a-custom-react-hook-for-fetching-data-cf942e64e9f8>

## 一个优雅且可重复使用的解决方案，适合使用 fetchAPI 和 Axios

![](img/47f6616206ae3a07f505b670f888f17a.png)

照片由[twist](https://unsplash.com/@twixes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/future?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我相信你已经听说过 React Hooks 的炒作。没必要给你基础知识——互联网上到处都是。此外，如果您对构建定制挂钩感兴趣，您可能已经对它们有所了解。

所以让我们直接进入代码。

# 获取数据的示例代码

大多数 web 应用程序使用某种 API 来获取数据。首先，我们将创建一个示例代码。

让我们使用组件`UserList.js`，在页面被装载到视图后，我们希望获取用户。出于演示的目的，我将使用假 API [JSONplaceholder](https://jsonplaceholder.typicode.com/) 。

该组件的基本内容如下所示:

UserList.js

我们将保存状态为`users`的数据，由`useState`钩子处理。在获取过程中，我们将显示一条加载消息，以提供更好的用户体验。

# 数据提取

在 React 16.8 和更高版本中，这是通过`useEffect`钩子完成的:

*   首先，我们将加载设置为`true`。
*   用于获取数据的整个代码块一旦被定义就被调用(第 6 行和第 18 行)。这种怪癖叫做“[立即调用函数表达式](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)”(或 IIFE)。
*   我使用的是`async-await`而不是传统的基于`Promise`的方法。为了正确处理错误，我实现了 try-catch-finally 块。
*   在这种情况下，使用 Fetch API，但是您也可以使用 [Axios](https://github.com/axios/axios) 。

# 性能优化

您应该始终牢记代码的性能方面。

为了防止在卸载的组件上获取数据，我们可以使用另一个钩子`useRef`。目的是只有当组件被安装到视图中时，它才应该在`useEffect`中运行代码。否则，您可能会得到这个熟悉的(针对 React 开发人员的)警告:

> 警告:无法对已卸载的组件执行反应状态更新。这是不可行的，但它表明您的应用程序中存在内存泄漏。要修复此问题，请取消 useEffect 清理函数中的所有订阅和异步任务。

下面是`useEffect`的最终版本:

在第四行，我们首先检查组件是否已安装。如果是，我们尝试获取数据。在清理函数(第 26–28 行)中将当前参考设置为`false` 也很重要。

# 创建可重复使用的自定义挂钩

我们的`useEffect`已经长成了很多代码，这可能会污染组件的环境。如果您在项目内部的几个组件中获取数据，您也违反了 DRY(不要重复自己)原则。

## 实现 React 的内置挂钩

让我们用下面的代码创建一个新文件`useFetch.js`:

*   `useFetch`只是一个特殊类型的函数，它将包含 React 的内置钩子。
*   它接受状态的参数——URL、引用和初始值。
*   这个钩子将相应地存储数据、错误和加载状态(第 4-6 行)。

现在我们唯一缺少的是数据获取本身。我们可以在`useEffect`中这样做，如上所示:

这个自定义钩子必须返回我们定义的状态。我们可以通过在第 26 行使用对象析构来实现。

## 回到我们的组件

现在，我们准备在组件内部使用自定义挂钩。删除不必要的代码，并将钩子的结果存储在变量中:

在`useFetch()`中，我们传递获取用户的 URL，`isComponentMounted`作为引用，空数组作为数据的初始值。

现在，我们的用户将被存储在`data`中，一旦获取数据或出现错误，组件将重新呈现。您可以根据需要处理组件中的错误。

# 怎么测试？

所以您刚刚创建了一个可重用的定制钩子，您可以在所有 React 项目中使用它。但是真的靠谱吗？

为了找到它，你应该写一些单元测试。请继续阅读我关于这个主题的下一篇文章:

[](https://levelup.gitconnected.com/testing-a-custom-react-hook-21ae732228b7) [## 测试自定义 React 挂钩

### 并对您的 React 测试库技能更有信心

levelup.gitconnected.com](https://levelup.gitconnected.com/testing-a-custom-react-hook-21ae732228b7) 

感谢阅读！