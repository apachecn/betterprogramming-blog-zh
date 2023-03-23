# Git 中常规提交的一个例子

> 原文：<https://betterprogramming.pub/a-case-for-conventional-commits-in-git-d70c65245009>

## 添加结构并提高提交的可读性

![](img/5a3449197b3d3d4608b7126d53fed4a1.png)

[扬西·敏](https://unsplash.com/@yancymin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/git?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

大约一年前，我开始在我的个人项目和工作中使用常规提交，到目前为止我并不后悔。这让我更清楚地传达了什么是变化。因此，我的提交历史更加易于管理。

*传统提交*是一种用于格式化提交消息的规范，它是轻量级的，人和机器都可读。即使不使用可以解析它们的工具，它对开发人员来说仍然很有用。

每个提交消息都有一个类型，将变更分为特性、修复、测试等。它还有一个可选的范围，即项目的哪个部分受到变更的影响，以及对已经完成的工作的简短说明。接下来是正文，包括更多的细节和事件发生的背景。最后，页脚将包含来自问题跟踪器的票据编号等信息以及类似的元数据。

了解它的样子最简单的方法就是看一些例子。下面是满足规则的最小提交消息。

```
feat: add an endpoint to get contents recommendationGET /contents/:topics/recommendation returns a JSON with some
recommendations for the user.
```

下面是一条更长的提交消息，包含更多详细信息。它有一个作用域(`api`)和一个带有元数据的页脚。

```
test(api): assess test suite quality with mutation analysisAdd a new executable `mutation-analysis` that analyses the API's
test suite and returns a score that we can use to keep track of
how good our tests are at catching defects. This is a better
approach than code coverage.It works by running the tests against mutants of the API
(slightly different versions of the code, e.g. a < could be
replaced with a <=) and counting the proportion of mutants that
the tests catch. This is relying on the assumption that most
mutants are invalid and should make the tests fail.Issue: ABC-456
Approved-By: Tom Feron
```

你可以通过阅读[规范](https://www.conventionalcommits.org/en/v1.0.0/)来了解更多关于如何格式化它们的信息。我接下来的目标是与他们分享我的经历，以及他们如何为我提供价值。

# 轻量级选手

如你所见，它们非常简单，几乎不需要任何承诺。不需要设置任何东西，也不依赖任何工具。如果你改变主意，你可以简单地停止使用它们。

因此，从常规提交开始的成本几乎为零。

你写提交信息可能要多花一点时间，但是你的队友——或者你未来的自己——会为此感谢你的。

唯一的要求是决定一些提交类型(或者[偷一些](https://github.com/conventional-changelog/commitlint/tree/master/%40commitlint/config-conventional))和作用域，如果你决定使用它们的话。不过，开始时，一些即兴表演是非常好的。用自己觉得合理的就行，以后再调整。

# 更容易通过

软件开发的一个重要部分是与其他人交流，而不是与计算机交流。这包括评论你的代码，[保持事情简单](https://medium.com/swlh/your-job-as-a-developer-handling-complexity-66e0c7063a35)(甚至有时以牺牲性能为代价)，以及制作好的提交消息。

清晰的交流意味着快速准确地传达意思。考虑到这个目标，标准格式可以帮助其他人快速解析提交消息，以了解更改的内容，并突出显示关键信息。

在使用常规提交一段时间后，我注意到它如何促使我给出更多的解释和上下文。我以前会写提交消息，比如“修复测试”——拜托，谁没有呢？—我现在将编写如下提交消息:

```
test(api): fix comment's JSON in endpoint testThe key `actorId` was deprecated a few months ago and removed
recently but the test suite hadn't been updated.
```

注意，我在这里使用`test`进行测试修复，除非它暗示了应用程序代码中的一个错误。`fix`应该保留给 bug 修复。事实上，它使得调试回归和发布跟踪更加容易。说到这个...

# 1 次提交= 1 次更改

我在使用传统提交时注意到的另一件事是它如何迫使我一次提交一个变更。当你不得不用`feat(task-runner): something`开始提交时，在同时提交一些不相关的重构之前，你要三思。

有时，将一些不相关的函数重命名(或某段代码的重新格式化)与您正在开发的新功能放在同一个提交中，并在提交消息的正文中提及，这是可以接受的。

虽然这有时是真的，但是在太多的提交和随之而来的噪音之间要找到一个恰当的平衡。您不一定希望将更改隐藏到其他提交中，从而混淆提交历史。

你不需要传统的提交来清楚地将变更分割成单独的提交，但是，从我的经验来看，这样做有助于获得必要的规则。

# 发布跟踪

在敏捷开发的时代，很难跟踪发生了什么变化以及什么时候发生的。在生产中调试问题时，这尤其成问题。常规提交以一种对人和机器都可以解析的方式告诉您每次提交的更改类型及其范围。

跟踪发布有用的另一种情况是用于流量分析。知道你的网站转化率的峰值发生在`3d0e862dacf9655236bd73c31676170037e87483`，甚至`fix spacing`部署之后，并没有什么帮助。

使用常规提交，您可以根据类型和范围过滤掉不相关的提交，并且只显示容易成为您所观察到的根本原因的变更。

# 变更日志

在准备 sprint 回顾的时候，你有没有被问到(或者你自己问你的同事)“我们这周发了什么？”您的任务管理器或看板可能对显示实际合并的内容没有太大帮助。一些变化没有被这样的工具捕捉到，其他的只是吉拉问题的一部分，但是仍然传递价值，值得一提。

同样，常规提交有助于自动生成准确反映实际情况的 changelog。

有一些工具可以使这个过程自动化。例如，[conditional-changelog](https://github.com/conventional-changelog/conventional-changelog)就是为这个目的而设计的一套工具。

# 结论

考虑到开始使用常规提交是多么容易，而且几乎不存在成本，您会鼓励任何人至少尝试一下，亲自看看它是如何进行的。

一开始不需要自动化任何东西。传统提交只有在人们使用时才是有用的。

如果你喜欢这样的故事，可以考虑成为[媒体](https://tomferon.medium.com/membership)和/或[订阅](https://tomferon.medium.com/subscribe)的会员。