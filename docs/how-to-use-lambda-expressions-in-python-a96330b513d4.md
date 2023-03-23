# 如何在 Python 中使用 Lambda 表达式

> 原文：<https://betterprogramming.pub/how-to-use-lambda-expressions-in-python-a96330b513d4>

## 它们一开始看起来可能很吓人，但其实很简单

![](img/87bea2e5066f59b8d85c669ef00020c1.png)

aaron Blanco Tejedor 在 [Unsplash](https://unsplash.com/s/photos/expression?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Lambda 表达式总是让我抓狂。我不知道为什么……我认为这个名字——纪念阿隆佐·邱奇对 lambda 微积分的介绍——让它们听起来复杂或者太“数学化”了。

或者，可能是因为我没有遇到一个好的用例来了解为什么它们是必要的。

术语*匿名函数/表达式*也用来迷惑我。它的行为是否不同于普通的`def`函数？它们的利弊是什么？兰姆达斯如何融入？

# 我是如何理解兰达斯的

直到我重温了一些 JavaScript 并开始实现[箭头函数](https://medium.com/better-programming/learning-javascript-arrow-functions-36cce13351c2)时，我才恍然大悟。

它帮助我学会了抛弃 lambdas 的匿名性，并开始将它们理解为用于编写简单函数的精简语法。

让我们快速看一下转换成 lambda 表达式的传统函数。

```
# traditionally defined function to multiply a number by itself
def traditional_square(n):
   return n ** 2# lambda function to multiply a number by itself
lambda_square = lambda n: n ** 2print(traditional_square(5)) # 25
print(lambda_square(5)) # 25
```

我们所做的只是重新安排了一些组件并清理了不必要的语法。没有使用`def`来定义一个函数，而是使用了`lambda`这个词，后面紧跟着参数列表。

此外，省略了关键字`return`，进一步压缩了语法。最后，虽然定义是匿名的，但我们将它存储到变量`lambda_square`中，有效地命名了它。

# 函数与 Lambdas 的异同

既然我们已经通过将 lambdas 与传统的函数定义联系起来，弄清楚了它们的基本语法，那么让我们来看看两者之间的一些关键的相似之处和不同之处。

*   *函数和 lambdas 都可以接受零个、一个或多个参数。在 lambda 表达式中，只需在关键字`lambda`后列出参数，用逗号分隔。*
*   从技术上来说，Lambdas 是表达式，因此它们只能包含其体内的其他表达式。这应该有助于理解为什么 lambdas 不使用`return`关键字。
*   *只有功能*可以有多行。从技术上来说，一个 lambda 可以用反斜杠(`\`)写跨多行；然而，子弹的本质是，只有函数可以有多个语句，有效地做多件事。Lambdas 仅限于正文中的单个表达式。

# 我什么时候用兰姆达斯？

有三个机会开始将 lambda 表达式编织到您的编程实践中。

## **将“一行程序”函数重构为 lambda 符号**

这是最容易实现的，也是熟悉语法的好方法。

对于已经是单个语句或者可以简化为单个语句的函数，尝试将它们重写为 lambda 表达式，类似于我上面展示的。

## **2。** `**.sort()**` **列表方法**

与其他接受 lambda 表达式的方法相比，`.sort()`没有什么特别之处；在我的经验中，这只是一个常见的用例。

在原始值列表中——想想整数或字符串——列表自然会按值排序。

```
scores = [54,36,75,85,13,46]scores.sort()print(scores) # [13,36,46,54,75,85]
```

但是像元组、字典或类这样的复杂数据类型呢？

您可以使用 lambda 表达式来指定您希望如何执行排序。

```
scores = [("Jade",21),("Rab",110),("Sylvando",39),("Erik",19)]scores.sort(key=lambda t: t[1]) # sort by 2nd value in tupleprint(scores)
"""
[
  ("Erik",19),
  ("Jade",21),
  ("Sylvando,39),
  ("Rab",110))
]
```

在上面的例子中，我们将排序的关键字定义为 lambda 表达式的结果。列表中的每一项都被设置为`t`，并且`t[1]`中的值用于对元组进行排序。

## **3。** `**filter()**` **功能**

类似于`.sort()`，这个函数以一个函数作为第一个参数。第二个参数是可迭代的，比如 list。采用上面的元组列表，我们可以创建一个过滤迭代器。

```
scores = [("Jade",21),("Rab",110),("Sylvando",39),("Erik",19)]result = filter(lambda t: t[1] > 25, scores)print(list(result)) # [("Rab",110),("Sylvando",39)]
```

我想澄清一下`filter()`函数的一些细微差别。

函数的结果存储在变量`result`中，因为与`.sort()`方法不同，`filter()`不修改原始数据。

需要创建一个列表是因为返回了一个过滤器对象，它指向迭代器的过滤版本。如果我们试图在不创建列表的情况下打印，输出将类似于`<filter object at 0x7f3fb03ce7d0>`。

# 结论

我希望对 lambda 表达式的介绍有助于消除一些恐惧和忧虑。如果你有其他简单的帮助你克服困难的例子，请分享它们！