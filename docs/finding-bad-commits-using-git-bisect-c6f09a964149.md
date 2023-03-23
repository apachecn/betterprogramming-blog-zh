# 使用 Git 二等分查找错误提交

> 原文：<https://betterprogramming.pub/finding-bad-commits-using-git-bisect-c6f09a964149>

## 找出是哪个提交引入了新错误

![](img/ff4250bcc6bd2c1125001e58cf28ce2f.png)

图片由 [Shane Hauser](https://unsplash.com/@shanehauser) 在 [Unsplash](https://unsplash.com/) 上拍摄。

> “Git-二分——使用二分搜索法找到引入 bug 的提交”— [Git 的官方文档](https://git-scm.com/docs/git-bisect)

最好通过一个真实的例子来理解这个命令并演示它是如何工作的。

让我们创建一个空的 Git 项目，并添加以下提交:

```
git commit --allow-empty -m 'good commit 1'
git commit --allow-empty -m 'good commit 2'
git commit --allow-empty -m 'good commit 3'
git commit --allow-empty -m 'good commit 4'
git commit --allow-empty -m 'good commit 5'
git commit --allow-empty -m 'good commit 6'
git commit --allow-empty -m 'BAD COMMIT'
git commit --allow-empty -m 'good commit 7'
git commit --allow-empty -m 'good commit 8'
git commit --allow-empty -m 'good commit 9'
git commit --allow-empty -m 'good commit 10'
```

我们刚刚发现了一个 bug，但是我们不确定是哪个提交引入了它。`git bisect`来救援了！

我们首先需要运行`git bisect start`来启动向导:

```
git bisect start
```

现在我们需要告诉 Git 我们所知的最后一个引入 bug 的提交是什么。假设我们已经在最后一次提交时发现了错误:

```
git bisect bad HEAD
```

当然，最早的提交是好的:

```
git bisect good HEAD~9
Bisecting: 4 revisions left to test after this (roughly 2 steps)
[375c587c8da1c90d07fd58a2402fd8bcf842ffd2] good commit 6
```

现在 Git 将对我们的历史执行二分搜索法，并检查我们需要决定是好是坏的提交。

我们现在在`good commit 6`上，一个在 bug 被引入之前的提交:

```
git bisect good
Bisecting: 2 revisions left to test after this (roughly 1 step)
[f14653c6730ef0537dbf8ec541997d7e9deb5e2a] good commit 7
```

这个提交*不是*好的，因为代码已经包含了`BAD COMMIT`。所以我们需要告诉 Git 这是不好的:

```
git bisect bad
Bisecting: 0 revisions left to test after this (roughly 0 steps)
[421dab9fc7f885dfadb3c7a37d70fa7dfbb6f3d1] BAD COMMIT
```

最后，错误的承诺:

```
git bisect bad
421dab9fc7f885dfadb3c7a37d70fa7dfbb6f3d1 is the first bad commit
```

`git log`现在将揭示实际上是`BAD COMMIT`引入了这个 bug:

```
git log | grep "BAD COMMIT" -B 5
commit 421dab9fc7f885dfadb3c7a37d70fa7dfbb6f3d1
Author: MarounMaroun <maroun email>
Date:   Sun Jan 19 11:28:03 2020 +0200 BAD COMMIT
```

# 自动化 git 二等分

> "如果您有一个可以判断当前源代码是好是坏的脚本，您可以通过发出以下命令来一分为二:
> 
> `$ git bisect run my_script arguments`
> 
> 请注意，如果当前源代码是好的/旧的，脚本(上面示例中的`my_script`)应该以代码 0 退出，如果当前源代码是坏的/新的，则以代码 1 到 127(包括 1 和 127)退出，但 125 除外。”— [Git 的官方文档](https://git-scm.com/docs/git-bisect#_bisect_run)

`bisect`可以在*上运行一个命令，每次迭代*。如果命令成功(以零代码退出)，它会自动将提交标记为好。如果失败，它会将其标记为坏的(以非零代码退出)。例如，我们可以尝试使用`make`实用程序(`my_test.sh`)来构建我们的应用程序:

```
#!/usr/bin/env bashmake test || exit 1
```

现在我们可以运行`git bisect run ./my_test.sh`来开始自动化调试。如果脚本成功，它将返回一个零代码，并且`git bisect`将把它标记为好的。否则，它会被标记为坏的。

# **总结**

`git bisect`是一个追踪 bug 的简单工具。它大大减少了调试时间。当你有一个很难调试的 bug，并且很难找到是哪个提交引入了它的时候，最好使用它。如果您创建小而一致的提交，这将非常有用。

如果您有可以通过编写测试场景由工具本身应用的自动化测试，这个命令将会更加有用。