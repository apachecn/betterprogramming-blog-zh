# 使用 PyPy 运行 Python 代码的速度提高了 30 倍

> 原文：<https://betterprogramming.pub/run-python-code-30-times-faster-with-pypy-5d717c32b273>

## 无需更改一行代码

![](img/80005f464c00792d8689625ba1a582f0.png)

用 DALLE-2 生成的图像

Python 因其难以置信的简单性和高度灵活性而非常棒。然而，由于它的设计选择，它不能像更多性能优化的语言一样运行得那么快。尽管如此，在将整个程序翻译成更高性能的语言之前，开发人员可以使用一些技术来提高 Python 代码的效率。

在本文中，我将向您展示如何使用 PyPy 毫不费力地显著提高 Python 代码的速度。

# PyPy 是什么

在此之前，Python 到底是什么？简而言之，Python 是一种编程语言:一套语法、句法规则和词汇，允许你编写消息来告诉计算机如何操作。信息可以用各种方式表达:通过演讲、纸张、无线电频率等。

编程语言可以用不同的方式实现:编译器、解释器、虚拟机等。比如 Python 一般是通过[的 CPython](https://en.wikipedia.org/wiki/CPython) 来实现的，它的默认解释器是用 c 写的，不过 CPython 也有替代实现，比如[的 Jython](https://www.jython.org/) (用 Java 写的)，[的 IronPython](https://ironpython.net/) (。NET)，还有 PyPy，是用 Python 写的。

PyPy，不要和 [Pypi](https://pypi.org/) 混淆，是 Python 中 Python 的重新实现，使用了 [RPython 翻译工具链](https://pypi.org/project/rpython/)。PyPy 的目标是成为 CPython 的快速替代方案，不需要修改源代码。得益于其 JIT 编译和更好的设计选择，PyPy 通常比 CPython 性能更好。

# 使用 PyPy

首先你要安装 PyPy。由于这个过程取决于您的系统，我将只链接到[官方下载页面](https://www.pypy.org/download.html)。请注意，许多 Linux 发行版都有一个漂亮的`pypy`或`pypy3`包。

一旦安装了 PyPy，基本上就完成了。现在，您可以像这样使用 PyPy 来执行您的 Python 程序:

```
pypy3 script.py
```

或者使用以下命令启动交互式 shell:

```
pypy3
```

# CPython 与 PyPy 性能

现在，我们知道 PyPy 比 CPython 快，但是快多少呢？没有明确的答案，因为它高度依赖于程序的执行:它有多大，代码结构，可预测性，等等。尽管如此，我们仍然可以执行一个基准测试，看看它们大致如何比较。

对于这个测试，我编写了一个执行一些操作的简单函数。然后我们使用`timeit`模块来检查代码执行需要多长时间。

然后，我们用 CPython 和 PyPy 执行这个测试脚本(姑且称之为`test.py`)并查看结果:

```
python3 test.py
Average time: 0.0838 seconds
pypy3 test.py
Average time: 0.0025 seconds
```

正如您所看到的，PyPy 远远超过了 CPython。整整快了 33 倍。

# PyPy 为什么快？

尽管 PyPy 解释器是用 Python 编写的，但它是编译成二进制的，因此省去了运行时解释的开销。

PyPy 使用实时(JIT)编译器，通过应用一些优化，在运行时生成一个更高效的代码版本，如果不编译，这些优化是不可能的。例如，JIT 编译器可以将那些经常使用的函数或代码块编译成机器码，从而为重复任务实现更好的性能，如前面的基准测试所示。

PyPy 的另一个重要方面是垃圾收集。我就不细说了，但是，简单来说，CPython 判断哪些对象死了的方式比 PyPy 的方式更复杂，从而给了后者速度上的优势。你可以在 realpython.com[的](https://realpython.com/pypy-faster-python/#garbage-collection)或者通过阅读[的官方文件](https://doc.pypy.org/en/latest/gc_info.html)了解更多关于 PyPy 的垃圾收集。

# PyPy 限制

到目前为止，PyPy 似乎是免费提高 Python 代码速度的神奇工具。那么，为什么它不是最流行的实现呢？不幸的是，PyPy 有一些限制。

首先，它不能很好地与 C 扩展和用 C 编写的模块一起工作。这些模块通常比纯 Python 模块慢，因为需要模拟垃圾收集中的引用计数，这不是 PyPy 中的默认做法。此外，您必须使用 PyPy 的 pip 为 PyPy 单独安装模块，如这里的[所示](https://doc.pypy.org/en/latest/faq.html#module-xyz-does-not-work-with-pypy-importerror)。

其次，正如我之前提到的，性能高度依赖于你的程序。非常小的程序不会从 PyPy 的 JIT 编译器中受益太多，因为在运行时将 Python 代码编译成机器码需要花费时间。JIT 编译器最适合运行至少需要几秒钟的程序，因为它需要时间“预热”然而，这并不是一个大问题:你不需要优化一个几乎不需要时间就能执行的程序。

使用 CPython 比 PyPy 性能更好的 Python 程序的一个例子是我不久前构建的这个数学解释器。并不是所有的程序都会从 PyPy 中受益，所以您应该经常检查是否是这样。

查看[该文档页面](https://doc.pypy.org/en/latest/cpython_differences.html)以获得 CPython 和 PyPy 之间差异的完整列表。

# 结论

总而言之，PyPy 是默认 CPython 解释器的一个很好的替代。它通常比 CPython 运行得更快，这要归功于它聪明的设计选择来提高运行时速度。PyPy 也非常接近成为 CPython 的替代产品，这要归功于它不断增长的模块支持。

然而，并不是所有的程序都会从切换到 PyPy 中受益，因为它的性能高度依赖于代码。因此，在选择一个实现时，应该经常比较两个实现的速度。一般来说，如果你的程序大量使用 C 语言编写的模块，那么 CPython 很有可能会更好。

我希望你喜欢这篇文章。如果你有什么要补充的，请在评论中分享你的想法。感谢阅读！

如果你对进一步优化你的 Python 代码感兴趣，看看下面这个故事:

[](/optimize-your-python-programs-for-free-with-slots-4ff4e1611d9d) [## 使用 __slots__ 将您的 Python 代码库优化近 3 倍

### 通过添加一行代码来提高执行速度和内存使用率

better 编程. pub](/optimize-your-python-programs-for-free-with-slots-4ff4e1611d9d)