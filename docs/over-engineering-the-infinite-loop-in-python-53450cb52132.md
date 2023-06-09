# 过度设计 Python 中的无限循环

> 原文：<https://betterprogramming.pub/over-engineering-the-infinite-loop-in-python-53450cb52132>

## 在 Python 中创建无限循环的 7 种方法

![](img/c613254c135b575455ddd778c11d2488.png)

照片由[飞:D](https://unsplash.com/@flyd2069?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Python 以高度灵活的编程语言而闻名。它提供了无数实现同一目标的方法——一些比另一些更合理。

在本文中，我将向您展示在 Python 中实现无限循环的七种方法，从最常见的方法开始，逐渐过渡到更复杂和奇特的技术。

# 经典而真实

处理无限循环最 Pythonic 化的方法是使用一个 while 循环，它有一个恒定的真值:`True`。

很直白吧？但同时也很无聊。让我们用下一个技术变得更有趣。

# 无限递归列表

在编程中，没有函数的无限递归这种东西，因为那会导致[堆栈溢出](https://en.wikipedia.org/wiki/Stack_overflow)。然而，数据结构可以毫无问题地引用自己。

在本例中，我们定义了一个空列表，并将其附加到自身。如果我们试图遍历嵌套列表，我们将永远不会到达末尾。这一事实可被利用来创建无限循环并在其主体中执行代码。

# 无限 for 循环

除了无限循环，for 循环在 Python 中无处不在。你不能仅仅用`for i in range(math.inf):`遍历一个无限的范围。然而，如果你知道 Python 迭代器是如何工作的，你可以写你自己的无限迭代器。

for-in 循环的工作原理是为每个新的迭代调用 iterable 对象的`__next__()`方法(例如`range`、`list`、`str`……)。如果 iterable 已被完全消耗，则会引发一个`StopIteration`异常。所以，无限循环的诀窍是永远不要引发`StopIteration`。这里有一个例子:

如您所见，在`__next__()`方法中没有`StopIteration`。我敢打赌，你从来没有使用过，甚至没有听说过 Python 中的无限 for 循环。从现在开始，事情会变得更加奇特。

# 转到循环

乍一看，你们中的一些人的反应可能是:“什么？Python 什么时候有 goto 语句了？”。

2004 年 4 月 1 日，一个名为`goto`的模块作为一个愚人节玩笑发布了。正如您所料，它的目的是为 Python 添加 goto 特性。虽然这只是一个玩笑，不应该在真正的代码中使用，但它仍然有效。

这个模块很旧，与 Python3 不兼容，但我发现它与最新版本的 Python2 兼容得很好。你可以在[这个链接](http://entrian.com/goto/)找到`goto`模块。

一些开发者也写了这个模块的 Python3 兼容版本，但是由于某些原因我不能让它工作，所以我不会提到它。

# 统计无限循环

到目前为止，我们只看到了在任何情况下都不会停止的真正无限循环。以下示例显示了一个中断概率极低但不完全为零的循环。

循环条件是`total`变量小于一个大数。在循环内部，`total`变量增加、减少或保持不变的机会不相等。从统计学上来说，`total`应该保持在零左右，加上或减去一些随机波动，这些波动加起来不太可能是这个条件下的大数字。

# 无限移位链接队列

除了听起来像一些奇怪的编程动画角色的特殊举动，这也是一种通过从队列中弹出一个元素并立即将其添加回来实现无限循环的技术。

名称中的“链接”部分是因为这个队列是通过一个链表实现的，这是必要的，因为 Python 不允许常规列表有这种行为。

以下是链接队列的代码:

这就是你在循环中使用它的方式:

因为每次从队列中弹出一个元素时，它都会被推回队列中，`node`永远不会是`None`，从而实现了一个无限循环。或者，您可以通过`__iter__()`和`__next__()`魔术方法使这个类可迭代，并使用 for-in 循环来代替。

这个例子还说明了为什么在遍历一个结构时不应该修改它，因为这可能会导致不必要的行为。

# 无限无理数循环

现在让我们从数据结构跳到数学。在数学中，无理数是那些不能表示为两个整数之比的实数。无理数的另一个重要特性是它们在小数点右边有无穷多(不重复)的数字。

因为这种“数字无止境”，无理数有无限的小数位，因此需要无限的时间和运算来精确计算。我们可以利用这个事实，把要重复的代码注入到无理数的计算中。

以下函数计算给定数字的平方根，没有小数近似值:

所以，如果我们给它一个平方根是无理数的数，它将永远需要计算答案，从而实现一个无限循环，因为数学。比如我们可以尝试计算`2`的平方根，我们知道它是无理数:

```
sqrt(2)
```

除非注入的代码返回，否则函数永远不会返回。

# 结论

用 Python 编程非常灵活，有无数种方法可以达到同样的效果。虽然有些方法有意义，但其他方法只是过度设计的技术，只是为了证明概念或进行编码练习。

> 无限只是一种错觉。当计算机最终被时间的镰刀割掉时，即使是“虽然真实”也会结束。

我希望你喜欢这篇文章。如果你知道任何其他有趣的在 Python 中实现无限循环的方法，请在评论中分享。感谢阅读！

这是本系列的第四篇文章，在这篇文章中，我探索了用 Python 实现简单任务的各种有趣的、过度设计的方法。如果你错过了最后一个故事，你可以在下面找到它:

[](/over-engineering-the-hello-world-in-python-352844417d37) [## 在 Python 中过度设计“Hello World”

### 不使用 print()打印到控制台的 7 种方式

better 编程. pub](/over-engineering-the-hello-world-in-python-352844417d37)