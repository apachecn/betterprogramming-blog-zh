# 4 个方便的 Git 命令，用于修复常见的代码库问题

> 原文：<https://betterprogramming.pub/4-handy-git-commands-for-fixing-common-code-base-problems-5996ff853c9>

## 并防止新的问题发生

![](img/55bd50fada12f87c871fbdd5466b54f2.png)

Yancy Min 在 [Unsplash](https://unsplash.com/s/photos/git?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片。

Git 是我们日常编程的重要组成部分，它解决了我们的许多问题——尤其是在与团队合作时。每个软件开发人员都应该熟悉它。

但是，如果你不小心犯了一个错误，并且不知道解决问题的选择，它可能会造成混乱，并增加你的血压。我记得我推了一个错误的提交，删除了主分支上的许多文件。在 Google 和 Stack Overflow 上搜索了几个小时后，我终于解决了这个问题。这一天仍然让我做噩梦。

解决这个问题后，我意识到在 Git 上一切皆有可能。我们只需要能够在不太焦虑的情况下搜索它。

从那以后，每次遇到问题，我都会把解决问题的步骤记录下来。它节省了我大量的时间，否则我会花在寻找解决方案上。下面是我遇到的一些主要问题，以及我如何用一组 Git 命令解决这些问题。

# Git 回购的全新开始

想象一下，你发现组织中的另一个团队已经完成了分配给你的工作。或者你找到一个你可以使用的模板。您可以克隆 repo 并开始进行特定的更改，但是您意识到有一个小问题:Git 历史包括所有以前的提交，这是您不希望在新的 repo 中出现的。

在过去的一年里，我亲自做了三次。下面是做这件事的步骤。

## 删除历史记录

```
cd ClonedRepo
$ rm -rf .git
```

## 启动新的回购

这将初始化存储库，添加所需的文件，并进行第一次提交:

```
git init
git add .
git commit -m "Initial commit to new repo"
```

## 更新遥控器

您可以选择验证遥控器，以确保:

```
git remote -v
git remote add origin git@<remote>
git remote -v
```

## 推至远程

```
git push -u — force origin master
```

这样，一个全新的回购协议就产生了，你就有了一个新的开始。

# 暂时隐藏更改

比方说，当您注意到同一个类(或文件)中有一个 bug 时，您正在处理一个特性增强。修复 bug 的更改很小，但是您不确定如何提交更改。

您是否应该修复 bug，然后将其与当前的功能增强一起提交？如果是的话，那么你就混合了两个不相关的变化。这不是一个好的做法。这可能会给以后想要查看更改的人造成一些混乱。

更好的方法是将这两种变化分开。为此，您可以继续改进，并在完成后提醒自己进行 bug 修复。但是有一个问题:你可能会忘记。

你要做的是保存(或搁置)当前的更改，以后再来处理它们。Git 对此有一个命令。是的，你猜对了— `[stash](https://git-scm.com/docs/git-stash)`。

> "`git stash`暂时搁置(或*隐藏*)您对工作副本所做的更改，以便您可以处理其他内容，然后回来重新应用它们。如果您需要快速切换上下文(或分支)并处理其他事情，但是您正在进行代码更改，还没有准备好提交，那么隐藏是很方便的。”— [大西洋人](https://www.atlassian.com/git/tutorials/saving-changes/git-stash)

这是它的工作原理。

## 保存当前的更改

```
git stash
Saved working directory and index state WIP on master: 342ae3b <Message>
```

它在响应结束时返回 SHA1 和最近提交的消息。

## 检索更改

```
git stash pop
```

在您完成了对 bug 修复的更改之后，上面的命令将帮助您返回您隐藏它时的状态。

如果您已经隐藏了多次，请在检索所需的更改之前，使用 list 命令获取所有已保存更改的列表:

```
git stash list
```

# 撤消推送的合并

有时，您会遇到这样的情况，您不小心合并了一些代码，却发现其中有一条敏感信息或其他问题，您希望在任何人看到它并提出问题之前立即恢复它。

不要担心！您可以在 Git 上恢复一切。按照这些步骤，假装什么都没发生。

## 看看主分支

这是为了确保考虑对主要分支机构的任何更改:

```
git checkout master
```

## 撤消更改

使用以下命令生成提交的哈希:

```
git *log* --oneline8827df9 (HEAD -> stag, origin/stag)Enabled caching
ecb05df Added query logging Changed priority pool
b2c9e66 Increase memory Copy middlemanager jvm to Cluster
d0a5f85 Merge pull request #3 from stag
...
```

然后使用哈希来恢复更改:

```
git revert -m 1 [commit-hash]
```

`git revert` 命令将生成一个提交，将分支的状态恢复到错误合并之前的状态。

恢复更改是混乱的。最好的办法是在未来防止这类问题——也许是在合并前做一次公关。

# 重写历史

您刚刚完成了为冲刺所做的所有更改。所有的代码修改都已完成，所有的测试用例都运行良好。新功能完美运行，你欣喜若狂。您提交更改。

就在您点击 commit 时，您意识到您忘记了向 Git 添加一个配置文件。没有这一点，整个过程将会失败。再次承诺是可以的，但会在你刚刚完成的漂亮工作上留下印记。

Git 的伟大之处在于，它不仅允许您控制一切，还为您提供了重写历史的选项。您可以通过使用`git commit –amend`命令来修改您刚才所做的提交。

## 修改变更

如果您忘记添加文件，并且想要更新提交，您可以这样做:

```
git add config_file.json
git commit — amend — no-edit
```

这将删除旧的提交并用新的替换它，但将保持消息完整。

如果您只想更新提交消息，可以这样做:

```
git commit — amend -m "Revised Commit message"
```

# 结论

我希望这些命令对您有用。可能有其他方法来解决这些问题，但是这些是我在使用 Git 时遇到的常见问题以及我如何解决它们。

如果你在 Git 上遇到过类似的问题，欢迎在下面的评论中分享。