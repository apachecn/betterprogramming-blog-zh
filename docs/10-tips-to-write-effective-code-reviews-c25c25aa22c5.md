# 撰写有效代码评审的 10 个技巧

> 原文：<https://betterprogramming.pub/10-tips-to-write-effective-code-reviews-c25c25aa22c5>

## 7.谦虚积极

![](img/db357d2980ff8ff564f83002f18cf64c.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

我们都见过这样的代码评审，其中代码不断地来来回回，需要多次迭代，直到最终合并。

不仅仅是作者讨厌评审不接受代码，评审者也会认为作者没有“理解”代码。

尽管有一些好消息；这不仅仅取决于修改的作者，反馈是否被顺利地接收，过程是否进行得更快。评审者也可以采用一些好的实践来确保评审过程不那么枯燥和清晰。

有效的代码审查对每个人来说都是双赢的，因为:

*   作者将更清楚地理解您的反馈，从而减少代码更改的迭代次数。
*   您将为作者提供一些提示和要点，供其考虑，这些提示和要点将影响他们对代码库的整体处理，因此下一篇文章中的“错误”会更少。
*   这项工作将被更快地合并，从而消除潜在的其他障碍。
*   至少，您将有一个关于实现选择的高质量讨论。

请注意，本文并没有解释如何进行代码审查，或者在这样做的时候需要注意什么。相反，我们在这里列出了一些关于评估流程的*方法*和*行为*的技巧和经验法则。因此，如果您已经在工作中定期审查代码，本文旨在帮助您重新考虑您的一些实践，并成为一名更好的审查者。

我们开始吧。

# 1.总是说为什么

让我们来看看一些潜在的评论:

`bad function name`

`this shouldn't be here`

`I think this might break`

这些评论有一些错误，但最突出的问题是它们没有提供推理。从这些评论中，代码的作者只能**假设**他们的代码有什么问题，我们都知道假设是多么糟糕——特别是在这种情况下；

*   作者可能会得出错误的结论，为什么他们的命名/代码是错误的，并实现错误的修复。这肯定会导致审查的额外迭代。
*   作者不会得到关于为什么他们的命名/代码是错误的有根据的反馈。反之，他们可能会学到一些他们不知道的东西，或者通过看到一些他们没有马上看到的东西来拓宽他们的视野。

更好的评论是什么？让我们看一个例子:

```
This function name isn't ideal; it should start with a verb to indicate action, and in this case have an indication of the argument, maybe with something like '...ByUserId'. Remember to check our code style docs for more details on this
```

是的，你只是写了一堆更多的单词，但是你现在清楚地告诉作者他们的名字有什么问题，以及他们在考虑函数名的时候应该怎么做。

你知道下一次同一个作者给你发代码审查时，你会有多高兴吗？你会发现他/她现在遵循了函数名的正确规则。试试看！

# 2.要彻底

代码的作者可以是你团队中最资深的人，知道代码库的所有细节，或者是提交第一次代码评审的新成员。

这不应该影响代码审查的质量或彻底性，也不应该仅仅因为你信任高级员工的工作就让他们松懈。

代码审查不仅仅是为了确保代码质量和结构，也是为了寻找作者可能忽略的角落案例或错误，甚至只是随机的打字错误。

承认吧，它发生在我们最好的人身上。在 bug 进入生产阶段(甚至进入产品阶段)之前捕获它，总是比发现 bug、报告它、找到原因、修复问题并将其发送给另一个代码审查更有效。

# 3.不要给出完整的结果

由于代码的审查者发生了变化，当您检测到问题时，修复代码或提出解决方案就不是您的工作了。

是的，你可以(有时应该)帮忙或讨论你的想法，但这并不意味着你应该给出问题的完整解决方案。考虑如下的注释:

```
this method doesn't quite do the job, it should be something like:
```
<code here>```
```

当你这样做的时候，代码的开发者将从你的建议中学到很少的东西；在某些情况下，他们甚至会复制粘贴你的解决方案并浏览一遍，而不会仔细考虑它。

尤其是如果你不断地这样做，开发人员很容易变得懒惰，在编写复杂的代码时不会考虑太多，认为评审人员会在他/她发现问题时提出修正建议。如果您经常这样做，那么如果您经常被选为团队中代码变更的审查者，也不要感到惊讶。

在这种情况下，正确的行为应该是由*描述*这个方法应该是什么样子。你应该说明为什么它不起作用，以及你将如何处理手头的问题，也许可以一步一步地写下来。甚至有些伪代码在某些情况下也是可以的。只要你让开发人员真正考虑你建议的方法，而不仅仅是复制粘贴你的解决方案，你就是优秀的

# 4.不要推迟审查

在任何一种团队中，尤其是工程团队，不**阻碍**对方的工作是非常重要的。

对于开发者来说，上下文切换并不是一件容易的事情。并且等待对他们的一个问题的代码审查，他们将不得不切换到另一个故事来工作，并且在他们得到审查和处理其评论时来来回回。

即使评审进行得非常快，这种情况还是会发生——因为开发人员会想继续下一个任务。然而，审查拖得越久，开发人员将不得不管理越多的故事和代码审查，就像这样在分支之间来来回回，重读他/她的代码(再次，上下文切换)，解决合并冲突，等等。

同样，想象一下你实际上会阻碍这个人的工作；这意味着他们不能继续工作，直到这个特性被完成/代码变更被合并。在这种情况下，他们将无法工作，直到你完成审查。这整个事情对双方来说都是既低效又烦人的(因为，哦，好吧，一旦他们被阻止，开发者可能会每 30 分钟发一条消息给评审者，要求他/她的评审🙃).

为了确保您不会为这种混乱让路，在您的计划中优先考虑代码评审总是一个好的实践。当然，您可能有更紧急的任务要处理，或者可能不总是能够马上抽出时间来处理评审，但是您有义务在您自己的工作和评审之间找到正确的平衡。

如果你有责任为很多人评审代码，那么每天在你的日程表中安排一个时间段来评审代码，并且在这段时间内只关注他们(或者两个时间段来更快地处理多次迭代)。

# 5.遵循指导方针

如果你的团队足够大或者足够结构化，你可能会在知识库中编写一些指导方针，解释为项目/代码库编写代码的指导方针。

它可以包括代码组织的一般方法、命名约定和许多设计选择。如果您正在积极地编写代码并审查他人的代码，您可能会习惯于这些准则。

但是你应该试着回头看看指南，每隔几周或每个月快速阅读一遍。您会惊讶地发现，在代码评审中，您已经忘记了或者没有注意到的要点有多少。

如果你的团队没有这样的指导方针，那么在审查代码的时候写一个也许是个好主意。在仅仅几个代码评审中，你肯定会提出一个冗长的清单，列出你在评审过程中注意的要点。

更重要的是，这样的指南列表将作为团队中唯一的事实来源，确保所有代码作者和评审者在编写/更新/阅读代码时都有资源可以参考。

# 6.赞美好代码

代码审查并不总是坏消息。有时候你会碰到一些代码，会让你很欣赏作者；一个逻辑，一些非常有效的查询优化，或者一个出色的 UX。说出来。

你可以简单地用`nice!`来评论相关的代码，或者用一个更长的句子来说明你喜欢它的什么。无论哪种方式，这个人都会觉得**得到了赏识**，这无疑会对团队合作产生影响。此外，如果代码的其余部分不尽如人意，他们也不会在审查后感觉太差。

最重要的是，作者现在已经验证了他们写的文章实际上是好的，所以他们可以尝试在未来创建更多这样的内容，或者对其他问题采取类似的步骤。

# 7.谦虚积极

这些年来，我偶然发现了很多写着`this is stupid`，或者仅仅是`no`的评论。我可能在一些糟糕的日子里也写了一些。

更不要说以上陈述理由的观点了，这样的评论只是令人不快和沮丧的，不会创造任何价值。[如果你想进一步阅读，Gitlab 给出了一些关于这个](https://about.gitlab.com/blog/2021/03/09/tips-for-better-code-review/)的好建议，听起来非常符合他们公司“积极意图”的价值观。

长话短说，在评审评论中保持友好是值得的，尊重其他人的时间和工作，并假设他们在写代码时尽了最大努力。在代码评审中吹嘘、羞辱或生气绝对没有任何好处。

让我们看看我从以前的代码评审中复制的一些真实的例子。注意那些在句子中定下基调的单词。

```
- shouldn’t this be taking "value" as well 🤔
- maybe you can use "find" instead
- I think start the name with "get..."
- "Synchronize" sounds weird here imho.
- please add an empty line here
```

相信我，一点点`imho`或者”🤔“会大有帮助的。

# 8.向资源指出

我不知道在我第一次做前端的日子里，我们在代码评审中分享了多少次这个关于 css 属性排序的[链接](https://css-tricks.com/poll-results-how-do-you-order-your-css-properties/)(应该只配置 stylelint🙈).我相信它甚至一度出现在我的书签里。

如果你知道某个资源可以帮助代码作者解决某个问题，请在你的评论中链接它。通常情况下，他们会欣赏这些评论，这甚至可能成为他们在工作中经常使用的资源。如果你有一个很大的资源，并且信息很难被发现，那么这个资源也可以是你公司指导方针的特定部分。

也许你能找到的更重要的资源就在作者正在工作的代码库中。过去已经被其他人实现的类似的逻辑或组件对作者来说是一个很好的指导，如果他们以前没有使用过，他们可能不知道代码库中的特定部分。

如果你发现了一个需要改进的代码，并且你知道以前有人写过类似的代码，不要认为作者会知道找到它。在你的评论中指出来，这样作者就可以将他/她的代码与现有的代码进行比较，进行改进或者在必要时遵循相同的约定。

# 9.不是你，是他们

在阅读代码以供评审时，让你能够容易地理解它是很重要的。如果代码中有一部分对你来说是胡言乱语，这并不意味着你没有足够仔细地阅读代码是错的。相反，这意味着 ***代码不够清晰*** 。

如果你很难理解这部分代码是做什么的，那么将来其他开发人员需要接触这部分代码时，就很难理解了。当您发现此类代码时，应该毫不犹豫地说出来。不要只是要求作者向你解释那段代码，相反，**要求重写****。**

**如果实在写不出更清楚的方式，至少要求作者在代码中添加一些注释。请随意提问以理解代码的目的，这样您就可以建议替代方法并讨论解决方案，但是您不应该仅仅满足于回答，您应该确保最终被合并的代码是容易理解的。**

# **10.你的不总是这样**

**完成事情的方法通常不止一种。当阅读一段代码时，你可能会想象其他的方法，并且会倾向于在评论中提出来，因为这是一个更好的解决方案。有时候你需要问自己:这真的是更好的解决方案吗？还是作者的方式也不错但建议更接近你一贯的做法？**

**除非对代码的复杂性或可读性有明显的好处，否则在建议重写之前要三思。重读代码，确保它符合您团队的惯例和代码风格，确保它能很好地完成工作，并与您心目中的替代方案进行比较，看看它会如何影响代码质量。如果作者的方法还可以，但你仍然认为你的更好，把它作为一个建议而不是一个必须的改变来写，并开诚布公地与作者讨论你所建议的改变的原因和细节。**

**希望这些提示能帮助你成为一个更有效的代码评审者，从长远来看，为你、代码作者和整个团队节省时间。如果你对上述内容有任何想法，或者在审查代码时可能有用的任何其他建议，请在评论中告诉我。**