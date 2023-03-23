# 如何在没有 Anaconda 的情况下在 PyCharm 中安装 Python 包

> 原文：<https://betterprogramming.pub/how-to-install-python-packages-in-pycharm-without-anaconda-f78c0fe33362>

## 并通过几个简单的步骤解决常见的错误信息

![](img/c46df28ac5b651555b7899d4d3153eb5.png)

苏米特·萨哈尔卡在 [Unsplash](https://unsplash.com/s/photos/time?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

当我需要在 [PyCharm](https://www.jetbrains.com/pycharm/) 中使用 [pandas](https://pandas.pydata.org/) 、 [NumPy](https://numpy.org/) 、 [matplotlib](https://matplotlib.org/) 和 [seaborn](https://seaborn.pydata.org/) 时，它没有响应我的命令，即使我的环境设置为 Conda，并且我使用 [Anaconda](https://www.anaconda.com/) 作为基本解释器。

事情是这样的。

我想在 PyCharm 的一个项目中使用 [Selenium](https://www.selenium.dev/) ，并在开始运行之前安装 Pandas、NumPy 和其他包。在尝试了上面的命令之后，没有一个包被安装。我不断收到错误信息。

出于沮丧，我向我的 Python 导师抱怨了这个问题，并问道:“我不能在项目中使用 Jupyter Notebook 吗？”

这是我的导师——一位有 20 多年经验的 Python 开发人员——告诉我的:“有多种编辑器，每一种都适合特定的用途。Jupyter 最适合数据科学项目。PyCharm 是一个 IDE[注意是' IDE '这个词，不是' editor']。我建议你和 PyCharm 相处愉快。”

我回到了皮查姆。

当 Anaconda 停止工作时，如果选择 conda 作为环境，就不能在 PyCharm 中安装或使用包。所有软件包停止工作。

本文将探索 b 计划。您将学习如何在没有 Anaconda 的情况下在 PyCharm 中安装包，并解决安装包时可能出现的错误消息。

首先，如果你还没有下载 Python。

# **如何在没有 Anaconda 的情况下在 PyCharm 中安装 Python 包**

如果不考虑 Anaconda，在 PyCharm 中安装包可能会很困难。出于某种原因，屏幕上会出现错误信息。然而，创建 PyCharm 的人让安装包变得很容易——如果你知道如何做的话。

在安装软件包之前，请确保您有一个强大的互联网连接。

要安装软件包，请执行以下操作:

*   在 PyCharm 中创建一个项目。
*   点击 PyCharm 左上角的“文件”。点击“设置”您将看到“项目:…。(以及您的项目名称)。”点击它。点击“项目解释器”(刚刚在右侧打开)。
*   在右手边，你会看到一个加号。点击它。在页面顶部打开的框中，搜索要安装的软件包的名称。从列表中选择软件包，然后单击页面底部的“安装软件包”。

*注意:在搜索框中输入一些单词后，您将开始看到以您输入的单词开始的包装名称建议。最好从建议的软件包列表中选择您想要的软件包，以避免拼错软件包名称，从而看不到您想要安装的软件包。*

“安装成功”消息将显示在页面的底部…还是刚刚发生了错误？别担心。我掩护你。

# **如何解决 PyCharm 安装包时出现的错误信息**

如果在 PyCharm 中安装软件包时出现错误，可能是因为您的计算机上有一个新版本的“pip”软件包。在软件包安装之前，您必须更新它。

要更新“画中画”:

*   转到“项目解释器”点击它。在列出的软件包中找到“pip”。点击一次，就会高亮显示“pip”。在页面的右边，你会看到一个类似眼睛的图像。当你悬停在它上面时，它会显示“显示早期版本”
*   在眼睛状图像的顶部，有一个微弱的向上箭头。当你悬停在它上面时，它显示“升级”。单击箭头将“pip”升级到最新版本。等待升级完成。

升级完成后，如上所述，搜索要安装的软件包。

# **结论**

在 PyCharm 中安装 Python 包而不安装 Anaconda 应该是您的 B 计划，因为如果没有 Anaconda，您将不得不为每个新项目安装包——即使以前已经为不同的项目安装了包。这需要时间。

但是当 Anaconda 停止运行时，您仍然可以按照本文中的步骤快速完成工作。

感谢您的阅读。