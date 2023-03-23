# 如何使用 Git 子树

> 原文：<https://betterprogramming.pub/git-subtree-usage-6aaba8b5d947>

## 其他 Git 存储库中的 Git 存储库

![](img/ca5eabd5d16aab3a0adc787095451a26.png)

[蒂姆·约翰逊](https://unsplash.com/@mangofantasy?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/branching?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

没有本机子树命令，它类似于一种允许将一个存储库嵌套在另一个存储库中作为子目录的方法。这是跟踪软件依赖历史的方法之一。

Git 子树是 Git 子模块的主要替代品。然而，子树不应该与子模块混淆。Git 子树是一个 Git 库的副本，它被放入父库。Git 子模块是指向另一个存储库中特定提交的指针。与子模块不同，子树不需要存储库中的`.gitmodules`文件或`gitlinks`。

# **利弊**

Git subtree 有时比子模块更有优势，当您在它们之间进行选择时，应该会遇到很多优势。然而，也有一些缺点。

## 赞成的意见

*   由旧版本的 Git 支持。它甚至可以处理 1.5.2 之前的版本。
*   简单的工作流管理
*   超级项目完成后可用的子项目代码
*   没有新的 Git 知识
*   修改内容，而不创建独立的依赖关系存储库副本

## 骗局

*   学习新的合并策略
*   复杂的子项目上游贡献代码
*   不要混合超级项目和子项目代码

# **如何使用 Git 子树**

让我们讨论一个简短的场景。假设有一个外部项目，您想要将它添加到您的存储库中。例如，要在存储您的`vim`设置的存储库中添加一个`vim`扩展，您应该这样做:

```
git subtree add — prefix .vim/bundle/example [https://github.com/Example/vim-example.git](https://github.com/Example/vim-example.git) master — squash
```

所举的例子将把`vim-example`项目的全部历史压缩到你的文件夹`.vim/bundle/example`中，记录下主人当时的 SHA-1，以备将来参考。

```
commit 6d7054b3gcea64e2e31f4d6fb2e3be12e5865e87
Merge: 87fa91e ef86deb
Author: Bob Brown<bob@brown.com>
Date: Tue Jun 23 13:37:03 2020 +0200
Merge commit ‘fe67ddf158faccff4082d78a25c45d8cd93e8ba8’ as ‘.vim/bundle/example’
commit fe67ddf158faccff4082d78a25c45d8cd93e8ba8
Author: Bob Brown<ann@brown.com>
Date: Tue May 22 13:37:03 2019 +0200
Squashed ‘.vim/bundle/example/’ content from commit b999b09
git-subtree-dir: .vim/bundle/example
git-subtree-split: b999b09cd9d69f359fa5668e81b09dcfde455cca
```

如果您调用 Git status，您将看不到任何东西，因为 Git subtree 已经为您创建了提交，并留下了干净的工作副本。此外，`vim/bundle/example`中没有任何内容表明该文件夹曾经来自另一个存储库。

# **拉动**

要将子文件夹更新到子存储库的最新版本，只需运行:

```
git subtree pull — prefix .vim/bundle/example https://github.com/Example/vim-example.git master — squash
```

请注意，参数保持不变，只是用`git pull`命令替换了`git add`命令，这将创建一组与前面的 add 类似的提交。

但是请记住，Git subtree 存储子项目提交的 id，而不是元数据中的引用。查找与提交相关的符号名:

`git ls-remote [https://github.com/Example/vim-example.git](https://github.com/Example/vim-example.git) | grep <sha-1>`

# 在 Git 子树后重置基础

在这里，您应该手动执行一个交互式 rebase 并删除`add`提交，然后执行 rebase。重置过程完成后，继续并重新执行`add`命令。

Git 子树对于 Git 初学者来说比较容易。代码被合并到外部存储库的历史中。只需要`clone`、`pull`、`push`，而且是分散的。Git subtree 允许您将另一个存储库集成到您的存储库中，包括历史。这就是为什么在集成之后，您的存储库变得更大了。集成后，没有到其他存储库的连接，除非更新它，否则不需要访问它。虽然它被认为是 Git 子模块的一个很好的替代品，但是它们都是为不同的工作流设计的。