# 您需要编写有意义的提交消息

> 原文：<https://betterprogramming.pub/you-need-meaningful-commit-messages-d869e44e98d4>

## 您的提交应该为未来讲述一个故事

![](img/b1b230f49bb16f7686dd0e407e211cc8.png)

克里斯·劳顿在 [Unsplash](https://unsplash.com/s/photos/books?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

我喜欢一本好书。小说或非小说。我最喜欢的书通常讲述某种形式的故事。

而我所知道的大多数故事，涉及的不仅仅是一个“开头、中间、结尾”。它们通常包含章节。较大的书使用更多的章节，把它们分成几节甚至几卷。

戏剧或音乐剧也是如此。这个故事分成几幕，然后是几个场景。这一部分是为了帮助演员，但也是为了帮助观众。幕间休息让你放松一下，伸伸腿，去趟洗手间。

但最重要的是，所有这些元素都有助于对一个想法或故事片段进行逻辑分组，使其更容易理解。因为当你有助于理解这个故事的时候，你可以更多地了解这个故事的真实内容。

# 源代码管理讲述了一个故事

代码也是一样。它讲述了一个故事。就像一个故事，你的代码需要章节或标记来帮助理解它。代码中有许多不同类型的章节标记——包、类等等。

然而，代码也是随着时间的推移而构建的。它不是一瞬间产生的。要理解*为什么*你的代码是这样构建的，你也需要理解*的历史*是如何构建的。

做了哪些决定？为什么这些类之间的命名如此不同？这将有助于像一个章节，使之更容易回答这些问题。

谢天谢地，我们有这些叫做源代码控制系统的东西可以帮助我们讲述这个故事。这些系统中的章节标记被称为提交。

提交本质上是对代码库进行的具有相同主题的一组更改。它通常有一条简短的消息，描述为什么要进行更改。

# 无用的消息

不幸的是，这些信息往往是无益的。您最后一次看到类似如下的提交消息是什么时候？

```
Fixed some testsRefactoringChanges per code reviewok - now it's done...
```

这有多没用？修复了哪些测试？它们以前坏过吗？还是 TDD 没有做到？你在重构什么？现在“完成”的“它”是什么？

如果我试图理解这些变化的故事，这些信息并没有告诉我太多。

理想情况下，这会让我对变化的意图有所了解。为什么需要它(例如一个故事或一个 bug)。请求变更的人。这正是我在查看 Git 日志时想知道的。

# 什么是有意义的信息？

然而，过于详细的信息也同样令人沮丧。列举你修改过的每一个文件的消息将很快被掩盖。

此外，在大多数源代码控制系统中，列举每个更改都是多余的。如果你需要看的话，系统可以告诉你细节。

其他伟大作家有很多关于这方面的好文章。它们在提交消息的格式和实际内容方面都有一点不同(下面会有更多的介绍)，但是它们在一些核心部分都是一致的。

## 1.独立主体与主体

这比你想象的更重要。

为什么？嗯，就像一封电子邮件。如果你在浏览你的电子邮件历史，你不会读完整封邮件。相反，你需要一个快速的总结内容的广告词。

如果您正在使用像`git log --oneline`或`git shortlog`这样的命令，尤其如此。

详细的部分也很重要，但它不会是您在日志中看到的第一件事。像评论一样，这可能是一个很好的地方来指出关于细节的附加注释或者改变的原因。

例如，如果您必须在进行特性更改之前进行重构提交，这将是一个很好的表达方式。或者，如果你不得不做出改变，不得不考虑一些边缘情况或什么，身体是完美的地方添加它。

## 2.保持主题简洁明了

Git 本身建议保持字符少于 50，但是其他指南说少于 72。无论如何——保持简洁。无法描述这么短的提交可能意味着您的提交太大了…

除了简短易读之外，主题信息应该清楚地表明变化。它应该描述如果您应用更改，您的代码库将会发生什么。

这与“干净代码”功能的想法相同。每个函数基本上都做了它说要做的事情。同样，一个有意义的提交将完全按照它的提交消息所说的那样去做。

你的信息也应该是命令式的主动语态。

不要使用像“修复时间通量电容器中的错误”或“将目标日期改为 1955 年 11 月 5 日”这样的短语。相反，说“固定通量电容器以减少钚的需求”或“将目标旅行日期设定为 1955 年 11 月 5 日”。

这个 [GitHub gist](https://gist.github.com/robertpainsi/b632364184e70900af4ab688decf6f53#a-properly-formed-git-commit-subject-line-should-always-be-able-to-complete-the-following-sentence) 描述提交消息的方式非常好:

> 一个格式正确的 git 提交主题行应该总是能够完成下面的句子:
> 
> 如果应用，这个提交将 *<你的主题行在这里>。”*

嘣。如果每个提交都这样做，历史跟踪会容易多少？代码审查会容易多少？

## 3.具有一致的格式

格式化只是你无法摆脱的事情之一。一致的格式总是使存储库更容易使用。尽管它很大程度上适用于代码，所以将它应用于 g it 可能看起来很傻。

但这并不愚蠢，这是必要的。

当你在阅读一本书时，一旦你买下并开始阅读，你就已经同意了一种格式风格。如果出版商决定允许某些行比其他行更短或更长，你会很难读懂它。

这同样适用于您的提交消息(和您的代码！).当格式一致时，理解可以达到最高水平。

有很多不同的方法可以拥有一个“好”的风格。 [Angular 的投稿文档](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-guidelines)就是一个例子，[Chris Beam](https://chris.beams.io/posts/git-commit/)的这篇文章也有一些很好的例子。

重要的是你的*团队*选择并坚持自己的风格。如果您在开源环境中工作，请确保遵循您所贡献的存储库的标准。

## 4.有意义的信息来自有意义的变化

如果你有什么都不做的提交，这些都不重要。还是做多了。或者是半生不熟和破碎的。

将您的提交视为一个原子事务。在每次提交结束时，您最好有一个工作代码库。至少它应该是可编译的。

你希望在不破坏东西的情况下增加一些价值(至少是故意的——我们总是会犯错)。

这样做好处是，如果您犯了一个错误并破坏了某个东西，您可以只恢复那个提交，以返回到工作状态。这可能是我的团队合并拉取请求的方式是“压缩并合并”的原因…

所有这些都是说，如果你专注于先做一些小的、有意义的改变，然后逐步地提交，那么做其他的事情会更容易。

# 例子

将这些规则放入一个简单的项目符号列表中，如下所示:

1.  主体与主体分离。
2.  保持主题简短明确。
3.  有一致的格式。
4.  有意义的原子提交。

现在我们已经介绍了一些，下面是一些有意义的提交消息的例子。

请注意，这些都是虚构的，但它们应该作为不同格式和准则的示例。虽然它们彼此并不一致，但只要稍加思考，您就可以找出它们各自的约定。

```
SYS-1345 (docs) - update method of procedure for upgrading hypervisorThe documentation for upgrading hypervisors was out of dateBug (api) : fix to response code returned in case of internal timeout (#API-4588)Our API was returning a 400 bad request when an internal timeout occurred. This change makes that response code a 503 to tell the client the request is retryableFeature #99 : allow users to re-arrange their dashboardThis change finally implements the ability for users to re-arrange their home dashboard via drag-n-drop. Some notes:
- currently toggleable. we will wait until an announcement is made to turn the toggle on
- drag-n-drop is limited only to dashboards already created. still need to implement drag-n-drop of a widget "template". this is capture in feature #100
```

# 其他资源、指南等。

*   [Chris Beams——如何编写 Git 提交消息](https://chris.beams.io/posts/git-commit/#seven-rules)
*   [角度贡献 Git 提交指南](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-guidelines)
*   [Git 提交消息指南要点](https://gist.github.com/robertpainsi/b632364184e70900af4ab688decf6f53#a-properly-formed-git-commit-subject-line-should-always-be-able-to-complete-the-following-sentence)
*   [清单之外——承诺的艺术](https://alistapart.com/article/the-art-of-the-commit/)

编码快乐！