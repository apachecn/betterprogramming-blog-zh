# 使用 Python 的内置模块 Glob 来简化文件搜索

> 原文：<https://betterprogramming.pub/use-pythons-built-in-module-glob-to-simplify-file-search-75e82cf928f7>

## 当您可以使用 glob 时，为什么要使用一个 blob 代码将这样一个琐碎的任务复杂化呢？

![](img/9c13f83c0e3d773a0148f11a460ca11c.png)

[帕卡塔·高](https://unsplash.com/@pakata)在 [Unsplash](https://unsplash.com/photos/RDolnHtjVCY) 上的照片

Glob 是一个内置的 Python 模块，用于在文件系统中查找匹配特定模式的路径名。它的灵感来自 Unix shell 风格的模式匹配。

# 那么，用例是什么？

好了，我们现在有了一个关于模块的想法，但是用例呢？在什么情况下可以使用这种方法，避免使用大量代码，而是使用 glob 的内置函数在一行代码中完成相同的任务？让我们来看看。

## 用例:在文件系统的目录中查找具有特定扩展名的文件

这是一个非常常见的任务，几乎可以在所有 Python 程序中找到它的用途。假设我们想在一个图像处理任务的目录中找到扩展名为`.jpeg`的文件。让我们试试不使用 glob，然后使用 glob，看看是否会更好。

**不使用 glob**

创建一个简单的文件路径列表太麻烦了。首先，创建一个空列表，然后遍历路径并找到 jpegs。

**使用 glob**

真的就这么简单。一行代码！

# 结论

差不多就是这样。我们使用 Python 内置的 glob 模块极大地简化了查找 jpeg 文件列表的用例，否则需要相当多的代码来完成这样一个简单的任务。如今，许多使用 Python 的系统程序员可能做了很多这样的工作，并从中受益匪浅。对于那些仍然看不到使用 glob 的价值的人来说，如果他们知道 glob 也提供了比 os.walk 更好的性能改进，以达到相同的[结果](https://stackoverflow.com/questions/8931099/quicker-to-os-walk-or-glob)，他们可能会发现一些价值。

## 资源

*   [堆栈溢出](https://stackoverflow.com/questions/8931099/quicker-to-os-walk-or-glob)