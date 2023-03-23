# 在 Python 中处理错误

> 原文：<https://betterprogramming.pub/handling-errors-in-python-9f1b32952423>

## 在 Python 中优雅地处理错误的最佳实践

![](img/fb257a2fa77dd7e69d6bebde2a7d1bc2.png)

照片由 [Alexandru Acea](https://unsplash.com/photos/XEB8y0nRRP4) 在 [Upsplash](https://unsplash.com/photos/XEB8y0nRRP4) 上拍摄。

> "最大的错误是认为我们永远不会犯错。"托马斯·卡莱尔

在开发可维护和健壮的代码时，添加大量的错误处理是至关重要的。错误可以分为几类:逻辑错误、生成错误、编译时错误和运行时错误。在本文中，我们将关注处理运行时错误——程序运行时发生的错误。

# *为什么我们需要错误处理？*

## 1.防止程序在发生错误时崩溃

*   如果程序中出现错误，我们不希望程序意外地对用户崩溃。相反，错误处理可以用来通知用户错误发生的原因，并优雅地退出导致错误的进程。

## 2.节省调试错误的时间

*   按照第一个原因，让程序显示一个错误而不是立即崩溃将会在调试错误时节省大量时间。
*   可以更新错误处理程序内部的逻辑，以便为开发人员显示有用的信息，例如代码回溯、错误类型等。

## **3。帮助定义项目需求**

*   如果程序由于错误的输入而崩溃，错误处理器可以通知用户错误发生的原因，并定义程序的需求和约束。

# 错误处理实践

在本文中，我们将讨论以下内容:

*   使用`try - except`和`try - except - finally`进行异常处理
*   断言
*   何时使用异常与断言

如果您应用了这些错误处理技术，您将会感谢自己。

# 捕捉和处理异常

![](img/425cfa8671e3034dc22f849e465dfe41.png)

Jakob Owens 在[上拍的照片。](https://unsplash.com/photos/PVPsGlhs3rU)

如果您有一个可能失败的代码块，您可以通过将该代码放在一个`try: … except: …` 块中来管理任何异常:

错误处理按如下方式执行:

1.  执行`try`块中的语句。
2.  如果语句成功，则跳过两个`except`子句，运行`finally`子句中的代码。
3.  如果`try`块中的语句失败，则执行第一个`except`语句中的代码。如果由于`ValueError`(即不能将非数字转换为整数)语句失败，则运行`except ValueError`块中的代码。
4.  如果`try`块内的语句失败且错误不是`ValueError`，则检查第二个`except`语句。如果语句由于`ZeroDivisionError`失败(即整数被零除)，则运行`except ZeroDivisionError`块内的代码。
5.  无论最后一个任务是在`try`块中还是在`except`块中,`finally`子句总是在最后一个任务完成后执行。

## 处理异常时要注意的事情

*   不要让代码吞掉异常。我们不希望简单地忽略错误而不被发现。如果你需要吞下一个异常来避免一个基本问题，程序的架构需要重新评估。

```
try:
    y = 100 / xexcept ZeroDivisionError:
    pass 
```

*   不要在可能无法到达的`try`语句中声明新变量。

# 引发异常

您可能需要重新引发一个异常来中止脚本。例如，如果我们不能确定是哪种错误导致了异常，我们可能希望重新引发它:

# 用户定义的异常

有几种类型的内置异常类继承自同一个基类`Exception`。这些内置类的完整列表可以在官方文档中找到[。](https://docs.python.org/3/library/exceptions.html#Exception)

也可以创建一个从基类`Exception`继承的定制异常类。如果开发人员希望集成更复杂的日志记录系统或进一步检查对象，可能需要一个自定义类。

定义`Exception`类时需要`__init__()`和`__str__()`方法:

# 断言

断言评估表达式为真或假。如果表达式为 false，python 将引发一个`AssertionError`异常。在测试代码时，断言可以作为一个强大的开发工具。

断言的语法是`assert Expression[, Arguments]`:

```
a = 20
assert a < 10, "something went wrong"
```

上面的代码会抛出这个错误:

```
Traceback (most recent call last):
  File "file.py", line 2, in <module>
    assert a < 10,  "something went wrong"
AssertionError: something went wrong
```

# 那么什么时候我们应该使用断言还是异常呢？

这实际上取决于具体情况，这里有争论的余地。在我看来，由于用户输入、硬件、网络等原因，在处理外部输入和输出时应该使用异常。当您想要正常退出程序、记录数据并通知用户为什么会出现这样的错误时，应该使用异常。

断言有一种快速失败的方法，应该用来查找代码中的错误和检测 bug。

如果您的生产代码中有断言，我的建议是确保异常处理被设置为捕捉任何`AssertionErrors`。万一断言在生产中失败，至少代码会通过理想地退出程序、记录问题并通知用户来安全地处理异常。

# 额外资源

*   [Python 3 错误处理](https://docs.python.org/3/tutorial/errors.html)
*   [Python 3 断言](https://docs.python.org/3/reference/simple_stmts.html#the-assert-statement)

# 结论

编码快乐！