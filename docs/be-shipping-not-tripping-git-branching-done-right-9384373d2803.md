# 是运输，而不是绊倒——Git 分支做得对

> 原文：<https://betterprogramming.pub/be-shipping-not-tripping-git-branching-done-right-9384373d2803>

## 基于 Git 流或主干的开发:找到适合你的策略

![](img/1723a76db9111ff229cbfaab365b01fb.png)

杰里米·珀金斯在 Unsplash 上拍摄的照片

“您不能提交给 master/main。”

“好吧，但是为什么？”

"主要是我们部署到生产中的东西."

“是的，这就是我想做的。”

“嗯，你不能，除非你已经做了…”

我第一次用 gitflow 的时候，我记得有两种感觉:“这感觉是个坏主意，”和“这感觉很专业。”

现在，我觉得这是个坏主意。不是想法本身，而是使用它的环境。

> 基于主干的开发> git 流

在解释我关于基于 tunk 的开发优于 git flow 的说法之前，我应该解释一下这两种实践，所以请耐心听我说一会儿。

# 基于主干的开发

基于主干的开发是一种分支策略，所有开发人员都致力于主干。主干是每个人都参与的单个分支。(这可能是主分支，也可能是名为 trunk 的分支)。这个想法是每个人都在同一个代码库中工作，而不是在不同的分支中工作。

# Gitflow

另一方面，Gitflow 是一种分支策略，所有工作都在不同的分支上执行。处理特征时，您是在特征分支中工作。当您发布您的代码时，您在一个发布分支中工作。当你必须修复一个 bug 时，你就在一个热修复分支中工作。当您完成您的工作时，您创建一个 pull 请求，并将您的工作合并到您的阶段的固定分支中(例如，开发环境的开发和生产的 main，等等)。)

# 有什么好吃的？

在我们说哪种策略更好之前，我们必须定义什么使得分支策略更好。我们最终想要实现什么，分支策略能有什么帮助？

任何企业都需要为客户提供价值，并根据这种价值获得报酬。

在软件开发中，当客户(或我们客户的客户)在生产中使用我们的软件时，我们就创造了价值。在那之前没有。处于预生产阶段的优秀系统不会为客户提供价值。只有当我们在生产中运行时，我们的努力才能提供任何价值。

因此，我们应该调整我们的软件交付过程来提供最好的价值。当我们的分支策略支持我们的“将代码引入生产”过程时，就创造了最佳价值。

“将代码引入生产”的过程如下所示:

```
Checkout -> build -> test -> deploy -> (test ->) (approve ->) -> deploy
```

括号中的活动是可选的，可能会有更多的阶段，但这就是流程。

根据流程，我发现很难确定首选的分支风格。然而，当我们看谁执行不同的活动时，差异就变得很明显了。

# 组织

如果你有独立的组织单位执行或负责开发、测试、批准和部署，那么基于主干的工作几乎是不可能的。您必须隔离提交，以确保每个单元都在代码的正确状态下执行。这里，像 gitflow 这样的分支策略更好，因为分支提供了隔离。合并变得很棘手，但仍然比处理单个提交容易。

另一方面，如果所有活动都由一个跨职能团队以真正的 DevOps 方式负责，那么除了基于主干之外的任何事情都没有意义。你不需要隔离，因为这只是不必要的开销。您更希望尽早发现代码冲突，这样就可以轻松快速地修复它们。这种工作方式使得做真正的 CD 变得容易，而不仅仅是 CI，因为测试和部署可以合并到团队的工作流程中。

# 我的看法

你可能已经注意到我在说基于主干的分支策略更好，但是另一方面，我提到每个策略更好取决于你的团队是如何组织的。

DORA 的研究很清楚:基于主干的开发是精英执行者的一个指标。通常其中一个技巧是将高绩效者从精英中区分出来。

我不觉得这有什么奇怪的。独立部署的小提交意味着每个变更更小，更可预测。这本身在许多方面都有帮助。

第一个是如果变化很小，bug 更容易被发现。第二，生产中的变化减少了精神负担，因为进一步的工作可能依赖于它，我们不必合并或精选新代码所依赖的代码。

第二种方式是小的部署意味着更多的部署，这反过来帮助我们练习部署，并推动我们自动化，从而使部署更加安全。

更进一步说，我从未见过一个批准步骤提供真正价值的部署管道。这仅仅是一种形式，负责人在批准之前进行最少的手工测试(如果有的话)。

即使采购订单或测试人员在批准每个部署之前对软件进行了细致的测试，这种努力似乎也是不合理的。在第三个特性之后，PO 可能会要求自动化。

我观察到的另一件事是阶段越多，你的部署过程就越糟糕。这听起来违反直觉，但事实证明，运行任何软件的 5-8 个环境都是非常昂贵的。因此，为了保持成本可控，这些环境(根据我的经验)在使用的资源方面有很大不同。这对测试和集成来说是非常糟糕的。

如果我的测试基础设施不同于我的生产环境，我不能通过在那个环境中运行测试而获得太多的信心。此外，部署到这些环境需要时间，并且有点容易出错(因为差异)。这不可避免地导致需要一个热修复过程，跳过大多数批准和环境，从而使您的整个系统不可预测。

我可以继续，但我想我已经表达了我的观点。

我还想说一句可能会引起争议的话。我经常看到一些中心团队配置 CI/CD 管道。如果我们在做一个真正的 CD，我会把管道配置交给开发人员，而不是一个中心团队。就像我们的代码开发一样，我们的管道也应该开发。这应该是开发人员持续思考的活动。此外，如果部署中断，修复它应该是当务之急。指责某个中央团队的渠道会妨碍这一点。

我更喜欢在真实的 CI/CD 环境中工作。基于主干的开发是最适合真实 CI/CD 环境的分支策略。

我想知道一些人是否在真正的 CI/CD 和(姑且称之为)传统的设置中工作过，仍然喜欢传统的设置。

如果是你，我很想听听你的经历！(其他评论也欢迎:D)。