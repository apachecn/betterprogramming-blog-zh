# 我几乎每天都在使用的 16 个 Git 命令

> 原文：<https://betterprogramming.pub/git-commands-that-i-use-almost-on-daily-basis-33fbef3c5f62>

## 有助于加快工作流程的终端命令

![](img/55bd50fada12f87c871fbdd5466b54f2.png)

[Yancy Min](https://unsplash.com/@yancymin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/git?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片。

Git 可能是当今使用最广泛的版本控制系统。尽管有像 Bitbucket 这样的替代方案，但每当提到项目时，您至少会听说过 Git。除了 Git 之外，我还使用过其他选项，比如 GitHub、GitLab、Bitbucket 和 SVN。然而，在本文中，我们将研究我最常用的 Git 命令以及您可能会遇到的情况。

这些 Git 命令通常与终端一起使用，用户可以通过输入某些命令来完成任务。如果你是一个 GUI 类型的人，你可能对此不感兴趣。但是如果你有兴趣使用终端像 Hackerman 一样键入一堆命令，并被认为很酷，你可以继续阅读。

玩笑归玩笑，我已经试着为你编了一个完整的清单，并对每一个都做了快速的解释，这样你更容易参考。有许多不同排列的 Git 命令(即当每个命令应用不同的标志时)。然而，有一个子集，人们通常每次都会使用。

所以，让我们开始吧！

*注意:在您的终端中运行* `*git [command]- — help*` *(如* `*git init --help*` *)可以随时获得更多信息。*

以下是我通常使用或至少会遇到的命令列表:

1.  `git init`
2.  `git clone`
3.  `git branch`
4.  `git checkout`
5.  `git status`
6.  `git fetch`
7.  `git pull`
8.  `git add`
9.  `git commit`
10.  `git push`
11.  `git remote`
12.  `git merge`
13.  `git log`
14.  `git stash`
15.  `git reset`
16.  `git config`

# 1.git 初始化

这是为了初始化一个空的 Git 库。基本上，它是在您想要的目录中创建一个空的 Git 存储库。生成一个`.git`目录及其子目录。

一旦完成，您就可以开始在目录中使用 Git 命令了。

```
$ git init
```

# 2.git 克隆

这是从远程存储库(如 Github 等)克隆或下载源代码。)到您的目录中。

它基本上是在远程存储库中制作一个项目最新版本的相同副本，并将其保存到您的计算机上。

我通常使用链接克隆，这更熟悉。

要将项目存储库克隆到主分支中，运行`$ git clone [YOUR_HTTPS_LINK]`。

例如:

```
$ git clone [https://github.com/randomname/awesome-project-example.git](https://github.com/sorrycc/awesome-javascript.git)
```

要将链接克隆到一个新的分支中，可以运行下面的命令:
`$ git clone [YOUR_HTTPS_LINK] -b [new-branch-name]`。

例如:

```
$ git clone [https://github.com/randomname/awesome-project-example.git](https://github.com/sorrycc/awesome-javascript.git) -b my-branch
```

# 3.git 分支

这将列出本地所有可用的 Git 分支。

这是一个常用的 Git 命令。我每次都使用它——尤其是当我必须处理位于不同目录中的不同内容，并且每个目录都有不同的分支时。

分支对于协作非常重要，也有助于维护主分支，防止它被未完成或未测试的代码弄乱。

您可以使用`git branch`命令来创建、列出和删除分支。

要创建一个新的分支(本地)，使用`$ git branch [new-branch-name]`。

例如:

```
$ git branch new-branch
```

为了确保远程存储库也有这个分支，通过运行`$ git push -u [remote-origin] [your-new-branch]`来推送它。

例如:

```
$ git push -u origin “feature/new-feature”
```

要列出您所有的本地分支并检查您现在所在的分支，请运行
`$ git branch`或`$ git branch --list`。

您当前所在的分支通常会突出显示。

要删除您的本地分支，运行`$ git branch -d [your-unwanted-branch]`。

例如:

```
$ git branch -d myBranch
```

# 4.git 检验

这也是最常用的命令之一——尤其是在处理不同的分支时。

这通常在你想切换到另一个分支时使用。您还可以使用它来检查文件和提交。

要切换到另一个分支，运行`$ git checkout [another-branch-name]`。

例如:

```
$ git checkout myBranch
```

