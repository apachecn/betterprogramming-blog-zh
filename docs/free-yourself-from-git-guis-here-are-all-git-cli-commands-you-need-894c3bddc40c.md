# 把自己从 Git GUIs 中解放出来——这里是你需要的所有 Git CLI 命令

> 原文：<https://betterprogramming.pub/free-yourself-from-git-guis-here-are-all-git-cli-commands-you-need-894c3bddc40c>

## 让命令行成为你的朋友

![](img/e4b4fc0dce4a14d6dd1aabe3b82d6dd0.png)

照片由[巴迪·阿巴斯](https://unsplash.com/@bady?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在团队中工作的软件开发人员除了互相分担工作之外，还非常依赖版本控制系统来跟踪每个团队成员所做的更改。版本控制系统也是我们的安全网——我们可能会犯错误和破坏项目，但是不用担心！在之前的提交中，工作版本仍然存在！(不过，不打破一切就很理想了)。

版本控制系统可以在任何平台上工作，所以开发者不需要使用相同的操作系统就可以互相协作。此外，如何使用 Git 并不重要(从命令行或使用图形用户界面)。重要的是你的代码是干净的，你使用的是特性分支，并且你基于这些特性创建了可读的拉请求。好吧，在整个工作流程中，还有一些事情很重要，但是让我们暂时专注于 Git。

我们现在将走过一个涉及 Git 的典型工作流。我假设您对 Git 有一些基本的了解。如果没有，没问题！我会解释我们在这里做的一切。

# 克隆一个回购

您可以克隆一个已经包含一些工作的 repo，也可以创建一个本地 repo 并远程持久化它。最终结果是一样的—一个远程回购，您可以在其中与您的团队共享工作。

如果您还没有克隆项目，您将使用

```
git clone url-to-project-repo
```

# 创建本地回购

您将使用`cd path-to-dir`导航到项目的根目录。之后，您将使用

```
git init
```

这将在该位置实例化一个 repo。

# 为本地存储库创建远程存储库

你想和你的队友分享你的工作。因此，继续将 URL 复制到一个空的repo。您将使用以下命令将本地存储库连接到远程存储库

```
git remote add origin [https://url-to-empty-remote](https://url-to-empty-remote)
```

现在你都准备好了！让我们来学习如何处理这个回购。

# 本地和远程分支机构

在 Git repo 中，您将有几个分支，每个开发人员都在其中工作。让我们假设所有这些工作后来都合并到了`develop`中。

为了列出您当地的分支机构，您将运行`git branch`。输出将显示您在本地拥有的所有分支。为了查看远程回购的分支，您将运行`git branch -r`。

# 远程拉动工作

现在让我们假设你在`develop`分支。在转到新的分支机构并开始工作之前，您希望确保自己了解最新的信息。

```
git pull
```

上面的命令将获得发生在`develop`分支上的最新变化。如果您想将一个特定的分支拉入您本地选择的分支，您可以键入

```
git pull origin branch-name
```

例如，当你在一个特性分支上工作时，这个命令会派上用场，同时，你的一个队友已经将一些东西合并到主分支中。然后，您将希望获得最新的更改，这样即使您正在处理您的特性，您也可以与代码库保持同步。

# 创建分支

假设您在`develop`分支。你想开始开发你的应用程序的`profile picture`功能。在将最新的更改放入您的本地分支后，您将使用

```
git checkout -b feat/profile-picture
```

用于创建一个新的分支(-b)并将其签出。这意味着你现在将在一个新的分支上工作。

# 签出现有或远程分支

如果您想检查一个现有的本地分支，您将使用

```
git checkout branch-name
```

如果您想检查一个只存在于远程回购上的分支，该怎么办？那我们就先用

```
git fetch
```

以便从遥控器获取所有分支。这也将从远程下载提交。现在您可以使用`git checkout branch-name`命令来检查之前只存在于`remote`上的那个分支。

# 承诺并推动你的工作

假设您已经实现了这个新特性，现在您想在远程回购上推进您的工作。

首先，我们要跑

```
git add .
```

为了上演所有的新变化。这意味着 Git 现在会考虑您刚才所做的更改。

我曾经遇到过`git add .`由于某种原因无法工作的情况。在这种情况下，我会使用

```
git add --all
```

完成完全相同的事情。

为了提交您的更改，您将运行

```
git commit -m "some info about the commit"
```

这将向包含您所有工作的分支添加一个新的提交。

假设你在`feat/profile-picture`分支。您将通过以下方式推动这些变化

```
git push --set-upstream origin feat/profile-picture
```

以便在远程上创建一个同名分支，并将其设置为本地分支的上游分支。这意味着，如果您随后要对这个分支做一些其他的更改，您可以简单地输入

```
git push
```

以便将您的提交推送到上游分支。如果不设置上游，每次都要显式指定上游分支。这意味着每次你想要提交时，你都必须键入`git push feat/profile-picture`。

# 在最后一次提交时更改某些内容

也许您忘记存放文件，或者您不喜欢您的提交消息。如果您还没有推送您的更改，您可以使用

```
git commit --amend -m "modified commit message"
```

以便在最近一次提交时保存最新的更改——因此无需创建新的更改。例如，当您在提交工作后发现一个未使用的导入时，这就很方便了。或者用于判定提交消息不正常。这不是创建新提交的理由，所以我们将使用`--amend`标志来告诉 git 我们希望在最后一次提交时添加更改。

# 更改分行的名称

如果要更改当前已签出的分支的名称，可以使用

```
git checkout -m new-branch-name
```

您将会看到您已经成功地更改了名称。

# 删除分支

有时我们会想要删除本地分支——否则，事情会变得一团糟！

请确保您所在的分支与要删除的分支不同。

```
git branch -d local-branch
```

`-d`标志告诉 Git 您想要删除这个分支。

如果您想删除一个远程分支，您将使用

```
git push origin --delete remote-branch
```

# 结论

这些是您每天都会用到的命令。有时你会需要一些更复杂的命令，但是 95%的时候你不会使用比这更复杂的东西。

非常感谢你的阅读，我希望这对你有所帮助！