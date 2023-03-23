# 有效工作的 5 个 Git 实践

> 原文：<https://betterprogramming.pub/5-git-practices-for-effective-work-b612e5430bc7>

## 尽早承诺，经常承诺

![](img/d65a53953cc7df9d9a3fd97914a37d94.png)

照片由[阿齐兹·阿查基](https://unsplash.com/@acharki95?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄

Git 的主要优势之一是灵活性。Git 使处理大小项目成为可能，并使团队成员的工作流程更加顺畅。跟踪变更、维护源代码备份、支持团队协作以及用一个命令在服务器上部署源代码被认为是 Git 性能的强大之处。

然而，有很多你甚至猜不到的东西对于高效工作来说是必不可少的。为了提高你的工作效率，达到更好的效果，我们建议你在工作时考虑以下几点。

# **1。进行干净提交**

你们中的许多人在修复一个特定的 bug 时，可能会发现另一个 bug，并试图在一次`[git commit](https://www.w3docs.com/learn-git/git-commit.html)`中解决所有问题。当然，这不是一个好习惯，可能会给你和你的团队成员带来问题。永远记住，修复两个不同的 bug 应该产生两个独立的提交。保持提交的整洁，并且只为一个目的提交一次。它将帮助其他人高效地检查您的更改，并进行代码审查。

此外，不要忘记编写好的提交消息(关于这个主题，我们已经贡献了整整一篇[文章](https://medium.com/better-programming/how-to-write-good-commit-messages-and-why-is-it-important-18ac406ce33a))。为此，Git 提供了 staging area (index)之类的工具，并且能够只存放文件的某些部分。

# **2。尽早并经常提交**

当你提交时，Git 对你的数据负责。不要等到你得到一个完美的提交。将功能实现分成小块，并经常提交。如果你需要更多一点的时间来完成你的工作，只要保持最新的变化，以免陷入冲突。如果您尽早且经常提交，您不会丢失工作或恢复更改，并且您可以在使用`[git reflog](https://www.w3docs.com/learn-git/git-reflog.html)`命令的同时跟踪您的工作。但是，不要提交半途而废的代码。如果您想提交只是因为您需要一个干净的工作副本，只需使用`[git stash](https://www.w3docs.com/learn-git/git-stash.html)`命令即可！

# **3。不要更改已发布的历史**

修改已经出版的历史是一种不好的做法。为什么？我们来解释一下。一旦提交被合并到上游分支，并且对其他程序员可见，不建议更改历史，以避免冲突。如果你后来发现你丢弃了你的提交，你可以创建新的提交来解决这个问题，例如通过`[git revert](https://www.w3docs.com/learn-git/git-revert.html)`。然而，有些时候，如果管理得当，修改已发表的历史可能是一件正常的事情。

# **4。使用别名**

`[Git alias](https://www.w3docs.com/learn-git/git-alias.html)`是创建短命令映射长命令的快捷方式。运行一个命令只需要几次击键，这简化了程序员的工作。例如，我们运行:

`git commit -m “commit message”`

相反，我们可以键入:

`git c -m “commit message”`

每次剪几个字母，肯定会节省很多时间！查看我们的文章[节省你时间的 7 个技巧](https://medium.com/better-programming/7-git-tricks-to-save-your-time-dacc94a90c2c)。

# **5。在交互模式下使用 Git Rebase**

当您的特性分支准备集成到主特性中时，有很多场景，但是大量的提交不允许您合并它。

在您的分支中可能有许多提交，但是一旦您想要将它合并到主分支中，您应该将这些提交压缩成一个干净的提交。 [Git rebase](https://www.w3docs.com/learn-git/git-rebase.html) interactive 来帮你:

`git rebase — interactive <base>`

由于这种神奇的模式，您可以通过过滤和修改现有的提交序列来清除历史记录。

总而言之，我们无疑可以说 Git 是一个灵活的工具，它提供了几乎所有的东西来简化您的工作流。这有点像一个神奇的世界，我们正试图为你深入探索。