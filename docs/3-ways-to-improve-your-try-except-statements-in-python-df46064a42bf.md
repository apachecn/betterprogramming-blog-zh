# 改进 Python 中 Try/Except 语句的 3 种方法

> 原文：<https://betterprogramming.pub/3-ways-to-improve-your-try-except-statements-in-python-df46064a42bf>

## 确保有效异常处理的提示

![](img/c18aa3b377fdcad22193e8604ce43fc1.png)

由 ThisIsEngineering 拍摄:[https://www . pexels . com/photo/female-software-engineer-coding-on-computer-3861951/](https://www.pexels.com/photo/female-software-engineer-coding-on-computer-3861951/)

Try 和 except 语句是学习 Python 时最先接触的工具之一。它们使用户能够测试代码中的错误并处理异常。

不幸的是，人们很容易以这样的方式编写 try 和 except 子句，要么不会产生最大的好处，要么最终给他们带来很多麻烦。

在这里，我们回顾了用户在 Python 中编写有效的 try/except 语句可以做的三件事:

# 1.避免使用 Pass 语句

让我们从解决使用 try/except 语句时可能犯的一个更严重的错误开始:在 except 子句中包含 pass 语句。

pass 语句充当占位符，在某些情况下确实有它的用途。然而，它不属于 except 子句。

为了说明为什么会出现这种情况，让我们编写代码，尝试将用户输入的两个数字相除，在 except 子句中插入一个 pass 语句。

从表面上看，这些代码可能是良性的。毕竟，它将毫无问题地运行。但是，如果分割不成功并引发异常，我们将完全不知道。

通常，这可能会造成问题，因为 try/except 语句的结果可能会影响后续操作。如果除法在 Python 脚本中很重要，我们需要知道何时获得或未获得该值。

因此，跟踪 except 子句被触发的时间是一个很好的做法。

我们可以通过用 print 语句替换 pass 语句来改进前面的代码。这样，我们就知道何时可以或不可以执行分割。

# 2.在 Except 子句中指定异常

虽然跟踪 except 块被触发的情况是一个好习惯，但是没有指定错误类型的用户将无法知道为什么 try 子句中的代码不能运行。

Python 中有许多类型的异常。识别引发的异常使用户更容易理解他们的代码及其输出。查看一下 [Python 的内置异常](https://docs.python.org/3/library/exceptions.html)以熟悉可能出现的错误类型是值得的。

通过前面的代码，我们可以知道该分部何时会引发异常，但我们不知道它是什么类型的异常。理论上，如果用户输入一个字母而不是一个数字，除法会产生一个`ValueError`，或者如果用户将`0`指定给分母，除法会产生一个`ZeroDivisionError`。这种分裂也可能引发一个我们没有预料到的错误。

在 except 子句中指定异常可以给我们更详细的反馈。我们现在可以知道为什么会出现错误，而不是只知道错误何时出现。

让我们通过为每个异常创建单独的 except 子句来修改代码:

有了这种安排，如果我们未能执行 try 块，我们可以判断错误是来自分配一个非数字输入，一个 0 到分母，还是另一个来源。

# 3.包括 F `inally`条款(如适用)

`finally`子句虽然不是强制性的，但是可以包含在 try/except 语句中。

无论 try/except 语句中运行哪个子句，用户都可能希望在最后执行相同的任务。

对于我们当前的例子，让我们假设无论运行哪个代码块，我们都希望在末尾包含一个 print 语句。

这是一种可行的方法，但是在多个块中重复同一行代码会导致冗余。此外，如果我们想改变这种说法，我们必须在每一个条款中进行修改。

用户可以利用`finally`子句来执行给定的代码，而不是在多个地方编写同一行代码，不管之前执行了哪个块。

在这里，我们修改了 try/except 语句，加入了以下子句:

有了`finally`子句，我们可以用更简洁、可读性更好的代码获得同样的结果。此外，它使调试或任何种类的修改更容易执行。

# 结论

![](img/18f34bdd6d59abb7ff16eae8580ab3b3.png)

照片由 [Prateek Katyal](https://unsplash.com/@prateekkatyal?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

最终，这些改进 try/except 语句的技巧是基于我编写和阅读他人脚本的经验。

避免 pass 语句、指定异常类型、利用`finally`子句都是用户编写易于阅读和调试的 try/except 语句的方法。

编码快乐！