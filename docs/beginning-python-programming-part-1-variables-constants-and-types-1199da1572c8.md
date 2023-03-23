# Python 编程入门—第 1 部分

> 原文：<https://betterprogramming.pub/beginning-python-programming-part-1-variables-constants-and-types-1199da1572c8>

## 变量、常数和类型的介绍

![](img/ea78fdfd6a2101c3790a24d4e696a567.png)

克里斯里德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

欢迎回来！我最近开始了职业生涯的新道路，斯威夫特退居二线。由于人们对我的关于 [Swift](https://medium.com/swift2go/beginning-swift-programming-part-1-variables-constants-and-types-776ad50952f1) 的系列很感兴趣，我想我应该做一个关于 Python 的系列。我还没有决定是否要为其他语言制作更多的教程，但是如果有你特别感兴趣的，请随时给我发消息。我尽量在一天之内回复我的读者。如果您看到任何错误，请让我知道，这样我们就可以为每个人提供一个很好的资源。

我的妻子非常害怕蛇，虽然我肯定还有其他人可能有同样的恐惧，但我会让这个教程免费。

像往常一样，我将从一些免责声明、一些最低要求和一些开发设置想法开始。如果你不明白我在说什么，没关系。我试着深入解释事情，这样你就能充分理解疯狂背后的方法。

首先，我希望你将 Python 文档页面加入书签，或者[下载](https://docs.python.org/3/download.html)它的 HTML 副本供离线使用。

 [## 文件

### 欢迎光临！这是 Python 3.7.3 的文档。

docs.python.org](https://docs.python.org/3/) 

请注意，Python 经常发布新版本，所以不要觉得自己需要最新版本。只要你有最新的次要版本(3.7)，你应该没问题。

随着本系列的深入，我将提供更多的链接供您浏览。

# 本教程有何不同

许多教程向你展示*如何*做某事；他们几乎从不解释为什么你会以某种方式做某事。我的目标是尽我所能并通过加入来自社区的反馈来告诉你为什么。

此外，教程经常一次把所有东西都扔给你。我计划详细讨论每个主题。你会学得很慢，但是当你完成每一部分的时候，我希望我已经能够教会你比你在其他地方阅读同样的内容所学到的更多的东西。

# 这个教程是什么

本教程旨在让你学习 Python 编程的基础知识。你学到的很多东西都可以移植到类似的语言中，但是所有的语法都是 Python 中的。

与我的 Swift 课程不同的是，我们将一路经历一些做着令人兴奋的事情的基本应用。

# 这个教程不是什么

本教程不会教你如何使用 Python 编辑器、如何制作 GUI 屏幕、<insert python="" module="" here="">、Jupyter 笔记本、数据科学或底层文件类型。</insert>

虽然其中一些主题很酷，但范围太广，本教程不能太短。

# 系列目标

这整个系列的目的不是要排挤另一个几乎什么都不懂的程序员；它旨在教导新程序员什么是编程，从课堂和实践的角度为读者提供对一般编程的更好理解，并让新程序员更好地理解将在面试和整个职业生涯中帮助他们的概念。

本教程假设您知道:

*   什么是电脑
*   什么是程序
*   足够的逻辑来理解 2 + 2 的答案是什么
*   足够的逻辑意识到你的冰箱里是否有牛奶

可以直接从[Python.org](https://www.python.org/)下载 Python 然而，有更好的方法为您的系统下载 Python。

如果你在 Mac 上使用[自制软件](https://brew.sh)安装 Python:

安装程序应该作为您的用户帐户运行，**而不是**使用提升的权限，因为它需要您的用户帐户来正确配置权限

安装完成后，键入以下命令在您的系统上安装 Python。

```
brew install python
```

这将在您的计算机上安装最新版本的 Python 及其所有依赖项。运行以下命令以确保安装正确。

```
python3 --version
```

如果你使用的是 Windows，你可以从网站上下载安装程序，并使用默认选项进行安装，或者，正如[my_variable = "This is code that will run" # this is a comment too"""
This is a
multiline
comment!!
"""my_second_variable = this is more code that will execute'''
This is another
multiline comment
using single quotes
'''](https://medium.com/u/5419113133a5# This is a single line comment</span><span id=)

# [摘要](https://medium.com/u/5419113133a5# This is a single line comment</span><span id=)

[所以今天你迈出了第一步，你学习了一些程序如何把变量和常量放入内存，这些东西是什么，它们会占用多少空间。这是一个缓慢的学习过程，但是我们正在进步。有很多比我快得多的教程，但我想确保你理解如何**和**为什么。](https://medium.com/u/5419113133a5# This is a single line comment</span><span id=)

# [下一步是什么？](https://medium.com/u/5419113133a5# This is a single line comment</span><span id=)

[接下来，我们将讨论**引用类型**、**指针**和**集合类型**。](https://medium.com/u/5419113133a5# This is a single line comment</span><span id=)

[](https://medium.com/@broebling/beginning-python-programming-part-2-reference-types-pointers-and-collection-types-a806ef6b92cf) [## Python 编程入门第 2 部分—引用类型、指针和集合类型

### 之前我们讨论了变量、常量和类型。

medium.com](https://medium.com/@broebling/beginning-python-programming-part-2-reference-types-pointers-and-collection-types-a806ef6b92cf) 

*我不隶属于 Python 软件基金会(PSF)、微软或 JetBrains。所有推荐都是我自己的。这些链接是为了方便起见，仅供参考。*