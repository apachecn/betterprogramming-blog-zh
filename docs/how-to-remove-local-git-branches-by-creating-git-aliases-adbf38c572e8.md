# 如何通过创建 Git 别名来删除本地 Git 分支

> 原文：<https://betterprogramming.pub/how-to-remove-local-git-branches-by-creating-git-aliases-adbf38c572e8>

## 适用于您日常工作流程的便捷命令

![](img/421cd0ea8dbcd9577ab6f403c6c8351e.png)

照片由 [Ujesh Krishnan](https://unsplash.com/@ujesh?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在优化 Git 工作流并开始处理 Pull 请求之后，您可能会有大量的本地分支，这些分支都已经被合并，并且可以被删除。

在这篇简短的教程中，我将展示如何轻松地做到这一点，以及如何创建一个可以在任何地方重用的 Git 别名。

# 删除单个本地分支

要删除单个本地分支，您可以打开您最喜欢的 Git GUI 界面，只需在选中的分支上按下`delete`。

但是，如果您不能正确使用终端，您就不能称自己为开发人员。

切换到您喜欢的终端，使用以下命令删除分支:

不幸的是(或者幸运的是)，只有当您想要删除的选定分支已经被合并时，这个命令才会起作用。但是，如果要删除未合并的分支，可以使用大写的 d。

# 删除所有本地分支

当您有多个本地分支时，您可能希望用一个命令将它们全部删除，而不是对每个分支都执行 delete 命令。

要实现这样的功能，知道`git branch -D`可以同时处理几个文件是很重要的。

考虑到这一点，我们首先编写一个命令来查找存储库中的所有分支:

如果您在 CLI 中执行此命令，您将看到您的主要和/或受保护分支也包括在内:主、主要、发布等。

你可以根据需要调整 find 命令(对我来说是 main/master/develop)。命令如下:

此外，您可以添加`--merged`标志来表明您只想查找已经合并的分支

现在，您可以通过管道将结果传递给上一章中介绍的 delete 命令，如下所示:

在任何 Git 存储库中执行这个命令(其中 main/master 是受保护的分支)将会删除每个已经合并并且没有本地更改的分支。如果你忘记了`--merged`，你也可以用`-d`来中止。

## **重要**

如果您在已更改的分支上执行此脚本，它将无法正常工作，因为将出现以下错误:

```
error: branch '*' not found.
```

为了避免这个问题，您可以通过在最后添加一个`\|*`来调整命令。这里有一个例子:

# 创建 Git 别名

## 简单的 Git 别名

Git 别名是一个通过简化工作流程来改善 Git 体验的特性。它把命令打包成一个别名，所以你可以在任何你想用的地方使用它。

您可能已经使用过的重要别名:

*   `co`用于结账
*   `ci`对于提交

要设置这些别名，您可以执行:

不幸的是，在简单的 Git 别名中，不可能正确使用管道操作符并将一个命令的结果转发给另一个命令。

幸运的是，Git 允许您使用别名！

## 复杂的 Git 别名

通过使用`!` (bang)操作符，您的别名可以转义为 shell，并且欢迎您进入一个所有别名都有可能的新世界。通过这种技术，您可以使用以下内容:

*   外壳参数和扩展
*   多个 git 命令
*   Greps、Pipes 和您已经安装的所有 Unix 命令行工具

要创建新的 Git 别名，您可以使用这个模板并添加您自己的命令:

在这个别名中，您使用一个简单的技巧，将实际的 git 命令包装在一个匿名的 bash 函数中(或者更好，一个名为`f()`的函数)。按照这种方法，您可以按如下方式访问命令行变量和 shell 扩展:

*   $1、$2 和$3 用于传递给 Git 别名的第一个、第二个和第三个参数
*   用`&&`链接 git 命令
*   使用`|`的管道结果
*   使用整个 Unix 工具包

为了演示复杂 Git alias 的功能，可以看一下下面两个例子:

**1。显示自上次 Git 命令以来的提交情况**

可以通过执行`git new`并提供`HEAD`来使用:

**2。向您的本地存储库添加一个遥控器**

可通过执行`git ra`并添加遥控器名称和遥控器`URL`来使用:

# 创建别名以删除分支

既然您已经知道了如何创建一个扩展的 Git 别名，我们可以使用前面的信息，用一个名为`f()`的匿名 bash 函数包装这个命令:

之后，您可以使用`git config`命令为当前登录的用户创建一个全局 Git 别名:

# 结论

您可能有多个本地分支，但在关闭“拉”请求后忘记立即删除。也许远程分支已经被删除，但是您的本地文件夹每天都在增长。希望您现在知道了解决这个问题的技术，并且可以在 CLI 中使用它来删除已经完全合并的每个本地分支。

此外，我希望您学会了一种使用 Git 别名和带有`!` (bang)操作符的复杂 Git 别名来优化您的 Git 工作流的优秀方法。

如果你喜欢读这篇文章，考虑在评论区留下你的宝贵想法。我很想听到你对我的教程的反馈。

```
**Want to Connect?**Feel free to connect with me on my [Blog](https://www.knulst.de), [LinkedIn](https://www.linkedin.com/in/paulknulst/), and [Twitter](https://twitter.com/paulknulst).
```

*本文最初发表于我的博客*[*https://www . paulsblog . dev/how-to-remove-local-git-branches-by-creating-git-aliases/*](https://www.paulsblog.dev/how-to-remove-local-git-branches-by-creating-git-aliases/)