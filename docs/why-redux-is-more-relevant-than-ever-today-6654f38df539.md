# 为什么 Redux 比以往任何时候都更重要

> 原文：<https://betterprogramming.pub/why-redux-is-more-relevant-than-ever-today-6654f38df539>

## Redux 与 Context API 的渗透思考

![](img/25ccd9494cabe2070119bfb065d0734d.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[哈迪贾·赛义迪](https://unsplash.com/@hadijasaidi?utm_source=medium&utm_medium=referral)拍摄

在过去一年左右的时间里，我遇到了一些意见，表明 React 生态系统建议在他们的项目中弃用 Redux，转而使用 Context API 实现“现代 React”。在[的 React 播客](https://reactpodcast.simplecast.com/)中有几集提到了这一点(比如“你把你的 Redux 迁移到上下文中了吗？”)并且已经有[多篇文章发表](https://medium.com/@jasonleehodges/list/redux-haters-f4e857d76bed)例证了这一观点。我从训练营的毕业生那里听说，他们的导师匆匆忙忙地完成了他们课程中重复的内容，因为这些内容“太复杂了”，“反正人们也不再使用它们了。”此外，我听说这种情绪蔓延到一个全新的自学成才的开发人员，他在我们公司开始一个新项目:

> “我认为人们有点远离 Redux，而倾向于上下文…”匿名自学开发人员

我自己甚至提出，也许 Thunks，一个用于进行异步动作调用的工具，已经过时了——由于 [Redux Toolkit](https://redux-toolkit.js.org/) 和我在项目中只使用定制钩子时经历的一些不便，我不再这样认为。老实说，我认为我们都对 React 的新特性感到兴奋，并认为“这就是未来！”他们很酷，他们赋予了很多有用的东西。但摆脱 Redux 不在其中。

[](https://javascript.plainenglish.io/are-thunks-obsolete-2652681c39af) [## Thunks 过时了吗？

### 为什么 React-Redux 挂钩可以取代 React 项目中对 Thunks 的需求。

javascript.plainenglish.io](https://javascript.plainenglish.io/are-thunks-obsolete-2652681c39af) 

也许这种情绪是从少数有争议的标题中产生的，没有经过大量真正深入的分析，但 Redux 没有死。事实上，我认为它比以往任何时候都更有意义。当然，我并不是想在这个问题上多管闲事。它可能并不适用于每个项目。事实上，Redux 的创造者[丹·阿布拉莫夫](https://medium.com/u/a3a8af6addc1?source=post_page-----6654f38df539--------------------------------)早在 2016 年的一篇文章中就提出，也许你的项目不需要 Redux。但是对于我的组织来说，我发现了在可预见的未来我们将继续使用 Redux 的几个原因。希望[新的 react 开发者](https://medium.com/@jasonleehodges/list/mentoring-new-engineers-b2550ac0faab)也会考虑这些，而不是简单地附和最近围绕上下文 API 的情绪。

我的工作性质允许我构建许多复杂程度不同的全新应用程序。这让我对什么有用什么没用有了某种独特的看法。我抓住了这个机会，当时我的队列中突然出现了许多新项目，我测试了状态管理的各种实现，以帮助我的组织确定我们在哪些地方过度投资了时间，以及哪些项目对于捕获 bug 和构建可靠的架构解决方案真正有用。这些变化包括构建应用程序:

*   只有`useState`挂钩(试图以这样一种方式设计，以避免尽可能多的支柱钻孔)。
*   Redux 的一个完全普通的实现。
*   只有上下文 API。
*   Redux 工具包的一个实现。

我发现，即使在我的应该很小的应用程序中，不可避免地会出现范围蔓延和边缘情况，最终使它们变得更大、更复杂。上下文 API 的一个主要优点是它更简单，对于较小的应用程序更有意义。但有了这次经历后，我会争辩说，不存在神话中的“小应用”这种东西。

此外，我团队中第一次接触上下文的开发人员开始遇到与使用可变变量实现上下文相关的错误。重构他们的代码以使其安全地不可变，这导致了基本上重新实现 Redux API(通过 reducers 函数)，但是没有令人敬畏的 Redux Dev 工具的好处。说真的，Redux Dev 工具非常好，我在调试时从来不需要使用断点或`console.log`。为了上下文 API 的“简单性”,不值得放弃追溯每个已调度的动作、在实时应用中手动调度新动作、准确跟踪代码中动作被调度的位置以及查看每个调度的完整状态和状态差异的能力。

关于简单性——之所以说上下文 API 比 Redux 简单，主要是因为减少了样板代码。然而，一个安全实现的上下文应用程序的样板文件并不比 Redux 少多少。现在，Redux Toolkit 已经大大减少了样板文件。Redux Toolkit 是这场辩论中的一大优势。我可能在游戏后期偶然发现了它，但是它相对于 Redux 的普通实现的简单性基本上完全消除了上下文 API 的争论。

# TL；速度三角形定位法(dead reckoning)

1.  没有“小应用”这样的东西，这是上下文 API 应该发光的地方。
2.  Redux 开发工具不值得为了样板代码的少量减少而放弃。
3.  Redux Toolkit 降低了 Redux 实现的复杂性，使其与上下文 API 的复杂性相当。

# 结论

在我看来，Redux 不仅没有消亡，而且比以往任何时候都更有意义。随着 Redux Toolkit 的加入，围绕 Redux 的状态管理能力比以往任何时候都要好。Redux Toolkit 中引入的新特性，比如 [RTK Query](https://redux-toolkit.js.org/rtk-query/overview) ，看起来相当有杀伤力(我还没有尝试过，但是我真的很想尝试)。为 Redux Toolkit 做出贡献的人们做了如此出色的工作，我的组织将在可预见的未来密切关注该库，因为我们将他们视为业界的思想领袖。