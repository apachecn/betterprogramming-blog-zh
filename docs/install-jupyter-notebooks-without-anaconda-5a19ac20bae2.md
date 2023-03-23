# 安装没有蟒蛇的 Jupyter 笔记本

> 原文：<https://betterprogramming.pub/install-jupyter-notebooks-without-anaconda-5a19ac20bae2>

## 这比你想象的要容易

![](img/0431b23b02b986bfa7158cd690402554.png)

Artem Sapegin 在 [Unsplash](https://unsplash.com/s/photos/programming-language?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

[朱庇特笔记本](https://jupyter.org/)仍然是最好的编辑之一。凭借其简单、有吸引力的设计和易于导航的优势，我会选择它而不是 PyCharm。

嗯，要看项目了。

与强大的 py charm IDE 不同，Jupyter 是一个用户友好的编辑器，由于其清晰的背景和快速以及令人印象深刻的显示，非常适合数据分析。

赞够了。

网上大部分教程都集中在如何使用 Anaconda 安装 Jupyter Notebook。就好像你只能用 Anaconda 安装 Jupyter 一样。事实并非如此。

为什么 Anaconda 这么流行安装 Jupyter 笔记本？

原因之一是一旦你安装了 Anaconda，你就可以使用 Jupyter 运行了。另一个原因是 Anaconda 在笔记本上提供了预装的库。环境已经设置好了，安装一些库时处理 bug 的问题不存在。比如你要用 NumPy 库或者 BeautifulSoup4，它们都已经预装在笔记本上了！

如果 Anaconda 没有在我的电脑上停止工作，并且无法通过故障排除进行修复，我也不会考虑以另一种方式安装 Jupyter Notebook。

我的 Jupyter 笔记本不见了好几个星期，我的学习进度被改变了，很多时间被浪费了。“没有蟒蛇，就没有 Jupyter 笔记本”的口号完美地描述了蟒蛇-Jupyter 笔记本用户。如果蟒蛇停止工作，朱庇特笔记本就会死亡。

应该是这样吗？

你可能想问，“你为什么不谷歌一下这个问题，或者问问如何解决堆栈溢出？”

我做到了。栈溢出所有答案我都试过了。我的 Jupyter 笔记本仍然无法起死回生。

但后来我学到了两个重要的教训:

*   实现编程目标的方法不止一种。
*   面对每一个困难，都有一个教训或机会。

在本文中，您将学习如何在不使用 Anaconda 的情况下安装 Jupyter notebook。我还将讨论如何在没有压力的情况下在笔记本上安装 Python 库。

让我们开始吧。

# 如何在没有蟒蛇的情况下安装 Jupyter 笔记本

首先，下载并安装 Python。确保在安装 Python 时勾选了“将 Python 添加到路径”。

然后转到计算机的命令提示符。要查找命令提示符，请在 Windows 计算机左下角的“在此处键入以搜索”任务栏中键入“cmd”。

打开命令提示符，键入下面的命令，然后按 enter 键:

对于 Python 2 用户:

如果尚未升级，上面的命令将升级 pip。

升级 pip 后，在命令提示符下写入以下命令，然后按 enter 键:

等待 Jupyter 笔记本安装。您的计算机将显示“成功安装”消息。恭喜你！你安装了没有蟒蛇的 Jupyter 笔记本！

要打开 Jupyter 笔记本，请在命令提示符下输入以下命令，然后按 enter 键:

```
jupyter notebook
```

瞧啊。Jupyter 笔记本自动在你的一个浏览器中打开！

# 如何在 Jupyter 笔记本中安装 Python 库

Jupyter Notebook 支持许多编程语言，包括 Python、R、Julia 和 Scala。本文重点介绍如何在 Jupyter Notebook 上安装 Python 库。

当你不用 Anaconda 安装 Jupyter Notebook 的时候，有些 Python 库是没有预装在笔记本里的。这可能意味着另一个令人头疼的问题——安装库时的错误。

别担心。如果你按照下面的步骤，在 Jupyter 笔记本上安装库是一件轻而易举的事。

在 Jupyter 中安装库有很多方法。最好使用下面的方法，以避免任何未来的并发症。打开 Jupyter 笔记本，在编辑器中编写下面的代码:

熊猫将成功安装。

*注意:你可以把上面代码中的* `*pandas*` *替换成你想要安装的任何库。*

# **结论**

在编程中，可能会有某个特定公式由于某种原因不再有效的时候。你应该放弃并接受失败，而不去寻找另一个公式或解决方案吗？或者你应该无休止地盯着那个无效的公式？我不这么认为。

把编程想成一把双刃剑。如果一边不起作用，试试另一边。大多数时候，总有其他的一面。开始用这种方式思考，可以为自己节省大量时间和精力，减少挫败感。

谢谢你的时间。我希望这篇文章能帮助一些人。