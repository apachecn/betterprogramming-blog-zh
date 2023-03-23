# 探索 Python 3.10 中的结构模式匹配

> 原文：<https://betterprogramming.pub/exploring-structural-pattern-matching-in-python-3-10-507e33a28a85>

## 期待已久的 switch-case 语句终于来了！

![](img/9b8f4ab0af397de18e00df794ef47323.png)

照片由[斯科特·韦伯](https://www.pexels.com/@scottwebb?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从 [Pexels](https://www.pexels.com/photo/purple-and-blue-abstract-wallpaper-430207/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄。

Python 是一种多用途语言，深受数据科学家和全栈工程师的喜爱。由于其语法上的甜蜜和哲理，它促进了代码的清晰和简洁。在本文中，我将解释结构化模式匹配——最受期待的 Python 特性——是如何工作的。

*注:Python 3.10 中首次引入了结构化模式匹配(PEP 634)。目前处于测试阶段，截止到 2021 年 5 月 27 日可以从* [*Python 的网站*](https://www.python.org/download/pre-releases/) *获得。*

# 什么是结构模式匹配？

最明显的类比是来自其他语言的`switch-case`操作符，比如 Java 或 C#。它允许您基于输入数据上的断言执行不同的操作。换句话说，它是多个`if-else`语句之上的语法糖。下面是在 Java 中如何使用`switch`语句:

在 Python 3.10 之前，用 Python 重写上述代码的唯一方法是一堆`if`语句:

这看起来没那么糟糕，但是随着案件的增长和变得更加复杂，这种结构将是无法忍受的。Python 开发人员发现了这一点，并为 Python 3.10 实现了一个新特性。

# 如何使用结构模式匹配

要应用结构模式匹配，您需要使用两个新的关键字:`match`和`case`。如果用 Python 3.10 重写，我们之前的例子看起来会是这样的:

首先，使用关键字`match`打开`match`块，关键字后面是要匹配的变量。然后，在`match`块中，编写带有`case`关键字的事例，后跟您希望变量等于的值。最后，`case _:`块将匹配任何东西。它是*默认的*处理程序。

让我们探索一些更有挑战性的例子。例如，您可以为单个`case`块指定多个条件，如下所示:

这个代码片段将打印出由`month`变量给出的一个月中的天数。为了使用多个值，我们用`|`操作符将它们分开。我们可以更进一步，通过使用 *guard* 表达式来检查闰年:

假设`is_leap_year`是一个函数，如果当前年份是闰年，则返回`true`。在这种情况下，Python 将首先评估条件，如果满足，它将打印`29`。如果不是，将进入下一个条件并打印`28`。

# 包装和拆包

结构模式匹配提供了比基本的`switch-case`语句更多的特性。您可以用它来评估复杂的数据结构并从中提取数据。例如，假设您将数据存储在一个格式为`(day, month, year)`的元组中——全部是整数。该代码将打印出季节以及日期和年份:

您可以看到，我们使用元组语法来捕获值。通过用`day`和`year`的名字来代替值，我们告诉 Python 我们希望这些值被提取出来并作为变量提供。通过用一个实际的数字值代替月份，我们只检查那个值。当我们检查`fall`时，您可以注意到我们再次使用了`_`符号。它是一个通配符，将匹配先前检查中遗漏的所有其他月份值。最后，我们还有一个通配符案例来说明格式错误的数据。

# 类别匹配器

Python 3.10 中的结构化模式匹配还有一个很酷的功能。假设你有一个这样的类:

```
class Vector:
    speed: int
    acceleration: int
```

您可以使用其属性来匹配`Vector`对象:

您可以看到，通过将参数作为构造函数参数传入，我们可以匹配任何类型的对象并捕获其参数。请注意，这样不会创建实际的对象，并且在类声明中不需要相应的构造函数。

# 何时使用结构模式匹配

当对数据进行分类或过滤时，这个特性对那些使用大数据结构的人很有帮助。清理大型的`if-elif-else`语句链也很有帮助。现在，这个特性还在 Python 3.10 的测试流中，所以暂时不要在生产项目中使用它。等待 2021 年秋季稳定发布。

# 结束语

感谢您的阅读。我希望你喜欢我的文章。在评论中让我知道你想在哪里使用新的结构模式匹配特性！

# 附加阅读

*   [全 Python 3.10 变更日志](https://docs.python.org/3.10/whatsnew/changelog.html#changelog)
*   [Python 3 . 10:4 中的新特性，你必须尝试一下](/new-in-python-3-10-4-features-you-should-try-out-d48db504500d)
*   [深度 Python 上下文管理器](https://levelup.gitconnected.com/python-context-managers-in-depth-52dd2dd2624b)