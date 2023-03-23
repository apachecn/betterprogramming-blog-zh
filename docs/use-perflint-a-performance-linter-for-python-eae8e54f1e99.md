# 使用 Perflint——Python 的性能 Linter

> 原文：<https://betterprogramming.pub/use-perflint-a-performance-linter-for-python-eae8e54f1e99>

## 尽早发现性能问题，并学习如何编写性能更好的代码

![](img/47016619ee21162a907fbd7b60ae6e73.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Chander R](https://unsplash.com/@chanderr?utm_source=medium&utm_medium=referral) 拍摄的照片

在当代软件开发中，无论是在测试工作中，还是在开发过程本身中，性能都占据了首要位置。

在本文中，我将展示如何轻松地将性能林挺集成到 python 应用程序开发中，并获得更好的代码。

# 性能工程

性能工程是软件工程中一个不断发展的领域，在这个领域中，性能因素被主动考虑。

一种更直观的性能方法是在项目结束时运行性能测试，并在所有功能需求都满足后使性能达到目标。

然而，这种方法似乎是无效的和昂贵的。

最近发表在《实证软件工程杂志》上的一项民族志[研究](https://link.springer.com/article/10.1007/s10664-021-10069-3#article-info)，旨在探索性能保证的常见实践。

研究人员连续采访了几名技术专业人员 6 个月，得出结论:

> 该研究表明，案例组织仍然依赖于瀑布式的绩效保证方法。这种方法对于 ASD(敏捷软件开发)来说是不充分的，因此导致了性能评估活动的次优管理。在尝试改进绩效保证流程时，我们提炼出三个关键挑战:(I)管理绩效评估活动，(ii)持续绩效评估，以及(iii)定义绩效评估工作。

这说明了在软件开发的生命周期中优先考虑性能保证的重要性，并且坚持敏捷方法而不是瀑布方法。

# 过早优化

> 真正的问题是程序员在错误的地方和错误的时间花了太多的时间担心效率；过早的优化是编程中所有罪恶(或者至少是大部分罪恶)的根源。

这句话摘自 1974 年图灵奖获得者唐纳德·克努特 1968 年的著作《计算机编程的艺术》。

Knuth 认为(记住，这句话已经有 60 多年的历史了…)程序员倾向于凭直觉思考性能。

他们通常会假设代码中较难理解的部分是机器执行起来最耗时的部分。

总的来说，他的声明意在警告程序员不要在没有[分析](https://en.wikipedia.org/wiki/Profiling_(computer_programming))的情况下盲目优化他们的代码。

然而，许多程序员断章取义，把他的话理解为好像任何优化程序的努力都必须发生在应用程序开发的最后阶段，而这个论点是错误的。

性能优化是软件设计和实现的一部分，和所有其他软件开发工作一样，它应该是风险和收益方面的优先事项。

# 介绍 perflint

我在之前的一篇文章中谈到了棉绒。

Performance linter 是一个更具体的 linter，旨在检测代码中的性能反模式。

正如我在上一篇文章中所展示的，`pylint`可以很容易地用插件来扩展，以添加更多的 checkers 功能。

所以令人敬畏的 python 开发者和作者 Anthony Shaw 一直在开发一个插件来扩展`pylint`以检测[性能问题](https://github.com/tonybaloney/perflint)。

使用这个 linter，python 开发人员可以提高他们的应用程序性能，还可以了解 python 的内部机制以及它如何影响性能。

# 亲自动手

让我们来看看一段不太有用的代码:

所以它有一个设置为 2 的全局变量、`main`函数和`main`子句。
main 函数有一个整数变量的本地列表，它遍历列表并打印由全局变量驱动的每个数字。

让我们运行代码:

```
python example.py
```

输出:

```
1
4 
9 
16
```

现在让 lint 这段代码，首先，我们需要使用 pip 安装`pylint`:

```
pip install pylint
```

现在让我们运行`pylint`:

```
pylint example.py
```

结果是:

```
--------------------------------------------------------------------
Your code has been rated at 10.00/10 (previous run: 10.00/10, +0.00)
```

没有发现错误，我的代码得分为 10/10。

现在让我们把`perflint`加入聚会:)

```
pip install perflint
```

现在我们可以运行`pylint`并使用`perflint`作为插件了！

```
pylint --load-plugins perflint example.py
```

结果:

```
************* Module __main__
myapp\__main__.py:8:27: W8202: Lookups of global names within a loop is inefficient, copy to a local variable outside of the loop first. (loop-invariant-global-usage)
myapp\__main__.py:6:16: W8301: Use tuple instead of list for a non-mutated sequence (use-tuple-over-list)------------------------------------------------------------------
Your code has been rated at 7.14/10 (previous run: 5.00/10, +2.14)
```

Perflint 发现了 2 个问题，我的代码分数降到了 7.14！

让我们检查这两个问题:

## 问题 1-在循环中查找全局名称

让我们把注意力集中在循环上，我们正在遍历列表，然后我们使用全局变量。

查找全局变量需要 python 解释器在名称和索引的整个范围内执行查找，这就是 python 字节码解释器的`[STORE_NAME](https://docs.python.org/3/library/dis.html#opcode-STORE_NAME)` [](https://docs.python.org/3/library/dis.html#opcode-STORE_NAME)`opcall`

但是当你定义一个函数的时候，它有固定数量的局部变量，这样 python 就可以设置一个固定的数组，轻松找到与局部变量名相关联的值，这就是 python 字节码解释器的`[STORE_FAST](https://docs.python.org/3/library/dis.html#opcode-STORE_FAST)` [](https://docs.python.org/3/library/dis.html#opcode-STORE_FAST)opcall。

所以一个快速的解决方法是将全局变量存储在一个局部变量中，然后我们可以在循环中使用这个局部变量。

现在让我们再来看一下`perflint`:

```
pylint --load-plugins perflint example.py
```

结果:

```
************* Module __main__
myapp\__main__.py:6:16: W8301: Use tuple instead of list for a non-mutated sequence (use-tuple-over-list)------------------------------------------------------------------
Your code has been rated at 8.75/10 (previous run: 7.14/10, +1.61)
```

酷，第一个问题解决了。

现在我们来解决第二个问题。

## 问题 2-使用元组而不是列表

我们代码中的列表变量从来不会发生变化，所以在这种情况下建议使用不可变对象。
这里首选的不可变对象是一个[元组](https://docs.python.org/3/tutorial/datastructures.html#tuples-and-sequences)

所以让我们来解决这个问题:

现在`perflint`:

```
pylint --load-plugins perflint example.py
```

结果:

```
-------------------------------------------------------------------
Your code has been rated at 10.00/10 (previous run: 8.75/10, +1.25)
```

满分！

# 奖金

让我们比较一下我们代码中的快速和慢速示例:

使用`[timeit](https://docs.python.org/3/library/timeit.html#module-timeit)`,我们可以评估“快速”版本到底快了多少:

运行:

```
python example.py
```

结果:

```
fast_main execution time = 7.155288799898699 usec
slow_main execution time = 7.3095553000457585 usec
```

“快”版本为 7.15 微秒，“慢”版本为 7.31 微秒，非常明显。

感谢阅读。