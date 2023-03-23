# 从你的 Python 代码库中抛弃这 7 个习惯

> 原文：<https://betterprogramming.pub/ditch-these-7-habits-from-your-python-codebases-e38abe904d7b>

## 用代码示例打破 Python 中的坏习惯

![](img/47bededd9ae44f7d97d1191c32197504.png)

由 [Kelly Sikkema](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我犯了所有这些坏习惯。我花了一些时间来摆脱他们。

Python 以其简单性和多功能性而闻名。作为最常用的编程语言之一，有很多很棒的教程。不幸的是，简单易学的语法有时会误导开发人员，尤其是那些语言新手。编写[干净的 Python 代码](/how-to-write-clean-code-in-python-5d67746133f2)可能会很棘手。

在本文中，我们将深入探讨一些人们在刚开始学习 Python 时可能会犯的常见错误和坏习惯。这包括我希望有人早点告诉我的事情。

别担心，我不想用一堆文字来烦你。相反，你会得到易于理解的代码片段。

## TL；速度三角形定位法(dead reckoning)

**停止:**

1.  使用`open`和`close`代替`with`
2.  使用空列表作为默认参数
3.  避免列表理解
4.  滥用列表理解
5.  使用裸异常
6.  用错了`==`和`is`
7.  使用`import *`

# 1.使用`open`和`close`代替`with`语句

处理文件流可能是我们开始学习 Python 时最常做的事情。大多数教程会以下面的例子告诉我们打开和关闭文件开始:

```
f = open("stocks.txt", "r")
data = f.read()
f.close() # NOTE: Always remember to close!
```

在这种情况下，我们经常会被提醒在使用完文件后关闭它，因为:

*   它消耗了我们有限的系统资源
*   它阻止我们移动或删除文件

相反，这里的最佳实践是在处理文件操作时总是使用 Python 中的[上下文管理器](/everything-you-need-to-know-about-context-managers-in-python-f83556fbdfb)(`with`语句)。这里有一个例子:

```
 with open("stocks.txt", "r"):
    data = f.read()
```

在这里使用上下文管理器也有助于我们在异常期间关闭文件。

嗯，你可能会说我们也可以在这里使用`try-finally`方法来代替。尽管如此，`with`语句将我们的代码简化为两行。

# 2.使用空列表或字典作为默认参数

对于 Python 新手来说，这可能是最令人困惑的问题之一。

我必须承认，这一点在我多年前刚开始学习 Python 时让我措手不及。我记得当我第一次接触到使用函数时缺省参数的奇妙之处时，我像不关任何人的事一样盲目地使用它。这里有一个例子:

当一个函数被定义时，默认参数(如上面例子中的`storage`)只被创建(即求值)一次**。**

**由于 Python 中的列表是可变的，所以每次调用`add_book`函数时，列表都会被修改。这里，相同的列表被反复使用，而不是创建新的列表。**

## **你应该做什么**

**同样的概念也适用于其他可变对象，例如 Python 中的 dictionary。**

# **3.不使用列表理解**

**列表理解无疑是 Python 中最显著的特性之一。毫无疑问，它有助于提高代码的可读性，并且通常被认为更加优雅和“[python 化](https://www.computerhope.com/jargon/p/pythonic.htm)”。**

**话虽如此，新的 Python 开发人员可能很难充分利用这一特性，或者至少在开始时习惯于使用它。这里有一个例子:**

**学会使用列表理解**

**如果你是 Python 的新手，放松一下，花点时间学习 list comprehensions。我保证，这对你会有很大的帮助。**

**同样的逻辑也适用于在 Python 中使用字典理解。**

# **4.滥用列表理解**

**我们明白了。列表理解是“pythonic 式的”。然而，我也看到了很多列表理解被以各种形式滥用的例子。**

**更糟糕的是，一些列表理解被编写到代码甚至不再可读的程度，违背了使用列表理解的最初目的。**

**这里有一个你不应该做的例子:**

**不要做这些**

**虽然列表理解更紧凑，运行更快，但我们应该避免编写长而复杂的列表理解(尤其是在一行中)，以确保代码的可读性。不要陷入理解列表的困境。**

# **5.使用裸除**

**我对此感到内疚。很内疚。作为初学者，我不喜欢一些代码库如何显式地捕捉异常。当时，我觉得它们过于冗长和多余。我想我只是太懒了。**

**这里有一个例子，展示了一个光秃秃的例外是什么样子的:**

**避免这样做**

**简而言之，我们不应该使用 bare，除非它捕获所有异常，包括一些不想要的异常，例如本例中的`KeyboardInterrupt`,我们想要退出应用程序。**

**最重要的是，它使调试成为一场噩梦，因为它会导致我们的应用程序失败，而我们却不知道原因。所以，[停止像这样使用异常](/stop-using-exceptions-like-this-in-python-2bd8ba7d8841)。**

# **6.用错了`==`和`is`**

**但这真的重要吗？我在野生 Python 代码里见过太多了。如果你不知道，他们是不同的。**

*   **`is`操作员检查这两个项目是否引用相同的对象(即出现在相同的存储位置)。**又名参考**等同性。**
*   **`==`运算符检查值是否相等。**

**这里有一个简单的例子来说明我的意思:**

**记住，`listA`和`listB`是不同的对象**

**这里总结了使用`is`和`==`运算符的注意事项:**

**使用 if baz 和 if not baz**

# **7.使用导入星号(星号)**

**许多人会认为这是一种权宜之计。但这只是简单的懒惰。**

**通过使用`import *`，您冒着污染当前名称空间的风险，因为它从库中导入了所有的函数和类。**

**“污染命名空间”是什么意思？通俗地说，这基本上意味着你用`import *`导入的任何函数(或类)都可能与你定义的函数冲突。**

**基本上，你冒着覆盖变量或函数的风险，最终很难区分哪个函数是哪个函数。**

# **结束语**

**Python 是一种相对容易入门的编程语言。它提供了许多机制和范例，极大地提高了我们作为开发人员的生产力。**

**话虽如此，人们很容易养成用 Python 写糟糕代码的习惯。回想起来，这篇文章也提醒我(希望你也是)远离这些糟糕的 Python 编码习惯。**

## **下一步做什么**

**这篇文章中有太多的禁忌。**

**这里有一些你可以做的事情——开始使用像 [autopep8](https://pypi.org/project/autopep8/) 、 [flake8](https://pypi.org/project/flake8/) 和 [pylint、](https://pypi.org/project/pylint/)甚至 [mypy](https://mypy.readthedocs.io/en/stable/) 这样的工具来保持你的代码质量处于最高标准。他们会通过帮助你对照像 [PEP8](https://www.python.org/dev/peps/pep-0008/) 等标准来检查你的代码，让你成为一个更好的开发者。**

**我希望这篇文章中的提示对您有所帮助，并祝您编码愉快！你用什么工具来检查你的代码质量？**

```
**Want to Connect With the Author?**This article was originally published at [jerrynsh.com](https://jerrynsh.com/ditch-these-7-habits-from-your-python/)
```