# Angular 没有变化检测会更好吗？

> 原文：<https://betterprogramming.pub/is-angular-better-off-without-change-detection-401df4f42c75>

## 知道 Angular 为什么要摆脱它的变化检测策略吗

![](img/1276281bf8cb819c4525d823e23b3f97.png)

照片由[贾维尔·阿莱格·巴罗斯](https://unsplash.com/@soymeraki?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

像大多数从 C#和。NET，我最终需要编写一个响应性的前端来与我正在创建的 API 进行交互。

当时，棱角分明似乎是显而易见的选择。它是用 Typescript 写的，Typescript 是微软创建的(和 C#有些相似)。因为 Angular 对我的应用程序应该如何布局很有主见，这使得它相对容易上手。

在我选择的时候，这个空间的两个大玩家有棱角，有反应。我认为 Angular 给我的类型安全是一个优点，我也觉得 React 的 JSX 模板不是一个直观的体验。我还发现，通过使用 Angular 中的视图绑定来更新组件中的数据比使用 React 和`setState()`方法更容易。具有讽刺意味的是，在使用 Angular 几年后，事实证明这种更新视图的相对“容易”也是我认为 Angular 最大的缺点。

多年来，我已经创建了相当多的 Angular 应用程序，包括我最近为一个客户创建的一个(它与. NET API 交互)。我可以有把握地说，我对 Angular 的看法是错误的。为什么呢？

# 棱角分明怎么了？

作为开发人员，我们的应用程序必须满足各种需求。首先，它们必须是互动的。他们不能只是坐在屏幕上，在后台做所有的工作，不给用户任何视觉输出。为此，我们准备向用户展示的一些数据要么是同步可用的(我们可以检索它，无需等待，例如当前时间)，要么是异步可用的(它将在未来可用，例如 web 请求)。

我们还需要开发响应迅速的应用程序。我们不能不必要地占用我们不需要的处理器时间，以一种浪费的方式占用内存，等等。如果我们做对了，我们的用户会很高兴。如果我们做错了，我们将对用户的电池寿命和体验产生直接影响，他们*将*告诉我们他们不高兴。这样做的一个关键因素是，当您使用工具或框架来完成某件事情时，这些工具和框架会按照您期望的方式工作(以及文档中所说的方式)。他们不应该轻易做出不明确的行为。这是我对 Angular 的主要抱怨之一。

# 事情并不像你想象的那样发展

总有第一次处理异步操作，比如承诺，然后你就开始尝试使用 Observables。在这个过程中，您学习了一些关键概念，例如错误处理。一旦你知道了某样东西是如何工作的，你就可以在这个知识的基础上，通过合并可观测量，或者通过使用管道来转换它们。

因为像可观测量这样的东西已经存在了很长时间，我们依赖它们总是以同样的方式工作。当我们开始使用基于这些异步概念的东西时，也是如此，比如`Subject<T>`的。如果你将数据发送到`Subject`中，那么它将总是被发出，并且你可以预期你将总是在你的角度视图中看到新的结果值——假设你已经使用了`async`管道来注册最新发出的值。

除了，它只是在某些时候这样工作，这是问题的一个重要部分。你不能依赖 Angular 使用其内置的变化检测来检测和更新你的视图，每次都基于一个发出新值的可观察值。只有当数据的变化以 Angular 知道的方式发生时，你才能依赖它。如果它们以 Angular *没有*意识到的方式发生，或者如果它们发生在 Angular 之外，那么你的视图不会更新，你会想知道为什么。

> 你不能依赖 Angular 使用其内置的变化检测来检测和更新你的视图，每次都基于一个发出新值的可观察值。

这次经历对我来说很珍贵，因为那正是发生在我身上的事情。我有一个连接到 SignalR hub 的可观察对象(对于外行人来说，SignalR 是一个实时消息系统。NET)并向受试者发送实时结果。我不知道的是，SignalR 库使用 jQuery 接收事件，当我将事件传输到一个可观察对象中时，Angular 没有用它更新我的视图。

当这样的事情发生时，你会立刻责怪自己。我是说，这是你的错吗？或者是一个已经存在了十多年，现在已经发布了第十二个主要版本的框架的错误？这肯定是你的错。所以你搜索了又搜索，为你如何解决它找到了相互竞争的答案和解决方案，并且[发现相当多的人](https://stackoverflow.com/questions/48955143/subscribing-to-observable-not-triggering-change-detection)处于和你相同的[情况。在每种情况下，这样做的结果似乎都是一样的:](https://stackoverflow.com/questions/57916707/angular-8-ui-observable-not-triggering-change-detection-changedetectionstrateg)

1.  人们真的不知道为什么他们的观点没有被更新
2.  他们带来了对`ChangeDetectorRef`的依赖，并调用`markForCheck()`或`detectChanges()`
3.  如果问题解决了，他们就不会仔细阅读实际上是什么解决了问题，也不知道 Angular 是如何运行其变更检测周期的

在 Angular 中手动调用变化检测过程是一个可怕的命题。有时，它会解决您遇到的问题，但是因为 Angular 决定是否检测变化，所以您有机会在 Angular 运行自己的变化检测周期的同时调用`detectChanges`。如果发生这种情况，Angular 抛出一个异常，事情就停止了。

因此，Angular 的变化检测策略，即每个 Angular 应用程序中更新视图的核心内容，只在大多数时间、大多数情况下有效。这远远不够。为了让我自己对我构建的应用程序有信心，我使用的框架必须在任何情况下都能够工作。如果我将一个事件发送到一个主题中，并期望它会更新视图，那么这就是它需要做的。

> 在 Angular 中手动调用变化检测过程是一个可怕的命题。

奇怪的是，Angular 保护你免受一些陷阱的影响，比如如果一个变量在变更检测周期运行之后*被更新(至少，它在开发模式中是这样做的)。但是会很乐意让你绑定到一个有新结果的`Observable`，而不在视图中显示那些结果，甚至不需要从框架中窥视。*

# Angular 的变化检测为什么不起作用？

关于 Angular 的变化检测是如何工作的，实际上写下来的数量少得惊人。我期望 angular.io 上有一篇关于这个主题的相当长的文章，但是没有(至少在我写作的时候没有)。但我能想到的是，有角度的“猴子补丁”各种浏览器功能来实现其变化检测。因此，虽然预期的浏览器事件，如鼠标点击或 XHR 浏览器事件，确实会触发这些变化检测事件，但无论 SignalR 客户端使用什么秘方*都不会*触发这些变化检测事件。如果您将数据作为 Angular 的输入，而这样做的机制没有调用 Angular 的变化检测过程，那么您似乎有麻烦了。

> Angular 不可能一直监听所有的东西，所以有时候它会误算你 app 的状态。

举例来说，假设你有一辆汽车，你想知道汽车在任何给定时间的状态。如果你把门把手、座椅和其他部件换成直接的复制品，完成原来的功能，而且还向你的手机发送一条信息，如“车门打开”、“车门关闭”和“司机座位已被占用”，你也许可以猜出汽车在任何给定时刻的状态。但是如果司机做了什么意想不到的事情，从车窗爬出去退出呢？你可能认为汽车处于一种状态(司机在车里)，而实际上，它处于完全不同的状态(司机不在车里)。

Angular 将浏览器中的一些功能替换为它可以监听的等效功能，这使它执行其变化检测周期。但是它不能一直监听所有的东西，所以有时它会误算你的应用程序的状态。这就是问题所在，揭示了角度和变化检测的真相。

## 变化检测不是未来

你无法解释进入浏览器的数据的每一种可能的方法。您不能对所有东西都打补丁，也不能每隔一秒左右就周期性地重新运行变更检测，以确保捕获所有可能的更新。

> 我觉得解决方案是*实际上完全移除变更检测。*

也许您会认为可以有更好的方法来做到这一点，并且可以改进变更检测机制来解决此类问题。但是，变化检测也对其他丑陋负责，比如当数据绑定组件在子组件中以 Angular 没有预料到的方式发生变化时，结果是，您的视图从未稳定下来，相反，您的应用程序陷入了无限的变化检测循环。也没有发生这种情况的警告，因为 web 应用程序只是将您的 CPU 固定在 100%，并不断消耗更多的内存。

这种行为可以被改变吗，有可能被解决吗？也许吧，但这需要付出巨大的努力。相反，我觉得解决方案是*完全移除变更检测。*或者至少，给我们选择完全退出的权利。

这听起来像是异端邪说，但让我详细说明一下。我认为 Angular 处理模板的方式非常好，依赖注入工作得很好，并且它有一些真正伟大的第一方组件(比如 Angular Material)。Angular 有很多优点，并且可以继续保持这些优点。我们没有将变更检测留给框架，框架可能以次优的方式更新视图，或者根本不更新视图，而是将这一职责交给开发人员，当 UI 发生变更时，开发人员会调用这一职责。

同样，也许必须调用一个函数来让你的框架重新绘制某个组件听起来像是一个负担，但是此时，你已经从这个决定中抽离出来了。相反，Angular 掷骰子，试图确定它是否应该更新视图，但它只在大多数情况下是正确的。

有趣的是，在另一个谷歌开发的框架(Flutter)中，直接告诉框架更新当前组件(或在 Flutter 中称为`Widget`)的能力一直存在，而变化检测完全不存在。相反，您可以告诉当前页面更新，或者您可以利用状态管理框架来比较状态并为您调用`setState`方法。

因为开发人员决定视图何时更新，所以您如何引入数据并不重要。从使用普通的异步数据到编写自己的套接字实现，因为您告诉 Flutter 何时更新 UI，所以它不起作用的唯一原因是您没有正确使用状态管理解决方案。在我看来，这是一种更新视图的更好的机制，Angular 肯定可以利用这种机制。你可以通过改变角度变化检测策略，并调用`checkForChanges`来实现这一点，但仍然有风险，你会在 Angular 使用它的同时使用它，你的应用程序会崩溃。

毫无疑问，这不是我说颤动比角状更好。它们是不同的技术，实现不同的东西。我只是将 Angular 的变化检测与 Flutter 的缺少变化检测进行比较(这也与 React 有相似之处)。在我个人看来，当我想更新视图时进行选择(或者将这一职责交给状态管理框架)会更好。

## 角度 sans 变化检测

因为变化检测只是 Angular 的一个组件，如果是一个*可选的*组件就好了。如果我们可以注入类似于`RendererRef`的东西，并手动告诉 Angular 重新绘制我们的组件，我相信这可能是一些应用程序的首选结果。我认为这必须是应用程序范围内的改变，因为我们必须确保 Angular 从不调用其内置的改变检测功能，这样我们就可以在需要时(或者当状态改变时)自由地请求重绘组件。我并不是说将变更检测策略改为`OnPush`并手动调用`detectChanges`，因为要求 Angular“检查变更”与告诉 Angular 重画组件不是一回事。

如果你在想“恭喜你，你刚刚描述了 React”，我不这么认为。对我来说，Angular 的好处在于它是一个关于我们应该如何布局应用程序的非常固执己见的框架，它对类型安全语言(TypeScript)有很大的支持，并很好地将我们的应用程序分为业务逻辑、模板和样式(TypeScript)。ts，。css 和。我们为创建的每个新组件获取的 html 文件)。变化检测本身对 Angular 来说并不重要，我甚至认为它对核心框架来说是多余的。

将来我们会看到 Angular 的一个版本吗？在这个版本中，我们可以选择退出变化检测，转而手动调用组件更新？那会是我感兴趣的变化。