# 基本 Git/GitHub 备忘单

> 原文：<https://betterprogramming.pub/basic-git-github-cheat-sheet-fa020831cb35>

## 版本控制初学者指南

![](img/f2f9c32514f05f0a7225f69973648a48.png)

约翰·施诺布里奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

如果您还不熟悉版本控制并将其融入到您的日常工作流程中，现在是时候开始了。这是一个让您开始使用 Git 的基本指南，为您的进一步开发打下坚实的基础。几乎可以肯定的是，Git 在任何职业环境中都有使用，你越早熟悉它，你对雇主就越有价值。此外，这将使您的个人体验更好，因为您可以切换计算机，而不必担心将项目保存在闪存驱动器上。团队项目将变得更容易管理。曾经把你的代码搞得一团糟，以至于你觉得从头开始会更容易？有了版本控制，你可以回到一个稳定的版本，摆脱那些你想在凌晨 2 点实现的疯狂想法。

# Git 和 GitHub

[Git](https://git-scm.com) 是实际的版本控制系统。它是一个运行在命令行中的开源 DVCS(分布式版本控制系统)。这基本上意味着它保存了项目的整个历史。你的项目历史和你的同事正在进行的同一项目的历史都有副本。这与 [SVN (subversion)](https://subversion.apache.org) 相反，后者的全部历史只存储在一个地方。

GitHub，通常与 Git 混淆，实际上是一个存储库托管服务。你现在可能不明白什么是存储库，但是坚持下去，到最后你会明白的。GitHub 实际上有很多东西，但是现在，我们将保持它的简单。这是您将使用 Git 上传历史的地方。这允许您和您的同事检索项目的最新副本，同时还允许多人在不妨碍彼此的情况下处理项目。

# 入门指南

Git 很复杂，需要学习的东西很多，但是要开始，你只需要知道一些关键的东西。使用 Git 越多，您将会遇到这样的情况，这是绝对不够的，但是有很多资源可以在这种情况下帮助您。所以，用这个来让你开始，但一定要不断扩展你的知识。

你要做的第一件事是下载 Git。对于 Windows 用户，我建议也安装 Git Bash，它在安装 Git 时可用。对于 Mac 用户来说，使用终端完全没问题。一旦安装完毕，请前往 [GitHub](https://github.com/) 创建一个免费帐户。现在您有了 Git，命令行工具，和一个 GitHub 帐户，您将在那里上传您的存储库。

# 小抄

使用 Git Bash 或终端导航到实际的项目文件夹。如果您正在使用 Git Bash，您可以右键单击项目文件夹并选择“Git Bash Here ”,它将在该工作目录中启动您。

`**git init**`这将在您的项目中创建一个. git 存储库。一个存储库或者“repo”是一个你在一段时间内对你的项目所做的所有变更的集合，并且将建立这些变更的历史。这是你在新项目中想做的第一件事。

`git config --global user.name "Your Name"
git config --global user.email "yourEmail@mail.com"` 这将设置您每次提交时使用的信息。这只需要在第一次安装 Git 时做一次。

`git add filename.extension` 把“文件名.扩展名”替换成你想添加的任何文件，比如“index.html”。这将把您指定的文件添加到所谓的“临时区域”或索引中。可以把暂存区想象成一个区域，在这里，所有的东西都被准备好并转移到您的存储库中。

`git add .` 如果您想将项目文件夹中的所有内容添加到暂存区，该命令会自动完成，而不必逐个添加每个文件。

`git add *.html` 如果要加全部。html 文件添加到您的临时区域，这将是要使用的命令。扩展名可以随意更改。

`git status` 显示已经添加到暂存区的内容，以及需要添加到暂存区的已更改文件。

`git reset filename.extension` 从暂存区删除指定文件。

`git rm --cached filename.extension` 这将从暂存区删除文件，并将其设置为不跟踪。

从您的临时区域获取文件，并将它们提交到您的本地存储库。引号中是对每次提交所做更改的简要描述。尝试用简短的细节描述提交，比如“修复了用户名不更新的错误”，而不是像“一些更改”这样的提交消息

这将创建一个名为. gitignore 的文件。您可以用文本编辑器打开该文件，并从您的存储库中写入您希望忽略的文件或文件夹的名称。当你运行`git status`时，被忽略的文件不会显示出来，以防止你提交你之前说过不想提交的文件，甚至不知道它们的变化。

`git branch branchName` 创建所谓的分支。分支是您所在的前一个分支(通常是主分支)的代码库的直接副本。

`git checkout “branchName”` 将允许您签出您创建的分支并在该分支内工作。您可以在这里对您的代码进行任何想要的更改。准备就绪后，您可以提交代码并将分支推送到 GitHub(见下文),或者如果出现问题或者您决定不再需要该功能或错误修复，您可以删除该分支。

`git merge branchName` 在 Master 中，您可以使用这个命令从您正在工作的分支获取提交，并将它们与主存储库合并在一起。

`git remote add origin [https://github.com/userName/project.git](https://github.com/azndude296/asdf.git)` 这将添加您的远程存储库的位置。到目前为止，所有的东西都在你电脑的本地存储库中。您需要进入您的 GitHub 帐户并创建一个新的远程存储库，在那里您将能够推送您的本地存储库。创建远程存储库后，您将获得一个链接，该链接就是您希望在上面的命令中使用的位置。

已经与您的项目相关联的远程存储库列表。

`git push -u origin master` 这会将您的本地存储库推送到您的远程存储库。这个命令只需要你第一次做的时候这样写。

这是您在初次推送后将代码推送到 GitHub 的工具。

如果您正在使用的计算机上没有您的项目，这将允许您将整个项目克隆(或下载)到您正在使用的目录中。

`git pull` 如果您与其他人在同一个代码库上工作，该命令将允许您从远程存储库中获取最新版本，并更新您的本地版本，以便您可以在最新更新的更改进入代码库时使用它们。

# 结论

希望这已经给了你足够的信息来开始，并对正在发生的事情有一个基本的了解。Git 还有更多的内容，但是您可以在这些信息的基础上进行构建。许多人不知道从哪里开始，或者认为理解起来很困难，但是只要掌握这些信息就可以开始了。你会很快看到好处，也会增加你作为程序员的价值。