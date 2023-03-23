# 在 Git 中撤消

> 原文：<https://betterprogramming.pub/undoing-in-git-39e4404c0e92>

## 如何改正你的错误

![](img/291cf523c0387ec147c9a4d1fd6b0fd8.png)

照片由[德拉诺尔 S](https://unsplash.com/@dlanor_s?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

我们总是强调 [Git](https://www.w3docs.com/learn-git.html) 为你提供的无数机会，今天的文章也不例外。Git 以其撤销几乎所有事情的超级神奇能力而闻名！你可能还记得成千上万个悲伤的案例，在这些案例中，你合并了，然后意识到你并不是有意这样做的。即使你认为误合并是人生最大的失败，也要深呼吸，把文章读完。

也就是说，Git 中没有传统的撤销命令。撤销是通过其他一些命令完成的，这将是本文的主题。我们将涵盖五大撤销案例，帮助你改正错误，继续前进！

# **撤销 Git 添加**

Git 社区中最常见的问题之一是“如何在提交前撤销`git add`”您可以撤消单个文件或所有更改。

这需要一个超级简单的解决方案。要撤销单个文件，只需调用`[git reset](https://www.w3docs.com/learn-git/git-reset.html)`命令:

```
git reset <file>
```

要撤消所有更改，请运行以下命令:

```
git reset
```

如果你提交得太早而忘记添加更多的文件，撤销是不可避免的。在这种情况下，使用`amend`命令进行更改、暂存并再次提交。查看[如何撤销 Git Add](https://www.w3docs.com/snippets/git/how-to-undo-git-add.html) 片段，了解修改是如何完成的。

# **如何撤销 Git 合并**

合并会让你陷入不必要的境地。但是正如我们已经告诉你的，你可以很容易地改正你的错误！让我们想象一个场景，其中您已经提交了您的更改，并意识到您必须撤销该合并。

又来了一个撤销型命令——`[git revert](https://www.w3docs.com/learn-git/git-revert.html)`。首先使用`[git checkout](https://www.w3docs.com/learn-git/git-checkout.html)`检查主文件:

```
git checkout master
```

下一步是运行`[git log](https://www.w3docs.com/learn-git/git-log.html)`来获取合并提交的 ID:

```
git log
```

然后通过如下操作恢复提交:

```
git revert -m 1 <merge-commit>
```

您命令 Git 使用 `-m 1`恢复到主分支合并提交的第一个父级。例如，`-m 2` 将使 Git 恢复到最初合并的第一个父级。

此外，请阅读这个代码片段来了解[如何撤销未推送的 Git 合并](https://www.w3docs.com/snippets/git/how-to-undo-git-merge.html)。

Git 还建议[预览 opportunity](https://www.w3docs.com/snippets/git/how-to-preview-a-merge-in-git.html) ，以防你想知道当你合并分支时会发生什么。

# **如何撤销 Git 复位**

在这里，我们建议一个超短的方法来解开`git reset`:

```
git reset ‘HEAD@{1}’
```

然后，您可以运行`[git reflog](https://www.w3docs.com/learn-git/git-reflog.html)`命令来查看所有引用更新的日志(例如，检出、重置、提交、合并):

```
git reflog
```

查看一个简单的用例。

# **撤销 Git 中最近的提交**

有几种方法可以解开`[git commit](https://www.w3docs.com/learn-git/git-commit.html)`。让我们逐一考虑一下。

`git reset`命令可用于撤销您的更改:

```
git reset — soft HEAD~x (or git reset — soft commit hash).git reset — soft HEAD~x
```

键入一个数字而不是`~x`。例如，如果指定`~4`，该命令将影响倒数第四个提交。如果不指定任何数字，`git reset — soft HEAD`将改变最近一次提交。

当您使用`git reset — soft HEAD`命令时，您只需撤销最后一次提交，但是您所做的更改将被存储在您的工作树和索引中。所以`git commit`将会创建一个提交，其中包含与您之前“撤销”的提交相同的更改。

另一种方法是`git revert HEAD~x` ( `git reset — hard commit hash`)命令，该命令恢复由`HEAD`中最后一次提交指定的更改，并使用恢复的更改创建新的提交:

```
git revert HEAD ~x
```

当使用公共共享存储库时，这种方法是最好的。

# **撤销 Git 重置基础**

假设您已经在本地 [git 分支](https://www.w3docs.com/learn-git/git-branch.html)上执行了`[git rebase](https://www.w3docs.com/learn-git/git-rebase.html)`命令，并将其推送到远程。然后你意识到这不是你所期望的，现在你必须撤销它。

最简单的方法是通过运行以下命令找到分支的 head commit:

```
git reflog
```

接下来，需要使用`git reset`重置当前分支。确保在运行带有`— hard`选项的`git reset`命令之前，如下所示:

```
git reset — hard HEAD@{5}
```

在这里，老犯是`HEAD5`。

# 结论

这就是如何撤销 Git 中最常用的命令。虽然没有传统的撤销命令，但其他 git 命令可以帮助您撤销误操作。

查看更多的 [git 命令](https://www.w3docs.com/learn-git.html)和[片段](https://www.w3docs.com/snippets/git.html)，进入 git 世界探索新想法！