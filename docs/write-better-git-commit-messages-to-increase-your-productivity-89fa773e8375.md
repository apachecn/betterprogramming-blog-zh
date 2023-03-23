# 编写更好的 Git 提交消息来提高您的生产率

> 原文：<https://betterprogramming.pub/write-better-git-commit-messages-to-increase-your-productivity-89fa773e8375>

## 看看对您的提交消息风格的一个小小的改变是如何对生产力产生巨大的影响的

![](img/cc13be0fc8a5659ba5e201ef796d6e45.png)

由[卡尔·海尔达尔](https://unsplash.com/@carlheyerdahl?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/productivity?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在本指南中，我将展示如何格式化 Git 提交消息，不仅优化您自己的生产率，而且优化整个软件开发团队的生产率。 ***本指南假设您了解*** [***基本 Git 工作流程***](https://docs.github.com/en/get-started/using-git/about-git) ***。***

# 为什么要编写 Git 提交消息呢？

要理解为什么 Git 提交消息对每个人都很重要，你可以打开你正在处理的任何个人或公司项目，并执行`git log`来获得使用过的旧提交消息的列表。

大多数人会看到至少一些提交消息的列表，如下所示:

```
commit 74b187e20b796ef243a6d47d2bd32f17fa5c0942 
Author: *****
Date:   Wed Feb 16 17:07:55 2022 +0100
clean upcommit 6b907b81bb1075a6faa596bc639ee0939359e29a
Author: *****
Date:   Wed Feb 15 12:17:55 2022 +0100
fix formatcommit fda43da641946a9c50988474b15ac8d1f7d05343|
Author: *****
Date:   Wed Feb 15 12:03:23 2022 +0100
hotfix URLcommit 09f6f131dece7bf9056063b9b4235f49caf8e0a5
Author: *****
Date:   Wed Feb 14 11:17:15 2022 +0100
redo fixcommit 75810b248462dc17adb3dae402b344f7fef85862
Author: *****
Date:   Wed Feb 14 09:35:43 2022 +0100
fix style
```

如果你问开发人员做了什么，你无法回答，因为 Git 提交消息没有意义。此外，如果要求您删除票证 XY，您不知道要还原哪些提交。此外，如果提交引入了破坏性的更改，您也不会通过阅读提交消息来了解它。

如果您编写了可读且信息完整的提交消息，那么您自己和所有其他开发人员都是经得起未来考验的。只要在 Git 提交消息中写下所做的事情，就可以节省研究和故障排除的时间。

# 提交消息的一般结构

在终端中创建提交消息有两种不同的方法:

## 基本:

```
git commit -m <your_message>
```

## 详细:

```
git commit -m <your_message> -m <detailed_description>
```

# 改善 Git 信息的 5 个步骤

1.  大写和标点:第一个单词要大写，不能以标点符号结尾。
2.  提交类型:必须指定提交的类型来描述您的更改。您可以使用:`Bugfix`、`Refactor`、`Hotfix`
3.  长度:第一行不应该超过 50 个字符，正文的长度应该限制在 72 - [见 Linus Torvalds 解释](https://github.com/torvalds/linux/pull/17)或[这个有用的指南](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)
4.  语气:在主语中总是使用祈使语气，因为它给出了你发出命令或请求的语气
5.  内容:没有短语的直接句子:`though`、`I think`、`kind`

内容非常重要，如果你写下来，你应该考虑并回答以下问题:

*   这些变化的原因是什么？
*   我的改变产生了什么影响？
*   为什么需要改变？
*   关于的变化是什么？

在没有详细的背景信息的情况下，总是试图以一种每个读者都能理解变化的方式进行总结。明显的变化还是要尽量详细的解释。

例如，比较这两条提交消息:

1.  git commit -m“添加边距”
2.  git commit -m“向标题添加边距，因为它与面包屑文本重叠”

如果您阅读这些提交消息，很明显哪一条信息更丰富。

# 更进一步——使用常规提交

[常规提交](https://www.conventionalcommits.org/en/v1.0.0/)是一个包含提交消息之上的轻量级约定的规范。它有一套简单的规则来创建可读、可理解和信息丰富的 git 提交历史。这将导致一个更有生产力的团队，每个开发人员都可以理解和维护 git 历史。

使用传统提交时，每个提交消息的结构如下:

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

提交消息包含以下三个元素:`header`、`body`、`footer`

## 提交消息头

表头由一个**强制类型**、一个**可选范围**和一个**强制简短描述**组成。

该类型可以包括:

*   **专长:**引入新功能
*   **修复:**修补你代码库中的一个 bug(bug fix 或 hotfix)
*   **构建:**影响构建系统或外部依赖的变更
*   **苦差事:**更新依赖关系，不涉及 fix 或 feat，不修改 src 或测试文件。
*   影响持续集成过程的变更
*   **文档:**更新文档或介绍文档
*   **样式:**更新代码的格式；删除空白，添加缺失的空格，删除不必要的换行符
*   **重构:**反应代码段以优化可读性而不改变行为
*   **性能:**提高性能
*   **测试:**添加/删除/更新测试
*   **恢复:**恢复一个或多个以前的提交

可选范围提供了额外的上下文信息。

*   允许的范围取决于项目
*   **不要使用问题标识符作为作用域**

描述是代码变更的**简短摘要**。

*   用祈使语气！
*   只有小写
*   没有标点符号

## 提交消息正文

提交消息体是可选的，在描述后提供一个空行。它可以由不超过 72 个字符的任意数量的换行符组成。此外，身体被视为自由形式。

## 页脚邮件正文

允许一个或多个页脚，在正文后提供一个空行，并且必须由单词标记组成，后跟一个`:<space>`或`<space>#`分隔符，后跟一个描述(或任何字符串)。单词 token 必须用`—`代替空格:`Designed-By: UserX`、`Acked-By: John`、`Solves-Ticket: Jira-123`

如果提交给软件引入了一个突破性的改变，这些规则的一个例外发生了。如果是这种情况，页脚必须包含大写的文本分隔符，后跟一个冒号、一个空格和一个描述。

## 例子

为了演示这些规则，我添加了一些如何使用传统提交消息的例子

[**提交带描述的消息和一个断行改变页脚**](https://www.conventionalcommits.org/en/v1.0.0/#commit-message-with-description-and-breaking-change-footer)

```
feat: allow provided config object to extend other configsBREAKING CHANGE: `extends` key in config file is now used for extending other config files
```

[**提交多段正文多页脚消息**](https://www.conventionalcommits.org/en/v1.0.0/#commit-message-with-multi-paragraph-body-and-multiple-footers)

```
fix: prevent racing of requestsIntroduce a request id and a reference to latest request. Dismiss
incoming responses other than from latest request.Remove timeouts which were used to mitigate the racing issue but are
obsolete now.Reviewed-by: Z
Refs: #123
```

**提交基于标签 473 添加特性的消息**

```
feat: send an email to the customer when a product is shippedSolves-Ticket: Jira-473
```

# Git 提交消息的比较

例如，您可以查看以下提交消息，这些消息可以相互交换，但会提供不同的信息。

## 好的

1.  `perf: optimize loading of items on landing page`
2.  `feat: send an email to the customer when a product is shipped`
3.  `fix: add the correct company name to the footer and replacing the dummy text`
4.  `revert: revert a previously introduced bug in items retrieving from database`

## 严重的

1.  `optimize landing page`
2.  `send email`
3.  `oops`
4.  `I think I fixed it this time?`

我想在这里展示，拥有一个格式良好的提交消息将会更详细地解释所完成的任务，并且不从事项目工作的开发人员也可以理解在这个 Git 提交中做了什么。

编写一个格式良好的提交消息对每个开发人员来说都是一项有益的技能，它还可以提高整个软件开发团队的生产力。开发者之间的交流变得更好，更容易协作。

# 结束语

请始终记住，提交消息用于存档对软件所做的每一个更改，并且可以成为解密过去的手稿。此外，作为一名开发人员，它可以帮助您基于过去所做的工作在未来做出合理的决策。

在本文中，您了解了如何改进自己的 Git 提交消息的基础知识。我希望这能对您有所帮助，并能够将其应用到您自己的软件项目中。

此外，在您将传统提交应用到您的软件开发团队之后，您可以引入这个 npm 项目,它可以用于从使用传统提交方法完成的提交中自动创建变更日志文件

我很想听听你对这些方法的想法和想法。如果你知道另一种方法或有任何问题，请写在下面。如果可能的话，我试着回答他们。

```
**Want to Connect?**Say Hello on: [Twitter](https://www.twitter.com/paulknulst), [LinkedIn](https://www.linkedin.com/in/paulknulst/), [GitHub](https://github.com/paulknulst)
```

*这篇文章也发表在我的个人博客上:*[*https://www . paulsblog . dev/write-better-git-commit-messages-to-increase-your-productivity/*](https://www.paulsblog.dev/write-better-git-commit-messages-to-increase-your-productivity/)