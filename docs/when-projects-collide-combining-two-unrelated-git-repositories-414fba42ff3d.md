# 组合不相关的 Git 库:当项目冲突时！

> 原文：<https://betterprogramming.pub/when-projects-collide-combining-two-unrelated-git-repositories-414fba42ff3d>

## **如何合并两个 Git 仓库？**

![](img/baaf6cdb3d4c8b9fed3611257a19832b.png)

最近开始了一个项目，为 chrome 构建一个 AI 支持的书签扩展，使用单独的 git 库来创建内容:一个用于 Chrome 扩展，一个用于 React/TypeScript 前端。

我认为从独立的存储库开始会更简单，一旦每个部分都有了基本的功能，再将它们组合起来会更简单。最终，我需要组合这些库，以便在 chrome 扩展中使用[弹出菜单](https://developer.chrome.com/docs/extensions/mv3/user_interface/#popup)和[书签页面覆盖](https://developer.chrome.com/docs/extensions/mv3/override/)的前端。

我在谷歌上搜索，找到了这个关于如何将一个存储库合并到另一个存储库的简洁且投票良好的答案:

[](https://stackoverflow.com/questions/1425892/how-do-you-merge-two-git-repositories) [## 如何合并两个 Git 存储库？

### 基本上，我重写了我的插件库的历史，这样看起来所有的开发都发生在…

stackoverflow.com](https://stackoverflow.com/questions/1425892/how-do-you-merge-two-git-repositories) 

它绝对值得一读，而且从定义上来说，它是本文的 TLDR。

在仔细阅读了那里的评论后，我决定一行一行地阅读推荐的方法，并对照 [git 文档](https://git-scm.com/docs/)进行检查，以便更好地理解在他们所说的“[瓷器](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain)下发生了什么。你很幸运，我做了笔记。

以下是投票最高且被接受的将`project-a`合并到`project-b,`的解决方案供参考，其行号链接到以下解释:

```
[1](#f4b5) cd path/to/project-b
[2](#5529) git remote add project-a /path/to/project-a
[3](#03be) git fetch project-a --tags
[4](#eea7) git merge --allow-unrelated-histories project-a/master # or whichever branch you want to merge
[5](#8a88) git remote remove project-a
```

我们很容易就开始了:

```
[1](#0e01) cd path/to/project-b
```

`cd`命令将您的工作目录更改为`project-b`，这样我们就可以将`project-a`合并到其中。简单:)

```
[2](#0e01) git remote add project-a /path/to/project-a
```

[](https://git-scm.com/docs/git-remote) [## Git - git 远程文档

### 不带参数，显示现有遥控器的列表。有几个子命令可用于在…上执行操作

git-scm.com](https://git-scm.com/docs/git-remote) 

`git remote`命令“管理其分支被跟踪的一组存储库(“远程”)，我们稍后也会再次使用它。
`git remote add`从指定的路径或 url 添加命名的远程存储库。因此，我们在这里将`project-a`作为远程存储库从位置`/path/to/project-a`添加到`project-b`存储库中。

```
[3](#0e01) git fetch project-a --tags 
```

[](https://git-scm.com/docs/git-fetch) [## 获取文档

### 获取所有遥控器。-a - append 将提取的引用的引用名称和对象名称追加到…的现有内容中

git-scm.com](https://git-scm.com/docs/git-fetch) 

`git fetch` ‘从另一个存储库中下载对象和引用’。
`fetch`从指定的存储库中下载分支和标记(统称为“refs”)以及完成它们的历史所需的对象。任何指向引用历史的标签也被获取。
使用`--tags`选项将所有标签从远程‘refs/tags/’目录提取到本地存储库的同名标签中。由于这些项目都很年轻，我还没有开始对它们进行版本化，所以我没有任何有价值的标签来组合，所以我不打算在我的命令中包含— tags 标志。

> 关于标签的更多深度，请参考我在下面的[转移。](#0682)

```
[4](#0e01) git merge --allow-unrelated-histories project-a/master # or whichever branch you want to merge 
```

[](https://git-scm.com/docs/git-merge) [## git-git-合并文档

### 提交-不提交执行合并并提交结果。此选项可用于覆盖不提交。与……

git-scm.com](https://git-scm.com/docs/git-merge) 

在这里，我们用`git merge`得到了过程的实质，它将两个或更多的开发历史连接在一起。

`merge`命令将来自指定提交的更改合并到当前分支中。它将“重放”在指定提交的分支上所做的提交更改，因为它与当前分支不同，并将结果连同两个父提交的名称和来自用户的日志消息一起记录在新提交中。您可能已经知道，这是一个常见的场景，在一个存储库中有多个分支，这是 merge 命令通常被设计和使用的方式。它通过将命名提交分支中文件的状态与当前分支中相同文件的状态进行比较，并使用差分算法来查找它们的分歧点。当重叠的改变或“冲突”被呈现给用户以选择使用哪一版本的线时，非重叠的改变被自动做出。

与其深挖底层机制，我只指出[这个简单的答案](https://stackoverflow.com/questions/14961255/how-does-git-merge-work-in-details)是对 git 默认的三路合并算法的很好介绍，对[不同合并策略](https://git-scm.com/docs/merge-strategies)的很好概述，以及 [Atlassian 的有价值的教程](https://www.atlassian.com/git/tutorials/using-branches/git-merge)条目。

在我们的例子中，我们没有合并具有共享历史的分支，所以`--allow-unrelated-histories`标志明确地让我们合并没有共同祖先的两个历史。如果这些是同一个项目的不同版本，这将是一个潜在的灾难性操作，可能会使我们的工作树丢失提交，没有办法恢复，这就是引入标志的原因。然而，将两个独立的项目结合起来，这些问题就不可能了。

对于真正喜欢冒险的人来说，这里是`git merge`的实际实现[,其中带有字符串*‘allow _ needrable’*的页面中的快速查找将在代码中遍历到处理`--allow-unrelated-histories`标志的位置。](https://github.com/git/git/blob/74cc1aa55f30ed76424a0e7226ab519aa6265061/builtin/merge.c#L299)

> 从文档中得到的一个重要注意是，确保您的工作树对命名的和当前的分支(或者在我们的例子中是存储库)上的所有变更都是最新的，没有您可能关心的未完成的未分级的变更。如果不是这样，那些显著的变化就有可能丢失。

```
[5](#0e01) git remote remove project-a
```

最后，我们返回到`git remote`并使用`git remote remove`从`project-b`的远程跟踪中移除命名的`project-a`。从`project-b`中移除了`project-a`的所有远程跟踪分支和配置设置，因此我们现在合并的项目不再引用仍然存在的`project-a`。在我们成功地将`project-a`的文件和历史记录合并到`project-b`之后，通过解耦这两个存储库，我们的过程就完成了。

> 如果我们不想再保留原始`project-a`的副本，我们可以包含类似`rm -rf path/to/project-a`的行。

我清理了两个项目的工作树，然后运行这些命令来合并我的两个项目。就像回答评论里宣传的那样顺利，只有我的一行冲突。gitignore 文件很容易修复。如果你已经读到这里，我希望你有一些回复可以一起分享！祝你好运！

# Git 中标签的转移

在这里，我觉得有必要更新一下 git 中的标签功能。再一次，优秀的 git 文档展示了它。

[](https://git-scm.com/book/en/v2/Git-Basics-Tagging) [## Git 标签

### 像大多数 VCS 一样，Git 能够将存储库历史中的特定点标记为重要点。通常情况下…

git-scm.com](https://git-scm.com/book/en/v2/Git-Basics-Tagging) 

本质上，git 中的标记可以用来为特定的提交创建标签，允许您以后引用它。它们允许一个指向特定提交的规范引用点，而不是从特定提交开始然后跟踪更改的分支。这特别考虑到了版本化符号，因此特定的提交可以被标记为例如`v1.4`并在以后通过 git 命令引用。

有两种类型的标签:“轻量级”和“带注释的”。

轻量级仅存储标记名和对提交校验和的引用。它可以用于存储临时标签，作为对过渡状态的引用，或者通常存储不期望被维护/共享的标签。

带注释的标记包括一条消息和一个标记名，并在 git 数据库中存储一个完整的校验和对象，包括标记者的信息和标记日期。这是大多数标签的推荐标签类型，因为它提供了完整的信息，并且如果需要，可以由 GPG 签名和验证。

一个很好的特性是能够在事后标记提交，因此在版本控制的情况下，标记过程不必实时发生，而是可以单独管理。

当使用例如`git push`到远程时，标签是默认不共享的，所以就像我们的`git fetch --tags`调用一样，如果需要，存在一个`git push --tags`选项来将标签发送到远程存储库。