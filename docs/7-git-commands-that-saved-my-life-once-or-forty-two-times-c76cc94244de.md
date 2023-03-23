# 至少救过我一次命的 7 个 Git 命令

> 原文：<https://betterprogramming.pub/7-git-commands-that-saved-my-life-once-or-forty-two-times-c76cc94244de>

## #5 —编辑旧的提交消息

![](img/876a29472a17f4c11059030a5b02f82b.png)

照片由[扬西·敏](https://unsplash.com/@yancymin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我不知道我花了多少时间试图解决冲突，当他们彼此远离时，用 master(或 [main now](https://github.com/github/renaming) 作为本文中的默认示例)重新调整我的功能分支，或者用我的个人电子邮件而不是专业邮件更新几个提交的作者——说真的，我这样做了很多次。

题目是 [Git](https://git-scm.com/) 的时候我肯定不是专家。其实我用的第一个[版本控制系统](https://en.wikipedia.org/wiki/Version_control)是[Subversion(SVN)](https://subversion.apache.org/)——9 年多前。三年后，我开始使用 [TFS / TFVC](https://docs.microsoft.com/en-us/azure/devops/repos/tfvc/what-is-tfvc?view=azure-devops) 好吧，不要紧，我们来谈谈 Git 那个。

我四年前才开始专业使用 Git，对终端命令一点都不熟悉。

由于我以前的经验是使用 [TortoiseSVN](https://tortoisesvn.net/) 用户界面，我只需右键单击一些文件和文件夹并“与基础进行比较”，然后交叉手指让一切按预期工作，所以我对 git 的第一次尝试是尝试一些友好的用户界面工具，如[源代码树](https://www.sourcetreeapp.com/)或 [GitHub 桌面](https://desktop.github.com/)。

有一段时间一切都很顺利，但当第一个大问题出现时，我意识到，我必须搜索的任何关于复杂问题的问题，在终端上用 git 命令回答的次数比用 git UI 点击回答的次数多 10 倍。

显然，使用 UI 工具并不妨碍您在情况变糟时使用终端，但在我的情况下，我更喜欢尽快开始使用终端。

我写这篇文章是希望你不需要花太多时间去解决这些情况。开始吧！

# 1.获取或设置用户和电子邮件变量

git 中的配置变量有三级:*本地*、*全局*和*系统*。第一个(如果已定义)会覆盖第二个，依此类推。通过这种方式，我们可以在计算机中进行全局配置，但如果需要的话，也可以为特定的存储库进行本地配置。

注意:如果你有一个局部变量，它会覆盖全局变量。

要了解为特定存储库配置的电子邮件，请转到存储库文件夹，分别运行每一行:

```
git config user.name
git config user.email
```

如果您在 git 存储库之外尝试这个命令，您可能仍然会得到一些东西，但是它将是您的全局或系统配置。

你想知道每个级别配置了什么吗？只需在`config`命令后添加具体选项:

```
git config --system user.email
git config --global user.email
git config --local user.email
```

*   最后一个只能在 git 存储库中工作。
*   通过将`email`更改为`name`，您可以对“名称”配置运行相同的命令。

## 如何设置这些 git 配置？

这基本上是相同的命令，但在末尾添加了值。像这样:

```
git config user.name "Your Name"
git config user.email your_email@domain.com
```

如果在 git 存储库中运行这些命令，这些值将被设置为本地级别。如果您在 git 存储库之外，它们将被设置为全局级别。

好的，但是让我们假设你主要在工作中使用你的计算机。您可以将您的专业电子邮件添加为全局配置。

但是如果你想在你的个人 git 账户里自学一些东西呢？

您可以设置一个特定的存储库来使用您的个人配置，而不会打乱所有其他文件夹的配置。我可能会做这样的事情:

```
git config --global user.name "Lucas Tagliani"
git config --global user.email my_professional_email@company.com
```

在我的个人 git 存储库文件夹中，我会:

```
git config user.email my_personal_email@freedomain.com
```

有更多关于[这个堆栈的信息请参见](https://stackoverflow.com/questions/46941346/how-to-know-the-git-username-and-email-saved-during-configuration/46986131#46986131)或[官方文档](https://docs.github.com/en/get-started/getting-started-with-git/setting-your-username-in-git)。

# 2.如何更改一个或多个提交的作者(电子邮件)

如果您只需要修复您上次提交的电子邮件作者，只需:

```
git commit --amend --no-edit --author="New Name<new_email@free.com>"
```

然后就可以用标志`--force-with-lease`推了:

```
git push --force-with-lease
```

现在，如果您需要修复一个以上的最近提交，建议您遵循[这些步骤](https://confluence.atlassian.com/bitbucketserverkb/how-do-you-make-changes-on-a-specific-commit-779171729.html)。

但是如果您需要为几次提交修复它——就像我多次做的那样——并且您不关心您的 git 历史，您可以使用这个脚本:

请注意，它会为每次更新的提交更改 git-sha。这被认为是一种非常糟糕的做法。只有在你知道风险的情况下才这么做。

```
#!/bin/sh

git filter-branch --env-filter '

OLD_EMAIL="your-old-email@example.com"
CORRECT_NAME="Your Correct Name"
CORRECT_EMAIL="your-correct-email@example.com"

if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]
then
export GIT_COMMITTER_NAME="$CORRECT_NAME"
export GIT_COMMITTER_EMAIL="$CORRECT_EMAIL"
fi
if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]
then
export GIT_AUTHOR_NAME="$CORRECT_NAME"
export GIT_AUTHOR_EMAIL="$CORRECT_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags
```

我在个人项目中运行过这个脚本几次，但是我从来没有在专业项目中运行过。小心点。如果你要用它，别忘了替换掉`OLD_EMAIL`、`CORRECT_NAME`和`CORRECT_EMAIL`。

# 3.向最后一次提交添加新的更改

想象一下。你刚刚完成了你需要做的改变。你感到自信:git 添加，git 提交，git 推送。然后你意识到某个单元测试中的一个小错误，你不想再创建一个提交来修复这个错误…

没问题！这可能是最简单的一个了！一个简单的`amend`就可以了。只需运行:

```
git commit --amend
```

然后将打开一个带有最后一条提交消息的文本编辑器。编辑消息，如果你想，保存并退出！完成了！

注意:这将重写上次提交的`git-sha`。

# 4.从 git 日志中删除提交

好了，现在你已经使用 git 有一段时间了，并且学会了进行小规模的独立提交。它让您在寻找问题时更容易，并且您的 git 历史看起来也更好。

然后你从一个业务涉众那里听说最近的一个实现需要撤销。你可能会想“没问题！”因为你有小而独立的提交，但是你会怎么做呢？

如果您试图只删除最后一次提交，这很容易，您只需运行:

```
git reset --hard HEAD^
```

但是假设您的 git 日志如下所示:

```
* 97c8db67 fix: commit 3 is correct as well
* 69e04606 feat: commit 2 is correct
* 9f130ee1 fix: commit 1 needs to be removed
```

如果您需要删除 *9f130ee1* *git-sha* (“需要删除提交 1”)，您可以执行交互式 rebase:

```
git rebase -i HEAD~3
```

其中 3 是在选择要删除的提交之前要查找的提交数。然后将打开一个文本编辑器，显示以下内容:

```
pick 9f130ee1 fix: commit 1 needs to be removed
pick 69e04606 fix: commit 2 is correct
pick 97c8db67 fix: commit 3 is correct as well
```

如果我们简单地将第一行中的“pick”替换为“drop ”,然后保存文件，就完成了！ *9f130ee1* 提交将会退出！

*   请注意，这与 [git 恢复](https://git-scm.com/docs/git-revert)不同，它将完全删除提交，并将重写 git 历史记录，如果您已经推送了它，这可能是一个糟糕的做法。
*   如果删除的提交之后的提交依赖于删除的提交，您可能需要手动解决冲突。

# 5.编辑旧的提交消息

您刚刚注意到一些提交消息中的一些拼写错误的单词，并且想要修复它们。

与上一个技巧非常相似，您可以运行:

```
git rebase --interactive HEAD~3
```

等到文本编辑器打开，用“reword”替换“pick ”,然后保存并退出。

使用 [git rebase](https://git-scm.com/docs/git-rebase) 可以做很多事情，编辑旧的提交消息就是其中之一。使用`git rebase --interactive` [在这里](https://docs.github.com/en/get-started/using-git/using-git-rebase-on-the-command-line)寻找更多选项。

# 6.如何从缺省分支重新建立基础

当我将主分支重置为我的`feature-branch`时，我从 [Tiago Marek](https://www.linkedin.com/in/tiago-alves-marek-3b8aaa79/) 那里学到了一个非常快速但有用的技巧:避免著名的合并提交，看起来像*“在我们的 git 历史上合并分支‘main’…*”。

不用点击一个非常诱人的按钮叫做“更新分支”或者只是使用`git pull origin main`，从你的`feature-branch`你可以:

```
git pull --rebase origin main
```

这将基本上:

1.  撤消本地提交
2.  从远程获取提交
3.  添加您的本地提交

如果有任何 git 冲突，您将被要求在推送之前解决它们。

# 7.获取两次提交之间的日志

我在排除故障时学到的另一个快速技巧(这次是从 [Thayse Onofrio](https://www.linkedin.com/in/thayseonofrio/) 那里学到的)是如何在两次不同的提交之间获取 git 日志。

```
git log --oneline 5a984958..49a7891a
```

这对于查看应用程序最后部署的版本与以前部署的版本之间的差异非常有用。如果您的情况是这样，您也可以使用 Github:

[https://github.com/user/your_repository/compare/5a984958..49a7891a](https://github.com/user/your_repository/compare/5a984958..49a7891a)

*   如果你把“..”在上面的链接上有“…”，你会有一个稍微不同的看法。

# 结论

这些是多次“拯救”我的命令。如果你有任何补充、询问或评论，请告诉我。感谢阅读。