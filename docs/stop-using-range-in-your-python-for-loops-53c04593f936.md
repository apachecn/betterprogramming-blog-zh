# 停止在 Python for 循环中使用 range()

> 原文：<https://betterprogramming.pub/stop-using-range-in-your-python-for-loops-53c04593f936>

## 如何使用 enumerate()函数访问当前索引

![](img/001af46caeed07ba4877d7a78e3248e4.png)

由[班特尔抓拍的照片](https://unsplash.com/@bantersnaps?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/range?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上

`for`循环。它是编程的基石——一种你作为新手学习的技术，也是一种你将在余下的编程旅程中一直掌握的技术。

如果您来自其他流行的语言，比如 PHP 或 JavaScript，那么您应该熟悉使用变量来跟踪您的当前索引。

理解这些`for`循环实际上并不遍历数组是很重要的；它们通过一个表达式手动迭代，该表达式充当引用每个数组值的代理。

在上面的例子中，`i`与`scores`没有明确的关系，它只是碰巧与每个必要的索引值一致。

# 旧的(坏的)方法

如上图所示的传统的`for`循环在 Python 中并不存在。然而，如果你像我一样，你的第一本能是找到一种方法来重新创造你感到舒服的东西。

结果你可能发现了`range()`函数，想出了这样的东西。

这个`for`循环的问题是它不太“Pythonic 化”。我们实际上并没有遍历列表本身，而是使用`i`作为代理索引。

事实上，甚至在 JavaScript 中也有直接迭代数组的方法(`[forEach()](https://medium.com/better-programming/stop-using-for-loops-to-iterate-over-arrays-5c46940e79d1)`和`[for…of](https://medium.com/better-programming/use-for-of-to-loop-through-your-javascript-arrays-57ebb900ab5a)`)。

# 使用 enumerate()函数

如果您想正确跟踪 Python `for`循环中的“索引值”,答案是使用`enumerate()`函数，它将“计算”一个 iterable——是的，您可以将它用于其他数据类型，如字符串、元组和字典。

该函数有两个参数:iterable 和可选的起始计数。

如果起始计数未通过，则默认为`0`。然后，该函数将返回每个当前计数的元组以及 iterable 中相应的值。

```
scores = [54,67,48,99,27]for i, score in enumerate(scores):
   print(i, score)
```

这段代码非常简洁。我们避免处理列表索引，迭代实际值，并在`for`循环的定义中显式地看到每个值。

这里有一个奖励，你是否曾经想打印一个编号列表，但必须打印`i + 1`，因为第一个索引是`0`？只需将值`1`传递给`enumerate()`，就可以观看魔术表演了！

我希望这个教程是有帮助的。你还发现了`enumerate()`功能的哪些其他用途？有没有发现语法比`range(len())`更容易读懂？下面分享一下你的想法和经验吧！