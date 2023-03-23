# 给你的 React 应用插上翅膀，用定制的钩子飞翔

> 原文：<https://betterprogramming.pub/give-your-react-app-wings-to-fly-with-custom-hooks-55e1b9648a34>

## 如何写现代反应

![](img/dc0a74fae976e2a0b8d40d127eecd06a.png)

费伦茨·阿尔马西在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

您还记得 React 生态系统中的趋势是使用不同的组件来保存逻辑，使用另一个组件来呈现演示，从而分离关注点吗？

如果你还记得，这意味着你已经使用 React 很长时间了。如果你不知道我在说什么，那么我会说你没有错过太多，因为 React 已经向前迈进了一步，并抓住了一个新的氛围:hooks。

甚至在 16.8 版本引入钩子之前，React 生态系统早就倾向于功能组件方法。但是对于钩子来说，这是一场革命。大多数流行的 React 库要么不得不重写它们的代码，要么包含使用它们的钩子兼容方法。

React 钩子如此鼓舞人心，以至于 Vue.js 等其他框架也开始借鉴这个概念。

![](img/a6675a71be9be1688a4eac23f8879938.png)

照片由[格兰特杜尔](https://unsplash.com/@blizzard88?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

React 组件可能很快变得复杂，这使得测试或调试变得困难。因此，如果能够应用 Unix 哲学来促进做好一件事情的想法，那就太好了。我们可以将所有的逻辑从一个组件中分离出来，并创建一个自定义的 React 钩子来保存它，这意味着我们已经将逻辑从表示中分离出来。这使得代码重构变得非常容易。如果你需要改变你的应用程序的外观，你可以简单地在表现方面下功夫。如果你的逻辑需要改进，你不必改变你的陈述。

# 定制挂钩

定制挂钩很容易创建，因为它们看起来像是公开数据和方法供表示组件使用的常规函数。好处是它使得逻辑重用变得非常容易。

你可能会说上面的例子太简单了，我不会不同意你的观点。考虑这样一种情况，您需要从 API 获取 JSON 格式的数据，以便在应用程序的各个组件上使用。如果你做了一个可以在你的应用中轻松重用的定制钩子，这将是有意义的。看看下面的例子:

一个懒惰的`fetch`选项应该是这样的:

如果你想了解更多关于构建你自己的定制钩子的信息，请查阅官方文档。