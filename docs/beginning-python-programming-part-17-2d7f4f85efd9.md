# Python 编程入门—第 17 部分

> 原文：<https://betterprogramming.pub/beginning-python-programming-part-17-2d7f4f85efd9>

## 总结工作场所优化

![](img/077e3aeea8dfe7082f4ca4db98f4f3e9.png)

由[蔡思昂](https://unsplash.com/@oks_ong95?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在[Python 编程入门的第 16 部分](https://medium.com/better-programming/beginning-python-programming-part-16-5703d8f0fad3)中，我们讨论了测试。

[](https://medium.com/better-programming/beginning-python-programming-part-16-5703d8f0fad3) [## Python 编程入门—第 16 部分

### 让我们来谈谈测试驱动开发

medium.com](https://medium.com/better-programming/beginning-python-programming-part-16-5703d8f0fad3) 

希望你会用你所学到的东西来确保你的代码没有错误。

在这篇文章中，我们将讨论如何优化您的工作流程。您已经知道了自己开始工作所需的一切，但是如果您正在寻找关于设置开发环境以使您的工作流更加舒适的提示和技巧，那么请留下来。

一个朋友兼工作同事帮助我开始开发专业的 Python 程序；我把这些建议归功于他才公平。谢谢，[肖恩](https://github.com/sahensley)！

作为免责声明，这只是一个适合我的工作流程。如果你觉得太难或太局限，那就改变它，让它适合你。

我最近专门为此格式化了我的笔记本电脑，我会尽力在 macOS、Windows 和 Linux 之间进行转换。我不期望这些说明会有太大的变化，所以请更改版本号以适应您的系统。

在我们继续之前，有一个注意事项:无论您在哪个系统上开发，您都应该下载生产系统可用的最高版本的 Python。

这意味着，如果您有三个部署代码的系统，那么您应该使用这三个系统中可用的最低版本的 Python。比如 CentOS 7 只用 Python 3.6.5，而 Fedora 用的是 3.7.4，所以你要下载 Python 3.6.5。

如果你想下载 3.7.4 的 Fedora 专用应用程序，请随意，你将获得 3.7 中引入的在早期版本中没有的特性。

# macOS 和 Linux

## macOS 的先决条件

首先，我们需要安装开发人员命令行工具。如果您已经安装了 Xcode 并且至少运行过一次，那么这些都已经安装了。否则，打开一个终端窗口(`/Applications/Utilities/Terminal.app`)并键入以下命令:

这将导致出现一个对话框，询问您是否要下载并安装命令行工具。继续并等待安装完成。

接下来，我们需要安装 macOS SDK 头文件。安装命令行工具时，会下载这些工具，但不会安装。要运行安装，请使用以下命令:

这将安装构建 Python 解释器所需的依赖项。

接下来，我们需要安装自制软件。家酿是 macOS 的一个包管理器，类似于 Windows 上的 [Chocolatey](https://chocolatey.org/) 或者 Linux 上的 Apt/Yum。我们可以使用[自制软件网站](https://brew.sh/)上的命令来完成这项工作:

这应该以非特权用户的身份运行。它会在安装过程中询问您的密码。

一旦安装完成，我们就可以安装`pyenv`。`pyenv`允许我们安装多个版本的 Python，使它们彼此或与系统不冲突。在不影响系统 Python 版本的情况下，我们还可以选择我们的用户帐户使用哪个 Python 版本。我们将使用自制软件来安装它:

家酿将自动拉出任何依赖项，如 OpenSSL 或 Readlines，并同时安装它们。如果一切顺利，您将看到一些成功消息。有时我们会收到有用的文本，解释我们下一步需要做什么。在这种情况下，我们需要将`pyenv`添加到我们的 bash 概要文件中，这样在我们下次启动终端时它就可用了:

如果您以前从未见过 bash 脚本，这可能对您来说看起来很复杂。本质上，这检查是否安装了`pyenv`，如果是，我们调用命令`*eval $(pyenv init -)*` *。*如果不存在，则跳过该命令。

现在`pyenv`已经安装好了，我们需要重启我们的 shell，继续关闭这个终端会话并打开一个新的。

## Linux 的先决条件

根据需要使用`yum` (RHEL/CentOS 7)安装`pyenv`的以下必备软件包:

使用`apt-get` (Ubuntu/Debian):

## 计算机编程语言

我们现在可以安装任何我们想要的 Python 版本。当我们第一次开始这个系列的时候，我们在 3 . 7 . 3；现在我们在 3.7.4。

运行以下命令安装 Python 3.7.4:

如果一切顺利，您将看到一条成功消息。

我有以下错误，因为 Xcode 没有安装(仅限 macOS):

```
ModuleNotFoundError: No module named 'pyexpat'
```

因为我不想让一个 11 GB 的程序无所事事，所以我需要找到一个变通办法:

首先，我使用 brew 安装了`xz`，然后将`SDKROOT`设置为命令行工具 SDK，并将部署目标设置为我当前的 macOS 版本(10.14)。我把它放在我的 bash 配置文件中，这样我就不用每次都输入它了。如果我遇到问题，我会在命令前使用 *#* 注释掉`export…`行，并创建一个新的终端会话。(您也可以使用`unset SDKROOT`对该会话执行相同的操作。)

成功安装 Python 后，使用以下命令设置 Python 版本并进行测试:

如果你想安装不同的版本，你可以使用`pyenv install -l`来列出所有可用的版本。请注意有很多版本，但是您最感兴趣的是包含 3.x.x 的版本。

## Pip 包

安装并设置好您选择的 Python 版本后，我们可以安装一些`pip`包来帮助我们的工作流程。这里有几个我喜欢的:

*   [pipx](https://github.com/pipxproject/pipx) —将 pip 包安装到它们自己的虚拟环境中，然后链接到您的用户路径。
*   [黑色](https://github.com/python/black) — Python 代码格式化程序，选项很少，少了一件你需要考虑的事情。
*   [httpie](https://httpie.org/) —超级简单的 Python HTTP 客户端。

首先，我们将默认的`pip`和`setuptools`升级到最新版本，同时安装`pipx`。

`Pipx`现在不允许一次安装多个包，所以我们将使用以下命令安装`black`和`httpie`:

您可能会得到一些关于在您的路径中没有找到`~/.local/bin`的警告。只需使用以下命令来解决这个问题:

最后，我在我的主文件夹中创建一个名为`Developer`的目录。这提供了一个带有开发者工具图像水印的文件夹(仅限 macOS)。这样以后就好找了。我所有的项目都在这里。

因为我使用 Jetbrains 产品，所以我通常保持生成的项目文件夹名称不变，这样我可以很容易地找到 IDE 创建的所有项目(例如，`~/Developer/PycharmProjects/testapp/`)。

这就是我们需要做的。除非另有说明，任何用于 Python 开发的应用程序都将使用您用`pyenv`安装的 Python 版本。在这篇文章的结尾，我列出了我使用的软件。在那下面，我包含了一个教程列表来帮助你开始使用 web 框架。

![](img/f8224853864fdef1fd512c3ae5c1b88e.png)

照片由 [Nathan Fertig](https://unsplash.com/@nathanfertig?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# Windows 操作系统

这是基于我在工作电脑上的开发工作流程。

在 Windows 上，我不用`pyenv`或者`pipx`。我的工作流程非常简单。

我将所有的项目存储在`C:\Dev`中，然后创建一个快速访问这个位置的快捷方式。

我把我需要的所有版本的 Python 直接从[python.org](https://www.python.org/)安装到`C:\Python\`里，那里的文件夹被命名为`363x86`用于 32 位 Python 3.6.3。通过这种方式，我知道我将在我的虚拟环境中使用哪个版本和架构的 Python。

使用它，我可以轻松地在`C:\Dev`中创建新文件夹，并调用`C:\Python\372x64\Python -m venv C:\Dev\myproject\venv` 为我的应用程序创建新的虚拟环境。

如果我将代码部署到 Linux，那么我在 PyCharm 中设置了一个远程解释器，自动将我的代码的副本上传到这个服务器。这样，我可以在 Linux 环境中直接调用我的代码。

如果我只为一个特定的 Python 版本编写，我会在系统环境中将我的用户路径设置为包含我最常用的 Python 版本的目录。

# 软件

## 编辑

我使用多个编辑器，不是因为我喜欢我的生活综合体，而是因为我不同地使用每个编辑器。我将简要介绍每种方法的优点和缺点，并根据用途对它们进行排序。

[](https://www.jetbrains.com/pycharm/) [## py charm:JetBrains 为专业开发人员提供的 Python IDE

### Python & Django IDE 具有智能代码完成、动态错误检查、快速修复等功能…

www.jetbrains.com](https://www.jetbrains.com/pycharm/) 

Pycharm 是真正让我迷上 Python 的工具。它提供了许多工具来扩展您的工作流程。我最喜欢的工具是远程 ssh 解释器、内置 git 和自动补全功能，这是我用过的任何其他 Python IDE 都无法比拟的。它还有一个很棒的插件市场，可以帮助进一步扩展 IDE。它还内置了对其他语言的支持，包括 HTML 编辑功能，比一些为创建 HTML 而构建的编辑器更好。

唯一不好的是专业版每年 200 美元的费用；这很容易被社区版本抵消，社区版本删除了一些功能。除非你在做严肃的 Django、Flask 或远程开发，否则你不会错过任何东西。它还有一个 Edu 版本，包括一个内置的 Python 教程。

[](https://code.visualstudio.com/) [## Visual Studio 代码—代码编辑。重新定义的

### Visual Studio Code 是一个重新定义和优化的代码编辑器，用于构建和调试现代 web 和云…

code.visualstudio.com](https://code.visualstudio.com/) 

微软为 Python 社区做了一件好事。通过扩展可以使用他们的 Python 服务器，自动补全非常有用。它也是一个熟悉的编辑器(几乎是完整的 IDE ),非常适合快速编辑或者很难同时打开两个 PyCharm 实例的机器。哦，它是免费的，非常容易扩展。

我对此唯一的抱怨是，它运行在电子设备上，编辑大文件时会很慢。

[](https://www.sublimetext.com/) [## sublime Text——用于代码、标记和散文的复杂文本编辑器

### Sublime Text 是一个复杂的代码、标记和散文文本编辑器。你会喜欢光滑的用户界面…

www.sublimetext.com](https://www.sublimetext.com/) 

Sublime Text 对于编辑大文件来说非常神奇。虽然它没有 VS Code 或 PyCharm 的扩展，但它能以多快的速度处理兆字节大小的文件，弥补了这一点。不要让图像欺骗你，它可以在 macOS、Windows 和 Linux 上使用，就像其他两个编辑器一样。我使用 Sublime Text 进行快速编辑或查看大型结果集。您可以永远免费试用，但每次保存文件时都会提示您购买。如果你想付费的话，一个主要版本的许可费是 100 美元。

## 版本控制

PyCharm 和 VS 代码已经内置了版本控制特性。Sublime Text 提供 [Sublime Merge](https://www.sublimemerge.com/) 这很好，但很难证明 100 美元的主要版本许可是合理的。

[](https://desktop.github.com/) [## GitHub 桌面

### 从您的桌面进行简单的协作

desktop.github.com](https://desktop.github.com/) 

Github 桌面似乎拥有 Sublime Merge 的大部分功能，但它是免费的。它跟踪变化，并对 GitHub 提供全功能支持。如果你使用 BitBucket 或 GitLab，你的里程数可能会有所不同，但基本功能应该仍然有效。

[](https://git-scm.com/) [## 饭桶

### Git 易于学习，占用空间小，性能快如闪电。它远胜于 Subversion 等配置管理工具…

git-scm.com](https://git-scm.com/) 

有趣的事实——Linus Torvalds 非常讨厌使用其他版本控制媒介，以至于他最终创建了 Git 版本系统来辅助 Linux 内核。其他人也想这样。如果您发现 GUI 很笨拙，这就是您一直在寻找的命令行工具。这是基本的 git 客户机，它也可以在远程服务器上托管 git 存储库。这个我用的比 GitHub 桌面多，但还是用 PyCharm 的版本控制比较多。这是我需要对 git repos 进行快速维护的时候，比如对多个 repos 进行重置，或者对陈旧的项目进行最新的修改。

# 团体

 [## 懒鬼

### 一个致力于构建和支持全球 Python 社区的社区，为以下人员提供一个安全和包容的场所…

pyslackers.com](https://pyslackers.com/web) 

大家都爱 Slack，我就推荐这个 Python 社区。它过去被称为 Python 开发者，但似乎已经经历了一次更名。有问题吗？问吧。拥有超过 1000 名成员，您通常会得到快速响应。

[](https://www.python.org/community/forums/) [## 论坛

### Python 编程语言的官方主页

www.python.org](https://www.python.org/community/forums/) 

Python 论坛适合那些不想在网上创建另一个帐户的人。答案可能会比较慢，但你可以肯定在这里找到很多历史。

[](https://stackoverflow.com/) [## 堆栈溢出—开发者学习、分享和发展职业生涯的地方

### Stack Overflow 是最大、最值得信赖的在线社区，供开发人员学习、分享他们的编程…

stackoverflow.com](https://stackoverflow.com/) 

你知道会这样。堆栈溢出仍然是寻找答案的好地方，Python 也不例外。

# 教程

我们总是渴望得到更多，或者至少我们应该这样。这里有一些优秀的资源，可以让你的学习更进一步。

[](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world) [## 烧瓶超级教程第一部分:你好，世界！

### 欢迎光临！您将开始一段旅程，学习如何使用 Python 和 Flask 创建 web 应用程序…

blog.miguelgrinberg.com](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world) 

Flask 是一个比 Django 使用起来更舒服的 web 框架，这也是我首先发布它的原因。对于不需要数据库后端的网站来说，这是很棒的(尽管它做得很好)。小型网站和基本 API 应该使用 Flask。

[](https://tutorial.djangogirls.org/en/) [## Django 女孩教程简介

### 本教程不会神奇地把你变成一名程序员。如果你想擅长它，你需要几个月甚至…

tutorial.djangogirls.org](https://tutorial.djangogirls.org/en/) 

Django Girls 是一个非营利组织，致力于教女孩和妇女如何编码。虽然它面向女性，但它也可以成为每个人的优秀资源。他们有一个关于如何开始使用 Django 的很棒的教程。当你第一次开始的时候，有些步骤会重复，但是还是值得多做几次。

[](https://docs.djangoproject.com/en/2.2/intro/tutorial01/) [## 编写您的第一个 Django 应用程序，第 1 部分| Django 文档| Django

### 本教程是为 Django 2.2 编写的，它支持 Python 3.5 及更高版本。如果 Django 版本不匹配，你…

docs.djangoproject.com](https://docs.djangoproject.com/en/2.2/intro/tutorial01/) 

Django 也有他们的教程，但我发现在你从 Django Girls 那里学到基础知识后，会更容易跟上。Django 是一个包含电池的网络框架。当你创建一个由数据库支持的网站时，最好使用它。

 [## 威廉·文森特

### William 是一名软件工程师，是 Django For 初学者和 REST APIs with Django 的作者。

wsvincent.com](https://wsvincent.com/) 

William Vincent 创建了一个小博客，涵盖了所有内容，重点是 Python 和 Javascript。到处都有播客和教程，值得一读。

虽然我已经给了你这些资源，它们是教你如何开始使用这些库。最终，要靠你自己去尝试，走出“辅导模式”

# 摘要

这个 Python 系列到此结束。我希望你学到了新东西。我们通过提供开发工作流的例子、工具列表和将使您继续下去的教程来总结这一点。

直到下次，继续练习！

除了作为消费者之外，我与上面列出的任何产品都没有关系。这里表达的观点是我自己的。我是 pythondev.slack.com 的基本成员。