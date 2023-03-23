# 你知道 Python 中存在 Float(“NaN”)和 Float(“INF”)吗？

> 原文：<https://betterprogramming.pub/did-you-know-float-nan-and-float-inf-exist-in-python-5a3b1054f4ce>

## 南！=南。虽然 inf == inf。但是为什么呢？

![](img/240850586a2382b00e6821e046efc8dd.png)

照片由 [Shubham Dhage](https://unsplash.com/@theshubhamdhage?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## NaN 和 Inf 是什么？

对于不熟悉`inf`和`NaN`符号的人来说，它们分别代表`infinity`和`Not-a-Number`。

尽管无穷大可以被认为是一个非常非常大的数，但它没有尽头。在 Python 中我们可以将负无穷大定义为`-inf`，正无穷大定义为`inf`。

数学上，实数中的`sqrt(-1)`或`0/0`(零过零)给出一个未定义的数，可以定义为`Nan`。在数据科学中，`Nan`用于表示数据集中缺失的值。所以`Nan`基本上是一个占位符，用来表示未定义或缺失的值。

您可以使用`float`类型创建`Nan`值。因为它不是 Python 中定义的关键字，所以必须以字符串格式(在引号内)将其传递给`float`。

```
float("Nan")
```

这里值得一提的是`"Nan"`不区分大小写。因此您可以使用下面的任何格式来创建一个`"Nan"`值。

都有效。如果您将它们中的任何一个赋值给一个变量并打印该值，您将在控制台上看到相同的内容:

```
nan
```

现在来说说`inf`。您可以像定义`Nan`值一样定义`inf`值。`Inf`也不区分大小写。您可以选择其中任何一个来创建`inf`:

但是它们会在控制台上打印相同的值:

```
inf
```

**注意:** `inf`和`Nan`只能由`float`类型定义。没有`float`您无法创建它们或将它们转换成另一种类型。

**例如:**

```
>>> int("inf")
```

**输出:**

```
Traceback (most recent call last):
  File "<input>", line 1, in <module>
ValueError: invalid literal for int() with base 10: 'inf'
```

**例 2:**

```
>>> int(float("nan"))
```

**输出:**

```
Traceback (most recent call last):
  File "<input>", line 1, in <module>
ValueError: cannot convert float NaN to integer
```

现在，让我们来看几个使用`Nan`和`inf`的例子。

```
>>> inf = float("inf")>>> 1 + inf
inf>>> inf * 2
inf>>> 0 - inf
-inf>>> inf * inf
inf>>> inf + inf
nan>>> inf - inf
nan>>> inf / inf
nan
```

第一个给出了无穷大，这是有意义的，因为无穷大超出了想象，但得到结果背后的想法并不太困难。最后几个是`nan`。嗯…在这篇文章中解释它们并不容易。如果你有好的解释，请与我们分享并评论它们！我推荐你在维基百科上阅读。

## **Inf 比较**

```
>>> float("inf") == float("inf")
True
```

当我们提到`inf`时，我们知道我们在谈论一个非常非常大的数，这个数是无穷无尽的，所以我们接受它在 Python 中的唯一性。否则，你声称有许多无限。

如果有很多，那你可以对比一下。这意味着它们中至少有一个有终点，所以它是有限的。正如你所猜测的，这是一个矛盾。正因如此，自然界和 Python 中都只存在负无限和正无限。

你可以将`inf`与任何数字相比较。举个例子，

```
>>> import sys>>> sys.float_info.max < float("inf")
True>>> float("inf") > float("-inf")
True
```

## NaN 的比较

```
>>> float("nan") == float("nan")
False
```

顾名思义,`Nan`是**而不是数字**,所以把它和其他任何东西或者它本身进行比较是没有意义的。你不能说一个未定义的数大于一个未定义的数。

或者说，一个未定义的数等于一个未定义的数。这毫无意义。你不能说`sqrt(-1)`等于`0/0`(两者都是`Nan`)。

当我们提到`Nan`时，我们不知道我们在谈论哪个`Nan`。有太多的`Nan`值，所以它们没有唯一的值。可以是`sqrt(-2)`或`sqrt(-3)`甚至是`inf/inf`。

请记住，每次我们写`float("nan")`时，它都会立即改变它的值。永远不会等于另一个`float("nan")`。

## **如何检查一个数是 inf 还是 NaN？**

如果你想检查一个变量是`Nan`还是`inf`，在标准的[数学模块](https://docs.python.org/3/library/math.html)中有两个函数正好达到这个目的。

```
math.isnan()
```

和

```
math.isinf()
```

就这些吗？当然不是。也可以使用 [NumPy](https://numpy.org/) 或 [pandas](https://pandas.pydata.org/) 库进行检查。

```
>>> nan = float("nan")
>>> inf = float("inf")>>> math.isnan(nan)
True
>>> math.isinf(inf)
True>>> import numpy as np
>>> np.isnan(nan)
True
>>> np.isinf(inf)
True>>> import pandas as pd
>>> pd.isna(nan)
True
```

# 结论

我们不能用常规的比较操作符(`==`或`!=`)进行比较来检查`Nan`。事实上，`Nan`不等于 Python 中存在的任何东西。它甚至不等于它本身！尽管如此，你可以使用我们刚刚学过的方法来检查一个数字是`inf`还是`Nan`。

我希望你从这篇文章中学到了新的东西。谢谢你阅读它。如果你喜欢你所读到的，看看下面这个故事:

[](/stop-using-or-to-check-multiple-conditions-in-python-404d31f2b569) [## 在 Python 中停止使用“或”来检查多个条件

### 还有更精密的解决方案！

better 编程. pub](/stop-using-or-to-check-multiple-conditions-in-python-404d31f2b569)