要创建一个新的分支，然后切换到那个分支，运行`$ git checkout -b [new-branch]`。

例如:

```
$ git checkout -b myBranch
```

上面的命令是一个快捷方式，首先创建一个带有`-b`标志的分支，然后检查它以进行切换。

要从一个特定的远程分支创建一个新的分支，您可以运行`$ git checkout -b [your-new-branch] [origin-remote-branch]`。

例如:

```
$ git checkout -b dev “origin/dev”
```

您可以根据以下标准切换到另一个分支:

*   在切换之前，必须提交或隐藏当前分支中的更改。
*   您要签出的分支应该存在于您的本地。

另一个用途是，如果您更改了分支中的文件，并希望恢复到原始版本。运行`$ git checkout [YOUR_FILE_PATH]`。

或者我通常只运行`$ git checkout [YOUR_FILE_PATH] --`。

# 5.git 状态

这为我们提供了关于当前分支的所有必要信息。

信息包括:

1.  当前分支是否是最新的。
2.  是否有任何要提交、推或拉的内容。
3.  是否有暂存、未暂存或未跟踪的文件。
4.  是否创建、修改或删除了文件。

跑`$ git status`。

该流程通常需要首先暂存具有更改(创建、修改或删除)的文件，然后您可以通过将所有文件添加到该阶段来提交它。提交之后，您可以将文件推送到远程存储库。

# 6.获取 git

要从您的远程存储库中获取项目的更新，运行`$ git fetch`。

这只获取本地和远程列出的可用分支的更新。要获取所有远程分支的所有更新，您可以运行`$ git fetch --all`。

请注意，这不会将更改立即应用到您的本地文件。

# 7.git 拉

比起`git fetch`命令，我更经常使用这个命令。

这个命令也可以从远程存储库中获取更新，只不过它类似于`git fetch`和`git merge`的组合。这意味着获取更新后，它会立即将更改应用到您的本地文件。

```
$ git pull
```

也可以运行这个命令:`$ git pull [remote]`。

例如:

```
$ git pull “origin/master”
```

否则，您可以像这样具体针对哪个远程分支到哪个本地分支:`$ git pull [remote-branch] [local-branch]`。

例如:

```
$ git pull “origin/dev” dev
```

我建议在你推进你的改变之前总是做一个`git pull`，因为冲突可能会发生。

因此，最好是将更改加载到您的本地计算机上，并首先解决任何冲突。然后你可以把它向上推，以便以后有一个平滑的合并过程。这也防止了你可能给队友带来的任何麻烦。

# 8.git 添加

当更改完成时，无论是创建、删除还是修改，都是在本地完成的，因此还没有远程反映出来。

通常，在更改完成后，您需要将文件暂存起来，然后运行`git commit`命令，真正将您更改的文件提交到更改中——或者在某种意义上保存。然后，您执行`git push`操作，将变更推送到远程存储库，以便远程反映变更。

因此，第一步是首先将您的文件放在 stage 中。怎么会？通过运行
`$ git add`。

此命令用于将所有文件添加到舞台。或者，你也可以这样做:

```
$ git add -A
```

要添加单个文件，只需运行`$ git add [FILE_PATH]`。

# 9.git 提交

这可能是最常用的命令。每次你想保存你的更改，你必须使用这个。当您想要推送您的更改时，您必须首先使用这个命令。即使您不打算推动您的更改，您仍然会使用这个命令。

有时，在解决一个问题或完成一项任务后，您会首先提交它，以确保通过设置检查点来本地锁定更改。这很像是一种主食。

有两种方法可以做到这一点:

1.  发短信承诺:`$ git commit -m “commit message”`。
2.  用更长的消息提交:`$ git commit`。

这个命令将为您打开一个编辑器，然后编辑您的提交消息。

如果您想编辑您的提交消息，也有两种方法可以做到:

1.  编辑最近提交消息的捷径:`$ git commit --amend -m “new commit message”`。
2.  以更长的方式编辑最近的提交消息:`$ git commit --amend`。

这将为您打开一个编辑器来编辑提交消息。

如果您正在更改已提交的提交消息，您需要通过运行`$ git push [remote-branch] [local-branch] --force`或`$ git push [remote-branch] [local-branch] -f`再次强制重新提交。

从本地向远程强制推送时要小心！

# 10.git 推送

在提交您的更改后运行此命令，将您的更改发送到远程服务器:

