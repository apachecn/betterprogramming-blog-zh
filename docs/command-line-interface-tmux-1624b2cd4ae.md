# 命令行界面— tmux

> 原文：<https://betterprogramming.pub/command-line-interface-tmux-1624b2cd4ae>

## 使用 tmux 管理终端仿真器实例的高级概述。

![](img/5ed8b5b5dd5a1314b6efa81ea6561964.png)

Pierre chtel-Innocenti 在 [Unsplash](https://unsplash.com/search/photos/window?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# tmux 是什么？

根据定义，tmux 就是所谓的“终端多路复用器”

## **什么是终端复用器？**

根据定义，终端多路复用器是:

> *一个* [*软件应用程序*](https://en.wikipedia.org/wiki/Software_application) *可以用来* [*复用*](https://en.wikipedia.org/wiki/Multiplexing) *几个单独的* [*伪终端*](https://en.wikipedia.org/wiki/Pseudoterminal)*[*登录会话*](https://en.wikipedia.org/wiki/Login_session) *在单个终端内显示，* [*终端仿真器*](https://en.wikipedia.org/wiki/Terminal_emulator)*
> 
> **[维基百科](https://en.wikipedia.org/wiki/Terminal_multiplexer)**

**为了让您对 tmux 和终端多路复用器有一个更全面的了解，它们本质上是终端仿真器的窗口和会话管理器，可以极大地提高您遍历不同文件系统的效率。它们允许用户垂直和水平拆分窗格，管理单个实例中的多个窗口，以及在现有会话中创建嵌套实例。**

**根据 tmux 的 GitHub 知识库 Wiki:**

> **“它能让你在一个终端的几个程序之间轻松切换，分离它们(它们在后台继续运行)并把它们重新连接到不同的终端。做更多的事情。”**
> 
> **( [GitHub](https://github.com/tmux/tmux/wiki) )**

**如果您想知道为什么您可能想要使用像 tmux 这样的工具，这里有一个实用的例子，它有望巩固我对它的用例的推理:**

**假设您有一台或多台计算机，您可能希望访问它们各自文件系统中的特定文件。我们可以假设其中一台机器是您的本地计算机，无论是笔记本电脑还是某种台式机，但是其他机器呢？**

**也许您有一台由 AWS 或 DigitalOcean 等服务托管的服务器，该服务器运行类似 Unix 的操作系统，如 Ubuntu，并且您想要一种方法来管理与所述文件系统相关联的文件，而无需实际访问计算机。当然，这就是 [SSH](https://en.wikipedia.org/wiki/Secure_Shell) 的用武之地，但是在 SSH 进入服务器并完成您希望完成的任何工作之后，如果您知道将来需要回到这台计算机上的这个文件系统，该怎么办呢？如果您需要跨多台计算机访问多台服务器，该怎么办？**

**如果没有像 tmux 这样的工具，那么管理大量不同计算机的 SSH 会话将是一场噩梦。**

**Tmux 允许你创建一个新的会话，然后在每个会话中，你可以有多个窗口。在每个窗口中，根据您希望访问的计算机，您可能有不同的文件系统。现在，您不再需要运行多余的 Unix 脚本来返回原来的位置。**

**当然，这不是为个人使用而设计 tmux 的唯一方法。Tmux 足够健壮，允许您或您的团队创建适用于任何场景的多路复用器工作流。**

# ****安装****

**在真正进入 tmux 并第一次使用它之前，您首先需要在您使用的任何机器上安装它。以下是最常见的方法:**

**[](https://brew.sh/)****(推荐 macOS 用户使用):******

```
****brew install tmux****
```

****[**Git**](https://git-scm.com/)**:******

```
****git clone https://github.com/tmux/tmux.git
cd tmux
sh autogen.sh
./configure && make****
```

****安装完成后，您可能需要重新启动终端模拟器，以便使用`tmux`命令。只需退出现有的终端模拟器，重新打开一个新窗口，或者在终端中运行以下 Bash 命令:****

```
****reset****
```

****如果由于某种原因这两个选项都不起作用，您可以找到您喜欢的 shell 的资源文件(例如在类似 Unix 的机器上找到一个`.bashrc`文件):****

```
****source ~/.bashrc****
```

****希望您现在已经在机器上安装了 tmux，并准备开始使用它！使用 tmux 首先要做的事情之一就是在您选择的终端仿真器中编写`tmux`命令:****

```
****tmux****
```

****这将使您进入一个新的 tmux 会话，其名称是您所在会话的索引。如果这是你第一次在你的机器上使用 tmux，你应该有 0 或者 1 的索引，这取决于你的`.tmux.conf`所说的优先索引号。****

****您的`.tmux.conf`文件是您所有 tmux 的配置设置。它可以在您的计算机的根用户目录中找到，`~/`对于 macOS 机器，只有在您的机器上启用了隐藏文件时才能看到。如果您没有启用隐藏文件，这里有一些资源可以帮助您根据您可能使用的机器来启用它们。****

*   ****[macOS](https://www.macworld.co.uk/how-to/mac-software/hidden-files-mac-3520878/)****
*   ****[视窗](https://support.microsoft.com/en-us/help/14201/windows-show-hidden-files)****
*   ****[Linux](https://askubuntu.com/questions/232649/how-to-show-or-hide-a-hidden-file)****

****一旦你让隐藏文件在你的电脑上可见，你现在应该可以看到`.tmux.conf`文件了。如果您是 tmux 新手，现在还不要太担心这个文件。当我们开始定制 tmux 时，我们将回到这个文件(这在本文后面会更有意义)。****

# ******入门******

****现在，您的机器上已经安装了 tmux，并且您的系统上已经启用了隐藏文件，让我们进入****

## ******最喜欢的 tmux 命令******

*****只有当用户在会话中时，所有这些命令才会执行。所有命令也以键盘快捷键* `ctrl + b` *为前缀。*****

*   ****"—水平拆分当前选项卡。****
*   ****% —垂直拆分当前选项卡。****
*   ****箭头键—允许在窗格之间导航。****
*   ****$ —重命名当前会话。****
*   ****，—重命名当前选项卡。****
*   ****s —在会话之间切换。****
*   ****d-从现有会话中分离。****
*   ****x —从当前窗口中删除一个窗格。****
*   ****& —删除当前会话中的选项卡。****
*   ****c-在当前会话中创建新选项卡。****
*   ****r-重新启动当前会话。****

****关于在一个会话中可以执行一次的所有 tmux 命令的完整列表，请参考[此链接](https://tmuxguide.readthedocs.io/en/latest/tmux/tmux.html)。****

## ******定制 tmux******

****就其本身而言，tmux 是一个非常通用和健壮的工具，它旨在解决问题，但是我们能够通过个人定制将多路复用器更进一步，以更好地适应日常程序员。尽管你可以修改 tmux，使它变成一个最适合你自己的需求和愿望的工具，这里还是列出了我为使 tmux 成为我自己所做的所有事情:****

*   ****将默认的`ctrl + b`按键改为`ctrl + a`。****
*   ****添加插件，提供 tmux 中不存在的特性。****

****以下是我使用的一些:****

*   ****[tmux-插件/tpm](https://github.com/tmux-plugins/tpm)****
*   ****[tmux-插件/tmux-复活](https://github.com/tmux-plugins/tmux-resurrect)****
*   ****[tmux-插件/tmux-连续体](https://github.com/tmux-plugins/tmux-continuum)****
*   ****[tmux-plugins/tmux-copy cat](https://github.com/tmux-plugins/tmux-copycat)****
*   ****[tmux-插件/vim-tmux](https://github.com/tmux-plugins/vim-tmux)****
*   ****[nhd aly/tmux-更好的鼠标模式](https://github.com/NHDaly/tmux-better-mouse-mode)****
*   ****[jimeh/tmux-themepack](https://github.com/jimeh/tmux-themepack) —电力线/块/青色****

****如果你正在寻找 tmux 插件的完整列表，请随意查看这两个 GitHub 链接:****

*   ****[tmux-插件](https://github.com/tmux-plugins)****
*   ****[rothgar/awesome-tmux —插件](https://github.com/rothgar/awesome-tmux#plugins)****

****为了对你的 tmux 配置进行实际的修改，你需要打开`.tmux.conf`文件，它应该在你的用户文件系统的根目录下。在 macOS 机器上，这将是`~/`。****

****如果你想自己动手定制 tmux，请随意查看来自 Ham Vocke 的这篇关于如何定制你自己的`.tmux.conf`的博客文章。****

****如果您有兴趣从自顶向下的方法改变 tmux，请在 GitHub 上查看来自 gpakosz 的这个流行的 tmux 配置[。](https://github.com/gpakosz/.tmux/blob/master/.tmux.conf)****

# ******结论******

****总而言之，如果您希望增加工作流程，并且每天都使用命令行界面，那么 tmux 是一个很好的工具。快速遍历多个文件系统不仅有助于提高用户的工作效率，还有助于划分特定的项目，甚至整个系统。****

****虽然一开始它有一个尴尬的学习曲线，但是一旦学会了，在某种程度上，如果没有多路复用器，你就不太可能再回去使用终端仿真器了。****

****如果您发现了任何错误，请随时发表评论。我会尽量尽快回复你。****

****感谢您的时间和关注。****