# 如何用 Chezmoi 管理点文件

> 原文：<https://betterprogramming.pub/how-to-manage-dotfiles-with-chezmoi-53cadb36b226>

## 懒人管理点文件快速指南

![](img/4b16bcb37d5f7307050a552cea1efb14.png)

加布里埃尔·海因策在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

有大量关于如何管理网络文件的教程和资源。如果你没有听说过“点文件”，它们是类 Unix 系统中以点开始的配置文件，例如`.bashrc`、`.zshrc`、`.vimrc`、`.gitconfig`等等。

默认情况下，点文件是隐藏的，它们通常用于定制您的系统。

何必呢？嗯，把你的点文件放在云上可以让你非常容易地建立任何新的环境，比如得到一台新的笔记本电脑等等。

## 先决条件

*   Git 托管服务帐户，例如 [GitHub](https://github.com/)
*   Git 以及如何使用它的基本知识
*   基本 Linux 命令

# 棘手问题

dotfiles 社区非常庞大。

作为一个普通的 Linux 用户，我在 VSCode 之外的开发工具定制完全是一团糟。我看到[华丽的网络文件](https://dotfiles.github.io/inspiration/)在互联网上到处被分享。他们是如此迷人，迷人，但有时令人生畏。

在花了几个小时阅读、浏览优雅的[点文件库](https://github.com/search?q=dotfiles&type=Repositories)之后，这篇文章旨在让你开始尽可能轻松地管理你的点文件。

# 传统

通常，点文件在一个单独的 Git 目录中管理，通常称为`~/.dotfiles`。然后，您必须从那里创建到相应点文件的原始位置(通常是您的主目录)的符号链接。

最后，点文件被提交并推送到你选择的 Git 托管服务，比如一个公共的 GitHub 库，[就像这个](https://github.com/ngshiheng/dotfiles)。

为了获得更多的背景知识，我强烈推荐你[阅读这篇文章](https://medium.com/@webprolific/getting-started-with-dotfiles-43c3602fd789)以了解通常如何管理点文件。

# Chezmoi 入门

今天，我们将研究如何抽象出管理你的点文件的大部分麻烦。

在我寻求一个简单的解决方案来管理我的网络文件的过程中，我找到了 [chezmoi](https://chezmoi.io/) 。使用 chezmoi，您可以忽略手动创建符号链接的部分。如果你需要更多的说服力，请查看[为什么使用 chezmoi](https://www.chezmoi.io/why-use-chezmoi/) 。

以下是如何开始使用 chezmoi 管理您的点文件的分步指南。

## 1.创建 GitHub 存储库

在我们开始之前，您需要[创建一个新的 GitHub 库](https://github.com/new)。

按照惯例，管理点文件的存储库名称通常称为`.dotfiles`或简称为`dotfiles`。这里，我假设您将您的点文件库命名为`dotfiles`。

不要误会，chezmoi 也可以与其他 Git 托管服务一起使用，如 [GitLab](https://gitlab.com/) 或 [BitBucket](https://bitbucket.org/) 。

## 2.装置

安装 chezmoi 很容易，只需根据您选择的软件包管理器按照这里的安装步骤[进行操作。快速的单行安装应该是:](https://www.chezmoi.io/install/)

```
sh -c "$(curl -fsLS chezmoi.io/get)"
```

## 3.初始化

还记得如何在 Git 目录中管理点文件吗？只需运行`chezmoi init`在`~/.local/share/chezmoi`(下面是源目录)中创建一个新的 Git 目录，chezmoi 在这里存储它的源状态。

稍后，您将必须执行 Git 提交，并将您所做的任何新更改推送到源状态，就像任何 Git 存储库中的普通项目一样。

## 4.添加您的第一个点文件

在我们开始之前，一定要记住管理您的点文件的第一条规则是永远不要将任何秘密(例如 API 密钥、AWS 凭证)推送到任何公共存储库(甚至不要推送到私有存储库！)明文。

在本文中，我将使用`.zshrc`作为我们选择的示例点文件。在实践中，可以随意一次添加多个点文件。所以，让我们开始通过“添加”来管理你的第一个点文件吧！

要添加通常位于我们主目录下的`.zshrc`文件，运行:

```
# This will copy ~/.zshrc to our source directory at ~/.local/share/chezmoi/dot_zshrc
chezmoi add  ~/.zshrc
```

记住这一点——chezmoi 只修改工作副本中的文件，您必须自己提交和推送任何更改(尽管 [chezmoi 可以自动完成这个](https://www.chezmoi.io/user-guide/daily-operations/#automatically-commit-and-push-changes-to-your-repo))。

要转到您的源目录，只需运行:

```
chezmoi cd # Same as running cd ~/.local/share/chezmoi/dot_zshrc
```

在这里，您将看到您的`.zshrc`文件为`dot_zshrc`。

## 5.提交并推送至 GitHub

听起来像一张破唱片——请确保您没有以纯文本形式提交任何机密或凭证。

从现在开始，这就像提交和推动您拥有的任何常规 Git 项目:

```
git remote add origin git@github.com:your-awesome-username/dotfiles.git 
git branch -M maingit add .
git commit -m "<Initial commit message>"
git push -u origin main
```

耶！现在，您已经在 GitHub 存储库中拥有了您的 dotfile！

# 维护点文件

我们永远不会满足于我们所拥有的。总会有更多的定制。新文件。更新。变化。

一个重要的概念——要对你的点文件做任何改变，总是在你的源目录中修改它(例如`~/.local/share/chezmoi/dot_zshrc`)；而不是原始的点文件(如`~/.zshrc`)。

您可以想象修改源目录中的任何内容，相当于更新您希望存储在公共存储库中的状态。

## 1.哪里需要改变

要修改源状态，您可以:

*   直接在`~/.local/share/chezmoi/dot_zshrc`更新源状态点文件或
*   一个快速的方法是使用`chezmoi edit ~/.zshrc`命令

## 2.如何编辑你的点文件

对更改满意后，保存您的点文件并运行:

```
# To view the diff between ~/.local/share/chezmoi/dot_zshrc & ~/.zshrc
chezmoi diff# To apply the changes to your original ~/.zshrc
chezmoi -v apply
```

上面的`apply`命令将对您主目录下的原始点文件进行修改，即本例中的`~/.zshrc`。

## 3.提交并推送到 GitHub(再次)

最后，要更新您在点文件存储库上的更改，只需运行:

```
git add .
git commit -m "<Your update commit message>"
git push -u origin main
```

太好了！您刚刚成功更新了 GitHub 存储库上的 dotfile！

# 添加新的点文件

添加新的点文件很容易。与必须执行`chezmoi diff`和`apply`的“维护点文件”不同，您只需遵循以下步骤:

1.  `chezmoi add ~/.newdotfile`
2.  `chezmoi cd`
3.  `git add .`
4.  `git commit -m "<New dotfile commit message>"`
5.  `git push -u origin main`
6.  搞定了。

# 使用您的点文件

现在我们已经将我们的点文件托管在 GitHub 存储库中，我们将研究如何在另一台机器上使用它们。

在另一台机器上，通过简单地运行以下命令，用您的点文件库初始化 chezmoi:

```
sh -c "$(curl -fsLS chezmoi.io/get)" -- init --apply your-awesome-username
```

记住——在将任何更改应用到本机的本地点文件之前，运行`chezmoi diff`查看`~/.local/share/chezmoi/`的点文件与其原始位置之间的差异。

如果差异是您期望看到的，运行`chezmoi apply -v`来覆盖您的本地点文件。

随时—要从您的点文件存储库中获取并应用最新的更改，请运行:

```
chezmoi update -v
```

# 结束语

在结束之前，你可以在这里找到我的点文件。

简而言之，您只需要习惯少数几个命令(您会自然而然地记住它们):

*   `chezmoi diff`
*   `chezmoi add`
*   `chezmoi update`或`chezmoi apply`

管理您的点文件有助于将您的定制随身携带*到任何地方*。我个人觉得在 3 台不同操作系统的机器上工作非常有用。

最重要的是，新机器的开发设置是世界一流的。

## 后续步骤

老实说，我对 chezmoi 的能力只是皮毛。如果您想深入了解，我强烈建议您查看 [chezmoi 的用户指南](https://www.chezmoi.io/user-guide/setup/)，它可以处理更高级的用例，例如:

*   运行自定义脚本，例如用于新机器安装的设置脚本
*   管理机器之间的差异(Linux、macOS、Windows)
*   模板化，其中某些文件的内容(例如`.gitconfig`)会根据机器环境而变化

坦率地说，没有“最好的方法”来管理你的网络文件。使用 chezmoi 似乎是解决我懒惰的最简单的方法，而且它确实有效。

这就是我今天的全部内容，感谢您的阅读！

```
**Want to Connect With the Author?**This article was originally published at [jerrynsh.com](https://jerrynsh.com/how-to-manage-dotfiles-with-chezmoi/)
```