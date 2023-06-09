# Python 中读取文件的 7 种方式

> 原文：<https://betterprogramming.pub/7-ways-of-reading-a-file-in-python-855340b002dc>

## 你能在不打开文件的情况下读取它吗？

![](img/7a4c81af303525a2a8e40a8295f6b94f.png)

照片由 [Gianluca Cinnante](https://unsplash.com/es/@gluca?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Python 编程语言以其巨大的灵活性而闻名，无论是在语法方面还是在如何实现某个目标方面。事实上，对于 Python 程序员来说，有无数不同的方法可以获得相同的最终结果，从使用花哨的语法到破解内置功能。

在本文中，我将向您展示用 Python 读取文件内容的 7 种方法。

# 老式的内置 open()函数

在 Python 中读取文件的第一个也是最基本的方法是使用内置的`open()`函数，该函数返回一个可读的流。随后，您可以使用`read()`方法来读取流的内容。

假设我们在 Python 脚本的同一个目录中有一个名为`file.txt`的文件。

好吧，这是一个有点平庸和非 pythonic 式的。让我们看看更好的方法。

# 用上下文管理器打开

这是 Python 中读取文件最常见的方式。上下文管理器防止在打开或读取文件时发生错误时的内存泄漏，因为它负责在任何情况下关闭文件。

这种方法与传统的方法没有本质上的不同，但它通常被认为是更好、更干净、更安全的方法。

既然我们已经排除了“默认”方法，那么让我们转到读取文件内容的更奇特的方法。

# 使用内置的文件系统模块

开发者说 Python 有一个模块来处理所有的事情。不出所料，它还有一个与文件系统交互的模块叫做`[pathlib](https://docs.python.org/3/library/pathlib.html)`。

用`pathlib`读取文件就像创建一个`Path`对象并调用它的`read_text()`方法一样简单，不需要手动关闭流。

尽管非常简单，但您通常不会在 Python 代码库中找到这种方法，因为它需要导入一个模块。

接下来，我们将看一看“黑客”，或者奇怪的、非默认的读取文件的方式，尽管如此，它们还是实现了最终目标。

# 使用外壳

如果您曾经使用过类似 UNIX 的操作系统，那么您肯定熟悉标准的`cat`命令。这个名字实际上来源于单词“con **cat** enate ”,与可爱的宠物无关。

内置的`subprocess`模块允许您运行 shell 命令，并从 Python 脚本中处理它们的输出。

对于那些拥有 Windows 操作系统的人来说，你可以使用类似的`type`命令来代替……或者直接切换到 Linux。

如果你认为这种读取文件的方式不寻常，那就勒紧裤腰带吧:我们变得越来越奇特了。

# 创建自定义 C 扩展

如果你对 Python 的内置函数不满意怎么办？您可以编写自己的自定义 C 扩展。为了保持文章简洁，我不打算解释为 Python 创建 C 扩展的完整过程。如果你有兴趣了解更多关于这个话题的信息，请查阅[这个指南](/speed-up-your-python-codebases-with-c-extensions-94859875eb70)。

首先，你要写 C 扩展:

然后你创建了`setup.py`脚本:

最后，构建并安装 C 扩展:

```
python3 setup.py build
python3 setup.py install --user
```

现在，您可以在 Python 脚本中导入您的自定义 C 模块:

对于读取文件内容来说，这有点过于复杂了，不是吗？下一种方法不包含任何 C 代码，但同样有些夸张。

# 构建一个 web 服务器来服务该文件

我们将使用 Python 内置的`flask`模块运行一个小型 web 服务器，通过 HTTP 发送文件。

或者，您可以使用`requests`库向服务器请求文件内容:

很不寻常，对吧？下一个将是一个真正的黑客。

# 暴力破解文件内容

有可能不打开文件就能读取它吗？正如您可能已经猜到的，我们将尝试所有可能的字节组合，并检查它们是否与实际的文件内容相对应。然而，为了实现这一点，我们不能直接在 Python 中打开文件，所以我们将利用返回给定文件的 md5 散列的`md5sum` UNIX 命令。

一旦我们有了校验和，我们就开始暴力破解，直到我们找到正确的字节组合。

这种方法既糟糕又可怕。在找到正确的文件内容之前，它必须检查的最大组合数是`256ⁿ`，其中`n`是以字节为单位的文件大小，而`256`是一个字节中可能的组合数。

无论这种方法效率有多低，您的 Python 脚本最终都会在不读取文件内容的情况下猜测文件内容，除非您的计算机先死机。

# 结论

总结一下，在 Python 中有无数种方法可以读取文件的内容。有些是有意义的，应该在真正的代码库中使用，而另一些是深奥的技巧，只是为了证明概念或进行编码练习。

我希望你喜欢这篇文章。如果你有任何其他用 Python 读取文件的奇特方法，请在评论中分享。感谢阅读！

如果你想了解更多关于 Python 的 C 扩展，我建议你看看下面这个故事:

[](/speed-up-your-python-codebases-with-c-extensions-94859875eb70) [## 用 C 扩展加速你的 Python 代码库

### 给你的 Python 程序带来 C 语言的速度

better 编程. pub](/speed-up-your-python-codebases-with-c-extensions-94859875eb70)