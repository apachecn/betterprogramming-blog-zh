# Python 编程入门—第 11 部分

> 原文：<https://betterprogramming.pub/beginning-python-programming-part-11-1dc915b4f0fd>

## 使用模块和虚拟环境

![](img/81f5f3ea638c3fccac7c59a570f81075.png)

里卡多·维亚纳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在上一篇文章中，我们介绍了如何构建代码的技巧、PEP 8 格式指南以及一些编码原则。

[](https://medium.com/better-programming/beginning-python-programming-part-10-afd00b71f04e) [## Python 编程入门—第 10 部分

### 在上一篇文章中，我们讨论了与错误相关的所有内容，以帮助减少程序中的错误数量。

medium.com](https://medium.com/better-programming/beginning-python-programming-part-10-afd00b71f04e) 

今天，我们将介绍更多的工具来帮助您保持代码的整洁，以及防止您重复发明轮子的常见实践。

# 包装

包是包含项目可重用代码的独立项目，与其他语言中的库或框架同义。

那么一个包裹里装的是什么呢？

在您希望在其他项目中重用的代码之外，一个包由一个`README.md`、一个`LICENSE`和一个`setup.py`文件组成。

*   `README.md` (markdown) —包含关于您的软件包的信息，可用于了解您的软件包的用途。
*   `LICENSE` —这是许可协议或使用条款。用这个让其他人知道他们是否需要给你信用，他们是否可以将它用于商业应用，他们是否可以进行修改和重新分发，如果你不承担损害赔偿责任，等等。
*   `setup.py` —这在您首次安装软件包时运行。它告诉 Python 任何依赖关系，并提供关于您的包的元数据，这些元数据将从包存储库中显示给用户。

在所有这些中，`setup.py`是最重要的，因为它是你的软件包如何安装的。

当我阅读许可证时，我总是被一些事情逗笑，当程序打印“你好，世界！”时，有人包括“对所造成的损害不承担责任”

撇开笑不谈，当你与世界分享你的代码时，这和 setup.py 一样重要。虽然您可能通过编写一个有益的程序为世界做了一件好事，但如果它不能与特定版本的 Python 一起工作，并且您的程序损坏了系统，那么您将承担责任，即使最终用户是最终决定在他们的计算机上运行您的程序的人。

Python 有数千个包可供您使用。我使用过连接到 LDAP 服务器的包来对用户执行工作，使用过远程 SSH 到另一台机器来运行脚本的包，使用过运行 web 服务器的包，等等。

给出这些例子，你可能对别人写了什么有一个很好的想法，以及你能写什么的一些方向。当你开始使用软件包时，你会对你能用它们做什么有更多的想法。

# Pip 命令

我们可以使用`pip`命令下载软件包。`pip`利用一个名为 [Python 包索引(简称 Pypi](https://pypi.org/) )的在线包存储库。

在早期的 Python 版本中，`pip`不包含在内。在 Linux 操作系统上，有一个名为`python-pip`或`python36-pip`的安装包。

`pip`是一个简单的实用程序。与`apt`、`yum`或`brew`非常相似，您可以搜索包，获取关于包的信息，或者安装和删除包。

我最常用的几个命令是:

*   `pip search <package_name>` —搜索要安装的软件包
*   `pip list` —列出所有已安装的软件包
*   `pip info <package_name>` —获取关于已安装软件包的信息
*   `pip freeze` —以需求格式提供已安装软件包的列表(稍后将详细介绍)
*   `pip install <package_name>` —安装软件包或以逗号分隔的软件包列表；使用`pip install -U <package_name>`将软件包升级到其最新版本。
*   `pip uninstall <package_name>` —卸载软件包或以逗号分隔的软件包列表

在本系列的前面，我说过我使用[黑色](https://github.com/python/black)来格式化我的代码。这通常是我在所有电脑上安装的第一个软件包。这个想法来自亨利·福特关于 t 型车颜色的[名言](http://oplaunch.com/blog/2015/04/30/the-truth-about-any-color-so-long-as-it-is-black/)

如果您希望使用它，可以使用以下命令安装它:

`pip install black`

您可以使用其他格式化程序，但是我不太关心它们如何缩进我的代码。这完全是主观的，所以我强烈建议你在决定一个之前把它们都检查一遍。

[或者，如果你想安装一个特定的版本，你可以使用`<=`来包含你指定的版本，或者使用`<`来获得最新的包，只要它比你指定的版本低。](https://medium.com/u/35ebb723d4a4#.#.#</code>。</p><p id=)

[如果您想确保您总是获得比特定版本更高的版本，您可以使用`>`或`>=`。](https://medium.com/u/35ebb723d4a4#.#.#</code>。</p><p id=)

[最后，你可以混合两者，在末尾用`>=0.2,<0.3`来表示大于或等于 0.2 和小于 0.3。这意味着 0.2.1 将被安装，而 0.25 或 0.3 将被拒绝。](https://medium.com/u/35ebb723d4a4#.#.#</code>。</p><p id=)

[`pip freeze`因输出重定向而派上用场。您可以使用
`pip freeze > requirements.txt`将输出发送到名为`requirements.txt`的文件中。如果文件不存在，将创建该文件；否则，里面的文本将被替换。如果出于某种原因，你想附加文本(不建议)，你可以使用`>>`代替。](https://medium.com/u/35ebb723d4a4#.#.#</code>。</p><p id=)

[在创建需求文件时，使用输出重定向可以为您节省大量时间。我喜欢做的事情是将我所有的测试需求从我的生产需求中分离出来。这样，pytest 或 coverage 之类的包就不会安装到生产环境中。我将所有这些放在测试阶段安装的一个`test_requirements.txt`文件中。](https://medium.com/u/35ebb723d4a4#.#.#</code>。</p><p id=)

[我清理需求文件的另一件事是在每个包上运行
`pip show <package>`来确定每个包需要什么。`pip show Django`的输出显示“Requires: pytz，sqlparse。”](https://medium.com/u/35ebb723d4a4#.#.#</code>。</p><p id=)

[基于此，我可以从我的需求文件中删除 pytz 和 sqlparse。您可以按照您希望的任何顺序来完成这项工作，但是我通常从 Django 或 Flask 等核心需求开始，到我知道可能需要其他包的其他需求，从上到下完成剩余的包。](https://medium.com/u/35ebb723d4a4#.#.#</code>。</p><p id=)

[请注意，如果一个依赖项被更新，并且版本没有包含在您的需求文件中，这可能会导致问题，正如下面评论中的](https://medium.com/u/35ebb723d4a4#.#.#</code>。</p><p id=) [Peter](https://medium.com/u/cc6a49fe4899?source=post_page-----1dc915b4f0fd--------------------------------) 所提到的。关于原因的更多细节，请看他的回答。

我这样做是因为有时我可能希望 Django 更新到下一个版本，这可能也包括一个更新的包需求。如果它是一个不应该在没有仔细检查的情况下更新的关键应用程序，则只保留列出的所有软件包。

如果你不知道哪些可能，不要担心；这需要时间和经验。

# 摘要

今天我们讨论了帮助我们使用预写代码执行任务的包，`pip`允许我们安装这些包，以及虚拟环境，这样我们可以保持我们的开发环境整洁。我们还介绍了需求文件，以及当我们与他人共享应用程序或将应用程序安装到不同的环境中时，如何使用它们来确保安装所有的依赖项。

## 链接

如果您想了解更多关于创建自己的包或将包上传到 Pypi 的信息，请查看 Python 打包用户指南。

 [## Python 打包用户指南-Python 打包用户指南

### Python 打包用户指南(PyPUG)是打包 Python 软件的教程和指南的集合。

packaging.python.org](https://packaging.python.org/) 

如果您想了解更多关于安装软件包和虚拟环境的信息，请查看本指南:

 [## 安装包 Python 打包用户指南

### 由于大多数 Linux 发行版处理 Python 3 迁移的方式，使用系统 Python 的 Linux 用户…

packaging.python.org](https://packaging.python.org/tutorials/installing-packages/) 

这些也在[docs.python.org](https://docs.python.org)播出。

 [## 安装 Python 模块-Python 3 . 7 . 3 文档

### 对于公司和其他机构用户，请注意许多组织都有自己的关于使用和…

docs.python.org](https://docs.python.org/3/installing/index.html) 

# 下一步是什么？

我们已经走了很长一段路。我本来打算写异步，但是我决定先介绍迭代器和生成器可能更有好处。两者都允许你循环遍历序列，我们将学习它们如何相似，如何不同，以及我们如何使用它们来改进我们的代码。

在那之前，实践你所学到的！

[](https://medium.com/@broebling/beginning-python-programming-part-12-5450ae804936) [## Python 编程入门—第 12 部分

### 在上一篇文章中，我们介绍了包和虚拟环境。

medium.com](https://medium.com/@broebling/beginning-python-programming-part-12-5450ae804936)