# 为什么知道 Git 藏匿是必须的

> 原文：<https://betterprogramming.pub/why-it-is-important-to-master-git-stash-eb95b88c8f28>

## 收起你的代码并正确应用它

![](img/a45a09e5c2969ed0707f0a3bad4329d1.png)

由[米哈伊尔·瓦西里耶夫](https://unsplash.com/@miklevasilyev?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/hide?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄。

作为一名开发人员，对 Git 或任何版本控制软件的良好了解不仅仅是一项技能——这是必须的。在 Git 提供的许多好东西中，有 [Git Stash](https://git-scm.com/docs/git-stash) 。为什么很棒？因为它防止您在同时处理几个 Git 分支时提交每一个微小的更改。

作为初级开发人员发现它对我帮助很大，所以这里是主要命令的基本概要以及它们有用的方式。

在一个理想的开发人员工作日中，人们应该能够专注于一个任务直到完成，然后切换到下一个任务。但在现实中，总有一些较小的紧急问题需要立即关注，而更长期的工作需要更多的思考和尝试。

处理这种情况的一种方法是在切换分支之前提交所有内容，但是这样会增加提交次数，同时破坏逻辑，从而使您的工作变得更脏。不理想。

所以让我们用`git stash`来代替，并学习使用它的命令集。

# 主要命令

## Git 贮藏

它“隐藏”了你当前所有的改变。换句话说，它将它们保存在一个列表中供以后使用。通常，如果不想提交，这是在切换到另一个分支之前要做的事情。请注意，只有被跟踪的文件将被隐藏。

## Git 存储列表

`git stash list`输出你的物品清单。每个藏毒点都有一个数字，零是最后一个。这些贮藏以后进先出的方式组织成一个栈，所以你可以把它想象成一个数组，每个额外的贮藏由`unshift`添加。现有存储的所有索引增加 1，最后一个存储保存在索引 0 处。

输出可能如下所示:

```
stash@{0}: WIP on feat-branch-1: 182e9e8d20 refactor profile stash@{1}: WIP on feat-branch-1: 182e9e8d20 add user custom pic
stash@{2}: WIP on feat-branch-2: 8566da5a18 add tests
stash@{3}: WIP on development: dcf86c7943 merge latest features
```

您可以看到`stash`索引、当您创建变更时正在处理的分支的名称、散列和前一次提交的名称。这有助于在你的存货清单增加时保留参考。但是，这些更改可能与之前的提交无关。

## Git 贮藏汽水

`git stash pop`应用最后一次存储(又名`stash@{0}`)的更改，并从列表中删除它们(相当于移动`stash`数组的`0`元素)。当您想要提交更改并同时保持一个干净的存储列表时，这很方便。

## Git 存储应用

`git stash apply`应用与上面相同的更改，但不从列表中删除存储，因此您可以稍后或在不同的分支上重用这些更改。

## 应用或弹出选定的索引

如果您需要应用或弹出除最后一个以外的任何其他存储，请在命令的末尾添加索引:

```
git stash apply 1git stash pop 2
```

## Git 隐藏清除

重置你的整个藏匿清单。之后运行`git stash list`命令时，终端应该什么都不输出。

## Git 藏匿点

运行`git stash drop`,后跟 stash 的索引，只会删除单个条目。

# 结论

正如您所看到的，当隐藏变更的分支数量增加时，它很容易变得混乱，所以建议定期清理列表。

我列出了我最常用的命令，但是要获得关于 Git Stash 的全面指南，请随意查看官方文档。