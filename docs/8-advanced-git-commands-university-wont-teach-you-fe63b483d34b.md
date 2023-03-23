# 大学不会教你的 8 个高级 Git 命令

> 原文：<https://betterprogramming.pub/8-advanced-git-commands-university-wont-teach-you-fe63b483d34b>

## 先进的提示和技巧，将节省您的时间和头痛。

![](img/a55af5dd56c5c34d3cd344cce7a1bc48.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Dom Fou](https://unsplash.com/@domlafou?utm_source=medium&utm_medium=referral) 拍摄的照片

当我开始我的 DevOps 之旅时，我幸福地意识到我面前的一切。然而，在与一些才华横溢的开发人员密切合作多年后，我开始大步前进。难以置信的是，坐在一位资深开发人员旁边，你可以学到很多东西，他会给你一天的时间，而且以前就在那里。日复一日，从该领域的专家那里获取**知识会教会你在任何在线课程或大学里都找不到的东西。**

虽然是的，课程，辅导和学校教育会教你核心概念。今天，我想更深入一点，展示一下我多年来学到的一些有用的技巧和窍门。使用这些高级 git 命令，我们不仅可以每周节省宝贵的时间；但是要对这个工具有更深的理解。

这些命令是我在我参与的每个项目中经常使用的，并且已经在我的笔记页面上存在多年了。所以，不多说了，我希望您喜欢我的八大高级 git 命令

![](img/786c12c5761a05e7f2ee27e2b99521ef.png)

照片由 [Unsplash](https://unsplash.com/s/photos/git?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [Yancy Min](https://unsplash.com/@yancymin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

# 1.在 Git 中搜索

如果您正在您的存储库中寻找可能已经删除很久的代码，git 有您的答案。使用下面的命令，我们可以在任何提交或分支中搜索任何短语。

```
$ **git rev-list** --all **|** **xargs** **git grep** -F ‘’
```

例如，假设您要搜索字符串“clapfor = joelbelton”在您的存储库中:

```
$ **git rev-list** –all **|** **xargs** **git grep** -F ‘clapfor=joelbelton;’
C2011:bestdev.py: clapfor=joelbelton;
B9831:devops_technical_writter.json: clapfor=joelbelton;
```

# 2.清理你当地的分支机构

我们可以删除已经从远程存储库中删除的本地分支。

```
git config --global fetch.prune true
```

我们还可以删除已经与我们的示例合并的任何本地分支:

```
git branch --merged master | grep -v "master" | xargs -n 1 git branch -d
```

# 3.提交计数

无论您是准备好重新建立基础、收集进度指标，还是只想知道您已经做了多少更改，计算您的提交都是非常有用的。我们还可以使用这些信息来增加内部版本号。

下面是我们如何统计这些提交:

```
$ **git rev-list** –count master
18
$ **git rev-list** –count feature
2
```

# 4.查看另一个分支的文件

我们经常想要查看存在于另一个分支上的文件的内容。我们实际上可以做到这一点，而不需要切换分支或浏览 git 存储库管理器。

例如，如果您在我们的**主**分支中有一个文件*‘new feature . py’*，但是我们正在我们的**开发**分支中工作，这已经落后了。我们可以使用以下命令查看这个新文件:

```
**$** git show master:newfeature.py
```

# 5.空白提交

如果您正在使用 DevOps 管道和 CI/CD，那么您可能会自动集成 git 提交。我们实际上可以只使用空白提交，而不是对文档进行小的修改。

```
**$** *git commit --allow empty -m 'new empty commit'*
```

这将触发我们的工作流，而不必对我们的代码进行细微的更改。

# 6.自动纠正拼写错误

每个人都会犯错误，每个人都在自己的终端上看到过这条消息…

```
git: ‘stats’ is not a **git** command. See ‘**git** --help’.The most similar **command** is
status
```

您可能没有意识到，但是 Git 实际上提供了自动更正命令的配置。添加全局标记意味着它专门应用于您的用户。

```
$ **git config** --global help.autocorrect 1
```

# 7.自动修剪

Git Prune 是一个实用命令，它整理掉任何引用都无法到达的孤立 Git 对象。任何不能通过标记或分支访问的提交都被定义为不可访问。Git prune 是一个 Git 命令，它将清除所有这些对象。

当然，知道你什么时候需要修剪被删除的对象是相当困难的，因为我们的库不会警告我们。幸运的是，通过启用这个 git 配置，我们可以让 git 在每次 git 获取和 git 拉取时执行一次修剪。

```
git config --global fetch.prune true
```

# 8.备份未被跟踪的文件

我不知道你怎么样，但是当被要求删除一些未被追踪的文件时，我总是很焦虑。虽然大多数情况下它是完全安全的，但为这些文件创建一个备份会很有帮助，以防你以后需要它们。

使用我们的 git 命令和一个聪明的 xargs 的组合，我们可以创建这个整洁的小功能，将所有未跟踪的文件一起备份到一个 zip 文件中。

```
$ **git ls-files** --others --exclude-standard -z **|**\
**xargs** -0 **tar** rvf ~**/**backup-untracked.zip
```

感谢阅读。

我定期在 DevOps 上发布文章。如果你想了解更多，我推荐你看看下面的故事:

[现代调试——保持领先地位所需的工具和技巧](https://medium.com/@joelbelton/modern-debugging-tools-and-tips-you-need-to-stay-ahead-bfe4340d3fa8)

[没人教你的最重要的 Linux 命令](https://medium.com/@joelbelton/the-most-important-linux-commands-that-nobody-teaches-you-ce423ef2ae28)