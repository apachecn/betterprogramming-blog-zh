# 为大拉取请求强制函数

> 原文：<https://betterprogramming.pub/forcing-functions-for-great-pull-requests-d7b11989f43b>

## 为什么一些开发团队可能会自然地写出好的 PRs

![](img/6b8e970d8a16feddf0ffd5888b53385c.png)

Theo Aartsma 在 [Unsplash](https://unsplash.com/s/photos/historian?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

网上有很多关于[为什么](https://frontside.com/blog/2020-04-15-7-reasons-for-good-pull-request-descriptions/)我们应该设计好[拉请求](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests)以及[如何](https://github.blog/2015-01-21-how-to-write-the-perfect-pull-request/)去做的说法。尽管这似乎被很好地接受了(接近“常识”)，我还没有观察到这是普遍的做法。

我最近一直在思考这个问题，特别是自从我在一个新的团队、一家新的公司开始我的旅程以来——因为我的前一个团队和现在的团队有非常不同的公关写作文化。这让我不禁要问:

> 为什么我们认为写好的 PRs 显然是有益的，但我们却没有看到预期的实践中的广泛采用？

根据我自己的经验，我将提供我对一些[强制函数](https://en.wikipedia.org/wiki/Behavior-shaping_constraint)的想法，这些函数可能自然地使开发团队倾向于产生更高质量的 PRs。

我对高质量 PRs 的标准很简单(但不容易):

1.  审查 PR 的团队能够很容易地收集工作的背景，这包括新的团队成员(2 个月以上)——至少，这应该包括关于我们为什么要做这项工作、它是如何完成的以及明确风险和假设的信息。
2.  工作本身已经被分解和组织，使得在相对较短的时间内(最多 1 小时)吸收所有的变更变得容易。通常，这表现为结构良好的提交，它们建立在彼此的基础上并讲述一个故事。

# 事情的现状

![](img/d19f1e0519db34f12bddfe7761c0c420.png)

本·格里菲斯在 [Unsplash](https://unsplash.com/s/photos/theory-practice?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

互联网上不乏提倡撰写“好的”拉式请求的内容。搜索:

*“我为什么要写好的拉取请求”*(3.65 亿条结果)，或者

*《如何写好拉请求》(*6.01 亿条结果)

你会发现关于编写好的拉式请求的好处的意见和博客层出不穷，甚至还有一些关于好的拉式请求可能是什么样子的指导。

特别是对于“为什么”，文章的主题可以归结为以下几点:

*   你(公关作者)希望让审阅者容易理解你的更改，并帮助你发布这些更改。
*   写得好的 PRs 可以作为未来开发者的文档(包括未来的你！)围绕某些变化的基本原理，或引入这些变化所采用的方法，即捕捉上下文。
*   一份写得好的公关提供了一个机会来合理化和证明你的改变，让别人清楚你的想法，并给人们一个机会来质疑你的改变理由或你选择的方法。

这在理论上是有道理的，但是…

尽管这些好处广为人知，并且通过 PR 模板等机制使“如何做”变得更容易，但精心制作的 PR 绝非无处不在。

# 经验观察

在我的职业生涯中，到目前为止，我已经看到一些团队自然地被精心制作的 PRs 所吸引，而其他团队充其量只是勉强地采用它们，将它们视为“过程”和“文书工作”,这分散了他们对交付代码的注意力。

![](img/5654bf1250a185cfc72751fa1689108d.png)

照片由[弗拉德·希利塔努](https://unsplash.com/@vladhilitanu?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/lego-team?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

努力起草好的 pr 的团队理解理论上的好处，*但是经常受到微妙的影响(行为形成倾向，也称为强制功能),这种影响维持了编写好的 pr 的实践，直到它成为团队 DNA 的一部分。*

以下是我观察和体验过的一些强迫函数:

1.  对于交付工作所使用的语言、工具或框架，具有不同经验/技能水平的团队。不同水平的专业知识迫使演示的简化(PR！)的那部作品。
2.  与#1 密切相关，对代码库/被改变的系统的熟悉程度不同。团队中的每个人都可能在技术上处于平等的地位，但是他们并不都理解代码库的每一部分，因此在合并之前，必须对变更进行很好的解释和展示。
3.  在新的团队中，团队成员彼此还不太了解，这导致人们投入大量精力来清楚地传达他们试图进行的改变的“为什么”和“如何”。
4.  与#3 密切相关的是一个经常变化的团队(人们加入，其他人离开公司或转移到其他团队)。这激励了编写良好的 PRs 作为捕获和传递上下文的机制。这种情况在鼓励高流动性的公司中可能很常见，或者团队的性质需要相对较短的任期。
5.  一些团队与许多利益相关者一起工作，这些利益相关者需要知道正在交付的工作的上下文，但是他们不是代码库或工作的技术细节方面的专家。来自相邻团队的产品经理、设计师和工程师通过内部采购为代码库做出贡献。
6.  代码库有历史或上下文，因此 PR 中的提交本身不足以讲述完整的故事。或者代码库代表一个与其他系统有复杂交互的系统，并且所做的更改必须与那些系统的更改相协调。

你还见过哪些强迫函数？你是一个工程经理或团队，希望改善你团队的 PR 写作文化吗？你能利用这些力量来改变你团队的 PR 文化和实践吗？

# 资源

1.  关于拉请求:[https://docs . github . com/en/Pull-Requests/collaboration-with-Pull-Requests/proposing-changes-to-your-work-with-Pull-Requests/about-Pull-Requests](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests)
2.  强制功能:【https://en.wikipedia.org/wiki/Behavior-shaping_constraint 
3.  良好拉动请求描述的 7 个理由—[https://front side . com/blog/2020-04-15-7—良好拉动请求描述的理由/](https://frontside.com/blog/2020-04-15-7-reasons-for-good-pull-request-descriptions/)
4.  如何写出完美的拉取请求—[https://github . blog/2015-01-21-how-To-Write-The-Perfect-Pull-Request/](https://github.blog/2015-01-21-how-to-write-the-perfect-pull-request/)