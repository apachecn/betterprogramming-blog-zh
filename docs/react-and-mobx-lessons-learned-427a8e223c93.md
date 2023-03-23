# React 和 MobX —经验教训

> 原文：<https://betterprogramming.pub/react-and-mobx-lessons-learned-427a8e223c93>

## 立即开始使用 MobX 作为您的状态管理库

![](img/8d3f7ba59db70c71427e168864ae36d7.png)

格雷姆·尼科尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# **简介**

几年前，我面临着组装和开发产品前端的挑战。当我开始的时候，我被告知我应该使用 React，但是除此之外，我可以按照我想要的方式构建。

# Redux？

在我必须做出的许多其他选择中，我必须决定是否使用 [Redux](https://redux.js.org/) ，以及如何处理应用程序中的数据。我已经熟悉了 Redux 和一些 [Flux](https://en.wikipedia.org/wiki/Flux_(software)) 实现，以及它们的生态系统，但是有一件事让我有点困扰，那就是对于你遇到的每种类型的问题/情况，有越来越多的库和依赖项——而且，由于大多数在线知识都是基于最著名的样板，你最终被迫在项目中插入几个库，甚至不知道你是否真的需要它们。

# MobX

然后我开始寻找替代品，发现了 MobX。起初，我认为这是非常不同的，所以有某种学习曲线，但它会很快过去。有趣的是 MobX 如何有开发者/公司的良好支持，如何一开始不受企业利益驱动，社区又如何比较大(当然即使它在 Redux 上什么都没有)。毕竟开始使用 MobX 是非常容易的。然而，我陷入了一些我在开始时就想更好地了解的陷阱，所以我决定写一些技巧。

在解释 MobX 如何工作之前，因为我在谈论 React，所以有必要简单说一下，我也必须选择是否使用 [Create React App](https://create-react-app.dev/docs/getting-started/) 。我需要服务器端的渲染，CRA 不支持，在脸书自己对 SSR 的推荐中，有 [Next.js](https://nextjs.org/) 和 [Razzle](https://github.com/jaredpalmer/razzle) 。我真的很喜欢 Next 和整个 Zeit 生态系统，但无论如何，你最终都会陷入其中。另一方面，Razzle 在定义上非常灵活。这只是项目的基础，几乎所有的东西都是可定制的。由于当时我对我的挑战中的未来知之甚少，所以我选择了它(值得一看)。我只需要将 mobx 和 [mobx-react](https://github.com/mobxjs/mobx-react) 安装并导入到项目中，以便将 react(通过 Razzle)与 mobx 一起使用。

# **工作原理**

让我们来看看一些基本的 MobX 概念。

该库的重点是应用程序的可伸缩状态管理(像 Redux，不一定是 React)，这种可伸缩性是通过透明反应性实现的(有趣的名字是 TFRP，*透明反应功能编程*)。这意味着与应用程序状态相关的一切都必须自动(或自动逻辑地)获得。很快就会变得更清晰。

# **可观测量**

允许这种情况发生的是可观测量的使用。很简单，一个可观察对象增加了现有数据结构被某人“观察”的可能性。它类似于 Pub/Sub 或 Mediator 的模式设计，其中 A 部分要求在 B 部分发生某些事情时得到通知，但在这里，除了所有这些自动发生(没有“订阅”的必要性)之外，观察到的是其本身的值，而不是您创建的回调。

在 MobX 中使用 decorators 是可选的:这只是一种少写一点代码并保持当前结构的方法。每个装饰者都有相应的功能。要启用装饰者，你可能需要改变[通天塔设置](https://mobx.js.org/enabling-decorators.html)。

一个例子是装饰符`@observable`和`@observer`的使用:

请注意，不必写任何具体的东西，当可观察名称改变其值时，观察者将独自做出反应。即使你有很多复杂的可观察对象，MobX 内部只记录你在 render 方法中使用的内容。

很酷，对吧？非常简单明了。

没有装饰器的前一个示例:

你可以在[文档](https://mobx.js.org/enabling-decorators.html)中找到更多有或没有装饰程序的例子。

# 计算值

乍一看，使用这个功能似乎有点不必要，但它非常强大。虽然创建一个可观察对象和一个观察者非常容易，但是 MobX 提供了几个工具，让您能够非常清楚地区分数据层和接口层。计算值是从一个或多个可观察值中得出的值。

您可以用它来做很多事情，例如为 AJAX 请求组装一个对象:

这里我用了一个`observable.map`。

不要在组件或其他模块中将碎片放在一起，问问自己是否可以用计算出的值做到这一点。如果有，那就用吧！

# 自定义反应

不仅 React 组件能够对可观察到的内容做出反应，任何代码都可以。为此有三个功能:`autorun`、`reaction`和`when`。

考虑前面的例子。如果您真的想在更新搜索过滤器时调用 AJAX:

好吧！就这样，对吧？这至少足够让您玩一会儿，并让您的应用程序工作。

# 行动

虽然这不是强制性的，但我强烈建议使用动作。默认情况下，MobX 会完成它的工作，每个观察者都会对他们正在使用的可观察对象(只有这些)做出反应。然而，没有什么可以阻止 observables 从任何地方被改变，包括从它们自己的组件。

使用动作是完全可选的，但是除了有助于提高性能之外，它还有助于构建代码。

好吧！现在我们可以去小费。简单回顾一下:

*   对于一个或多个组件中使用的可变数据——以及导致任何 Javascript 代码频繁运行的数据——使用**可观察的**。
*   如果你需要的数据是其他数据的组合，使用**计算值**。
*   对于对可观察到的事物做出反应的反应组件，使用`observer`。
*   对于任何对可观察事件做出反应的 JavaScript 代码，使用`autorun`、`reaction`或`when`。
*   为了更好地组织您的代码并维护一个可以修改可观察性的标准，使用**动作**。

# **第一个提示:使用域存储**

按照领域(例如用户、搜索、feed 等)在商店中分离你的观察对象和与之相关的逻辑。).

要使用组件中的存储，请使用`Provider`和`inject`。

# 第二个建议:使用强制措施

使用`enforceActions`选项，MobX 将仅观察作为所声明的动作的结果而发生的可观察值的变化。

# **第三招:暴露自己的行为**

选择一种方式**，仅允许访问您的 Stores 的动作和可观察项**的读数，或者将它们导出到另一个对象，或者明确它们是公共的。某些操作将在 Store 内部执行，组件不得使用—另一方面，其他操作必须使用。有很多方法可以做到这一点，在 MobX 文档中，我们被告知对于您想要如何处理事件以及什么触发了动作，该库没有发言权。可以使用一些 Flux 实现或任何其他库来创建此通信通道。一旦用户动作发生并触发了来自商店的内部动作，MobX 就会被动地处理剩下的事情。

# **结论**

MobX 和 Redux 都是好的，都兑现了自己的承诺。尽管许多基准测试表明，在某些用例中，MobX 的性能优于 Redux，但我无法否认或确认哪一个更好。就目前而言，它们似乎只是风格不同。我认为理想的情况是:如果要使用 Redux，请了解每个库存在的原因，并尝试使用尽可能少的库，使代码尽可能直接。如果使用 MobX，请从接口中分离数据和逻辑，并确保遵循简单和冗余的模式。

如果想了解更多，以下两个环节就足够了:

*   [https://mobx.js.org/](https://mobx.js.org/)
*   [https://github.com/mobxjs/awesome-mobx](https://github.com/mobxjs/awesome-mobx)

以下是使用 MobX 的项目的一些很好的例子:

*   [https://github.com/lionsharecapital/lionshare-desktop](https://github.com/lionsharecapital/lionshare-desktop)
*   [https://github.com/fcsonline/react-transmission](https://github.com/fcsonline/react-transmission)
*   [https://github . com/gothinkster/rear-mobx-real world-example-app](https://github.com/gothinkster/react-mobx-realworld-example-app)

当然，也不是所有东西都是 Mobx，也不是 reaction。重要的是要知道如何在合适的时候使用这些工具。