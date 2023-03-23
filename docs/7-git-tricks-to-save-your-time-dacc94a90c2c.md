# 节省你时间的 7 个窍门

> 原文：<https://betterprogramming.pub/7-git-tricks-to-save-your-time-dacc94a90c2c>

## 从通过关键字搜索提交到列出忘记的别名，等等

![](img/14ea3c1d0e4472e199b1404ad41189b4.png)

照片由[韦恩·洛](https://unsplash.com/@wayneshin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/magic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在过去的几年里，Git 已经成为最广泛的版本控制系统之一，也是程序员最好的朋友。在其他 VCS 中， [Git](https://www.w3docs.com/learn-git.html) 是目前最受欢迎的一个，它提供了各种工具来管理项目，从小到大的代码库。

Git 不仅仅是[创建分支](https://www.w3docs.com/learn-git/git-branch.html)和[推动变更](https://www.w3docs.com/learn-git/git-push.html)。基本命令后跟各种参数可以创建神奇的东西，节省您的时间和精力。总有很多值得学习和改进的地方。拥有正确的知识，你可以掌握 Git 并成为一名伟大的专家！

这里有几个 Git 建议的你可能不知道的高超技巧。探索、尝试并与同事分享。

# **1。重写上次提交消息**

`[git commit — amend](https://www.w3docs.com/learn-git/git-commit-amend.html)`是一个方便的提示，可以帮助修改最近的`[git commit](https://www.w3docs.com/search?q=commit)`。最好的一点是，它将分阶段的更改与之前的提交相结合，而不是创建一个全新的提交。您可以编辑之前的[提交消息](https://www.w3docs.com/snippets/git/how-to-change-commit-message.html)而无需更改快照:

`git commit — amend`

# **2。通过关键字**搜索提交

你如何找到你正在寻找的那段代码呢？`[git log](https://www.w3docs.com/learn-git/git-log.html)`命令会为您找到它。后跟`-S` 选项，它列出了基于给定字符串的提交:

`git log -S”config.menuItems”`

# **3。查看特定日期范围的日志**

看起来`git log`命令可以让你的工作更加容易。该命令推荐的另一个有用的特性是显示在指定日期 [Git 存储库](https://www.w3docs.com/learn-git/git-repository.html)中发生了什么变化。假设您想查看 2019 年 12 月 20 日至 2020 年 1 月 20 日之间发生了什么变化，只需附上 `— since`和`— until`旗帜，瞧:

`git log — since=’DEC 20 2019' — until=’JAN 20 2020'`

# **4。查看没有合并提交的日志**

虽然`[git merge](https://www.w3docs.com/search?q=git+merge)`命令是你工作流程的重要部分。合并提交每次都很烦人。因此，您可以使用以下选项来隔离它们:

`git log — oneline — no-merges`

# **5。列出遗忘的别名**

一个`[git alias](https://www.w3docs.com/learn-git/git-alias.html)`被配置来简化开发者的工作。运行一个命令需要更少的击键次数。但是，有时您可能会给命令取别名，然后忘记它们。As direct `git alias`命令不存在，而是通过`[git config](https://www.w3docs.com/learn-git/git-config.html)`命令和 git 配置文件创建的。

以下是您可以列出忘记的 git 别名的方法:

`git config -l | grep alias | sed ‘s/^alias\.//g’`

# **6。撤销共享历史记录**

Git 中存在的黄金法则之一是永远不要修改已经共享的历史。更改共享的历史可能会导致巨大的问题，因为其他团队成员并不知道这些更改。但是，您可以使用`[git revert](https://www.w3docs.com/learn-git/git-revert.html)`命令来[撤销一个推式提交](https://www.w3docs.com/snippets/git/how-to-undo-pushed-commits.html)。这将创建一个新的`[git commit](https://www.w3docs.com/learn-git/git-commit.html)`,恢复特定提交的更改，但不恢复随后的提交:

`git revert <commit hash>`

# **7。隐藏您的更改**

肯定有这样的时候，当你实现一个特性时，你的工作目录被改变搞得一团糟。为了[隐藏您的变更](https://www.w3docs.com/snippets/git/how-to-stash-git-changes.html)，您可以简单地创建一个新的分支，并将您的变更提交到那个分支。然而，还有另一个绝妙的解决方案，即`[git stash](https://www.w3docs.com/learn-git/git-stash.html)`。该命令获取未提交的暂存和未暂存的更改，保存它们以备将来使用，并清理您的工作目录和暂存区域。运行`git stash`，给你的目录一个整洁的外观:

`git stash`

Git 提供了许多未公开的特性，这些特性是每个程序员都必须知道的，以加速工作并正确完成任务。有了 Git，几乎一切皆有可能。命令-选项组合可以做神奇的事情，这就是安心。查看更多的 [git 命令](https://www.w3docs.com/learn-git.html)和[片段](https://www.w3docs.com/snippets/git.html)，进入 git 世界探索自己的新想法！