```
$ git push
```

或者，您可以运行以下命令来更具体地了解哪个分支:

```
$ git push [remote-branch] [local-branch]
```

例如:

```
$ git push “origin/dev” dev
```

但是，如果您没有跟踪您的分支，因为它是新创建的，那么您可以运行这些:`$ git push --set-upstream [remote-branch] [your-new-branch]`或`$ git push -u [remote-branch] [your-new-branch]`。

# 11.git 遥控器

要显示本地机器可用的所有远程 Git URLs，运行`$ git remote`。

但是，它通常不显示 URL，只显示 URL 的名称或别名。因此，为了显示所有内容，包括与名称相关的 URL，运行`$ git remote --verbose`或`$ git remote -v`。

如果您想查看完整的输出，并且您所在的网络可以到达源所在的远程 repo，请运行`$ git remote show origin`。

克隆存储库时，通常已经跟踪了一个源远程。但是，如果您想添加一个新的来指向您的本地存储库以跟踪远程存储库，您可以运行`$ git remote add [name-alias-for-url] [remote-git-url]`。

如果出于某种原因想删除远程 URL，可以运行`$ git remote -rm [name-alias-for-url]`。

例如:

```
$ git remote -rm origin
```

当克隆一个存储库(从 GitHub 或任何源存储库)时，克隆源的默认名称通常是`origin`。

# 12.git 合并

这是将分支与父分支或主分支合并(无论主分支使用哪一个)。通常，当你已经完成了开发，并且本地一切正常时，就可以进行测试了，下一步是进行其他类型的测试，比如集成测试或其他。

您必须确保您处于正在变化的当前分支中。然后你跑`$ git merge [parent-branch]`。

所发生的是你当前的分支或者特性分支被合并到父分支中，父分支可以是`master`或者`dev`用于进一步的测试。

# 13.git 日志

这是为了查看当前分支的提交历史。它显示最新的历史信息。您可以看到作者、日期、合并代码和提交消息。

```
$ git log
```

# 14.git 贮藏

假设您已经进行了更改，并且想要保存它们，然后想要将状态恢复到您没有对工作分支进行更改时的状态。您可以通过运行`$ git stash`来实现这一点。

顾名思义:它隐藏或存储您的更改。然后你可以继续做别的事情。

有时，当有您还不想提交的变更，但是由于某种原因您需要签出另一个分支时，您可能想要这样做，但是如果不提交或推送您的变更，您就不能签出。

这个命令可以派上用场。

您可以使用它来检查您隐藏的更改:

```
$ git stash list
```

然后，您可以通过运行`$ git stash pop`将它从存储库弹出到您的工作分支。

# 15.git 重置

这是为了将本地分支的当前`HEAD`或更改重置为特定状态。

要重置您的更改，您可以运行`$ git reset [FILE_PATH]`。

它还提供了两个可以重置的选项:

1.  硬:更改被丢弃或删除(`$ git reset --hard`)。
2.  软:改为分阶段进行更改(`$ git reset --soft`)。

# 16.git 配置

我通常使用它来为我的 Git 全局设置我的用户电子邮件和用户名配置，如下所示:

```
$ git config --global user.name “yourName”$ git config --global user.email [yourEmailAddress@example.com](mailto:yourEmailAddress@example.com)
```

# 结论

这乍一看似乎令人不知所措——尤其是当您刚刚开始您的旅程并看到这一长串不同的命令选项时。然而，随着您参与更多频繁使用 Git 的项目，您将逐渐开始注意到您每天是如何反复使用一些命令的。当你开始把它们列出来的时候，你会注意到你自己已经使用了很多命令。最终，你会对这些了如指掌。

最后，永远记住做这些事情:

1.  在你想要推你的改变之前获取。
2.  然后从遥控器中取出更改(如果有的话)。
3.  然后添加您的文件。
4.  提交您的文件。
5.  将您的更改推送到遥控器。

如果有任何冲突，记得解决。不要固执地在没有从远程存储库中提取变更的情况下就将变更推上来。这不仅会给你自己的项目带来问题，也会给你的团队成员带来问题。

一个清晰的提交信息将有助于你的项目的长期发展，我祝你在激动人心的旅程中一切顺利！

今天的单子到此为止。如果您有其他本文没有讨论的通用 Git 命令，请在下面的评论部分分享它们！我们都喜欢一起学习。

感谢阅读。