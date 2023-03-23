# Python 循环性能比较:最快的是…

> 原文：<https://betterprogramming.pub/python-loops-performance-compared-the-fastest-is-b4638744a1ff>

## 深入分析 Python for-each、for-range 和 while 循环，以找到最佳循环

![](img/b515f130f72fd4dd7b8b7321bca1cb61.png)

照片由 [Jonathan Chng](https://unsplash.com/@jon_chng?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 Python 中，主要有三种迭代对象集合的方法:for-each 循环、for-range 循环和 while 循环。虽然他们都完成相同的基本目标，但他们在幕后的工作方式不同。因此，它们的性能会有很大差异，并导致程序的执行速度不理想。

在本文中，我们将首先比较遍历性能基准的三种方式。然后，我们将探索每个循环对 Python 虚拟机来说实际上是什么样子，并讨论为什么一种方法比其他方法好。

# 性能基准

在进入 Python 循环的工作细节之前，我们首先确定哪种方法是最快的。下面的脚本比较了完成相同任务的三个函数的执行速度，使用`timeit`模块以不同的方式循环遍历一个列表:

你能猜出哪个循环最快，哪个效率最低吗？准备好之后，看看控制台输出的基准测试结果:

```
Foreach loop:       0.011 seconds
While loop:         0.076 seconds
For range loop:     0.042 secondsThe fastest method is Foreach loop with 0.011 seconds
```

正如您所看到的，for-each 循环比其竞争对手的表现好得多。此外，最快的竞争者和较宽松的 while 循环之间的执行速度差异很大:for-each 循环比 while 循环快六倍以上。甚至 for-range 循环也比 while 循环快近两倍。

现在，停一会儿，想想你用得最多的是哪个循环。是时候改变你的编程风格了吗？无论如何，无论您已经使用了最有效的方法还是任何其他较差的方法，都是时候探究一下为什么 for-each 循环比它的对应循环快得多了。

# Python 如何看待循环

您可能已经知道，Python 源代码首先被编译成特定的 Python 字节码，由 Python 虚拟机运行，该程序将这种与架构无关的字节码转换成机器指令，供 CPU 执行。为了分析 Python 你的源代码循环如何看到你的代码，我们可以看一下生成的字节码(你有没有注意到那些`.pyc`文件？).

为了让我们的生活更轻松，我们将使用[编译器资源管理器](https://godbolt.org/)，这是一个非常棒的在线工具，让您可以在舒适的网络浏览器中轻松分析编译后的源代码。

首先，我们来看看翻译成 Python 字节码的 while 循环。别担心，我会根据需要通过评论仔细解释一切。

这就是人们所期望的工作方式，不是吗？现在让我们更仔细地看一下 for-range 循环。不过，在这里我会开得快一点。

这里可以看到使用了内置的`range()`函数和`FOR_ITER`指令，而不是手动增加索引。速度优势在于使用内置特性，因为它们是用 C 实现的(因此已经编译成机器码)。更多信息，请看一下用 c 编写的默认 Python 解释器 [CPython](https://en.wikipedia.org/wiki/CPython)

最后，下面是 for-each 循环生成的字节代码:

如你所见，这里我们几乎只使用了内置特性，这些特性总是比纯 Python 要快。这就是为什么 for-each 循环比它的对应循环快得多，因为繁重的负载是由 Python 解释器本身以一种优化的方式处理的。

此外，for-each 循环需要更少的指令，从而使您的程序更小。你几乎不会注意到现实世界中的任何差异，但这仍然是一个理论上的好处。

现在，有些人可能会说，您的应用程序并不总是需要最高的性能，使用效率最低的循环类型对于许多用例来说并没有太大的区别。虽然这有时是正确的，例如在测试或原型开发时，编写优化的代码总是更好，特别是如果唯一需要的工作是使用内置的函数和特性，而不是在纯 Python 中自己实现它们。

此外，在考虑改变您的技术堆栈或在 CPU 内核或 RAM 等系统资源上花费更多之前，您应该检查您的软件是否有类似这样的潜在优化。

## 但是我还需要索引和元素

如果需要元素和列表索引，可以考虑使用 for-range 循环类型。然而，有一个 Python 内置函数`enumerate()`可以做到这一点。下面是这两种方法的性能比较:

基准测试结果:

```
For range loop:     0.043 seconds
For enumerate loop: 0.036 secondsThe fastest method is For enumerate loop with 0.036 seconds
```

你可以清楚地看到，内置函数总是比纯 Python 代码有优势。在这种情况下，索引列表和获取第 I 个元素的负担就从代码的肩上卸下来了，由 CPython 来处理。

如果您感兴趣，下面是 for-enumerate 循环的注释字节代码，它与 for-each 和 for-range 循环非常相似:

# 关键要点

总结一下，虽然 Python 中有很多循环方式，但显然更好的方法是使用 for-each 循环。一般来说，您应该总是更喜欢内置的 Python 函数和结构，因为它们比纯 Python 版本更优化，因为它们是用 C 编写的，并且已经编译成机器代码。

> 走向精通的第一步是了解你的武器，它们的长处和短处，并以最佳状态使用它们。

我希望你喜欢这篇文章。如果你有什么要补充的，请在评论中分享你的想法。感谢阅读！

如果您有兴趣了解关于编译器资源管理器的更多信息，我建议您查看下面的故事:

[](/compiler-explorer-a-must-have-tool-for-software-developers-98e7f4d6e94c) [## 编译器资源管理器:软件开发人员的必备工具

### 从一个舒适的浏览器选项卡中轻松分析、比较和优化源代码

better 编程. pub](/compiler-explorer-a-must-have-tool-for-software-developers-98e7f4d6e94c)