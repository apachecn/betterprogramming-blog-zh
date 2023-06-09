# 为什么每个人都讨厌结对编程

> 原文：<https://betterprogramming.pub/why-everybody-hates-pair-programming-c7d1b52a4263>

## 为什么它的缺点多于优点的 7 个原因

![](img/f960abb73cec058f70e11252e9e0cba9.png)

照片由来自 [Pexels](https://www.pexels.com/photo/employees-using-a-laptop-in-the-office-7163386/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 [Anthony Shkraba](https://www.pexels.com/@shkrabaanthony?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

我最近的一篇关于结对编程的[优点的文章受到了很多批评。原来大多数人讨厌](/if-your-company-doesnt-do-pair-programming-leave-204335c25a54)[配对](https://en.wikipedia.org/wiki/Pair_programming)。

这让我很惊讶，因为我从中学到了很多，仅次于代码评审。

所以我决定探究一下结对有什么问题。

为什么开发者讨厌结对？

# **防止深度工作**

当有人在你身后看着你并告诉你该打什么的时候，你不能做深度工作。

我不能不同意这一点。“但是什么是*深功*？”你问。

> “在不受干扰集中状态下进行的职业活动会将你的认知能力推向极限。这些努力创造了新的价值，提高了你的技能，而且很难复制。”——卡尔·纽波特，《深度工作:注意力分散的世界中专注成功的规则》一书的作者

集体讨论解决问题的方法是无效的。如果个人带着他们事先想好的想法来参加会议，效率会更高。

编码也是如此。软件工程的困难部分是在给定的约束条件下(成本、技术债务、客户需求等等)提出创造性的解决方案。这最好在独处时完成。

也就是说，结对编程在一个解决方案被考虑出来并达成一致后，对于实现(也就是写代码)仍然是有用的——特别是当它涉及到你不是专家的代码库的部分时。

# **对所有开发者都不好**

有趣的是，大多数开发人员都是内向的人。内向的人喜欢刺激较少的环境，结对可能会过度刺激。

这是不总是配对的一个很好的理由。

但这并不意味着内向的人讨厌社交或者不喜欢团队工作。

特别是在“永远在家工作”的时代，结对可以提供一些受限的社交互动，否则内向的开发人员可能会整天不与任何人说话，盯着屏幕。对心理健康不好。

结对对于一些开发人员来说比其他人更好。但同样重要的是一个紧密团结的团队，在这个团队中，每个人都感到舒适并相互信任。

# **这会导致新开发人员失去信心**

在高级/初级配对会议中，如果高级工程师不小心，他们很容易让初级工程师觉得自己不够格。

如果高年级学生只是告诉低年级学生做什么，而没有解释任何基本逻辑，这种情况就会发生。或者更糟糕的是，如果学长既导航又开车，而让学弟只是简单地看着。

虽然这不是高年级学生的错(他们已经看过代码一千遍了)，但这可能会让低年级学生感到远远落后，他们不好意思问问题。这可能会导致信心的丧失，而事实上他们可能只是错过了一些关键的信息。

我亲眼见过这种情况，所以我能理解一次糟糕的经历如何影响开发人员对结对的看法。

出了差错，这可能是对时间的巨大浪费。但是高级开发人员的同理心和初级开发人员停下来问问题的信心会使这成为一次有价值的经历。

# **累死人了**

即使是外向的人也不能一天八小时配对。大多数开发者也会同意结对是令人疲惫的。

值得庆幸的是，很少有公司会全天结对编程。我见过的唯一这样做的公司是日复一日生产平庸的 CRUD 应用程序的开发公司。反正你也不想在那些地方工作。

考虑到配对需要大量的能量，它只应该用在能增加价值的地方。如果你正在做一件非常复杂的事情，无论如何你都需要时间独自思考和计划。

目标是找到一个平衡，而不是尽可能的配对。

# **只对初级开发者有用**

学习是片面的。它可以帮助初级工程师提高速度，但对高级工程师没什么好处。

虽然我同意学习是单向流动的，但是把结对说成是高年级/低年级错过了一个巨大的用例。

当一个特性涉及两个领域时，专注于栈的不同部分(前端和后端)的两个有经验的开发人员可以从合作中受益。就我个人而言，这是我最喜欢的配对时间。

也就是说，高级开发人员的部分工作是帮助培养初级人才。所以花一些时间传递知识而没有太多回报可能只是工作的一部分。

# **配对是多余的**

为什么要用两个开发人员来完成一个开发人员可以完成的任务？反正一次只能一个人写代码。

这忽略了配对的意义。如果一项任务可以由一个开发人员轻松完成，就没有必要结对。

但是有些任务 1 + 1 等于 3:

*   当你陷入困境时，得到另一双眼睛
*   培训新开发人员。与让他们去看医生相比，带他们看一些东西可能会节省更多的时间。
*   调试。第二个开发者比[橡皮鸭](https://en.wikipedia.org/wiki/Rubber_duck_debugging)好。句号。

如果我们更关心整个团队的速度，而不是个人的贡献，结对在特定的场景中会很有意义。

# **缺乏代码所有权**

让一个以上的开发人员在一段代码上工作会降低责任性。如果代码后来被破解，谁来负责就不再清楚了。

这是管理层的决定，但在我看来，做公关的开发商最终要负责。第二个开发者只是帮助他们。

不过，最终，团队应该足够关心他们的应用程序，以避免不惜一切代价推出糟糕的代码，并且永远不要使用其他开发人员作为借口。

# **结论**

配对有它的问题，不应该被视为灵丹妙药。它只是开发人员用来完成工作的工具箱中的一个工具。

我怀疑大多数开发人员对结对的不满来自于管理层对结对的实现，而不是实践本身。作为开发人员，我们希望被信任，结对编程可以让我们感到被关注。但是不应该，因为结对可以归结为简单的互相帮助。

总的来说，我发现结对非常有用，并且认为从不结对是一个错误。

也就是说，如果使用不当，它有可能打击士气，并驱使开发者去其他更好的地方。

我错过了结对编程的缺点了吗？你有什么想法？