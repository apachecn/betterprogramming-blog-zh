# 停止使用 Print 来调试 Python 代码

> 原文：<https://betterprogramming.pub/stop-using-print-to-debug-your-python-code-2ace9217f501>

## 开始使用内置的日志模块

![](img/33c9524a5d3f0f86e0fba8edeaaf8093.png)

[艾蒂安·吉拉尔代](https://unsplash.com/@etiennegirardet?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

我从 Bash 脚本转向 Python 已经有一段时间了。来自操作世界，我在编写更复杂的代码时会犯一些错误。代码越复杂，调试起来就越复杂。直到最近，我还在使用与 Bash 相同的东西进行调试:语句`print`。

在 AWS 中，成本管理是一件大事。在我个人的工作中，我们在下班后关闭所有非生产环境中的数据库，以节省资金。基本上，这是由 GitLab CI 管道触发的，它关闭应用程序堆栈，然后停止数据库。

您可能知道，Python 默认缓冲输出(这可以用`-u`参数设置为无缓冲模式)。因此，当我停止数据库时，我无法在作业结束前获得输出——除非我使用了这个技巧。我没有修改 Python exec 的行为，而是寻找一种更好的方法来处理消息。这时我发现了日志模块。

> "这个模块定义为应用程序和库实现灵活事件记录系统的函数和类。
> 
> 使用标准库模块提供的日志 API 的主要好处是，所有 Python 模块都可以参与日志记录，因此您的应用程序日志可以包含您自己的与来自第三方模块的消息相集成的消息。”— [Python 的官方文档](https://docs.python.org/3/library/logging.html)

因此，让我们看看如何实现它，以获得可读性更好的代码！

首先要理解的是如何配置模块。我不会在这里解析所有可用的选项，但我将只谈论要知道的主要三元组:

*   默认日志记录模式(参数级别)
*   日志格式(格式参数)
*   日志输出(`stdout`，文件…)(文件名参数)

```
import logginglogging.basicConfig(level=logging.DEBUG, format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', filename='myFirstLog.log')
```

这很容易初始化。这里，我们只是向日志记录对象传递一些参数来调试详细信息，用一些有趣的值格式化输出，并将我们的日志重定向到一个专用文件(默认为`stdout`)。

谁不喜欢举重？我确定没有人。基于此，我创建了下面这个使用日志模块的类(注意，我没有使用本教程的 filename 选项来获得`stdout`上的所有输出) :

这里我们能注意到什么？上面的代码中使用了一些不同的`prints`:`DEBUG`-`INFO`-`ERROR`。如果您检查第 16 行和第 19 行，日志选项被设置为`DEBUG`。这基本上是我们不想拥有的信息，如果我们要用这个代码来戳。同样的事情也适用于所有的日志语句。您可以选择在日志文件中包含什么内容！

这里的一个快速优化在第 27 行:

```
logging.info("Your barbell is now %s", self.total_weight)
```

我们也可以写:

```
logging.info("Your barbell is now {}".format(self.total_weight))
```

这两种说法有什么区别？只有当你的日志等级为`INFO`或更低(即`DEBUG`)时，才会调用第一个函数，`%s`翻译只在这种情况下进行。第二个每次都会变换(调用`str` ) `{}`，即使日志级别设置为`ERROR`。请注意，我这里只说的是`{}`替换——而不是打印日志。因此，如果您有适当的日志级别，第一个问题只涉及一个操作，反之则没有。对于第二个，如果您有正确的日志级别，每次都有一个操作*—*两个。**

**让我们调用我们的类，看看如果我们将光标一次放在`DEBUG`上，一次放在`ERROR`上，会发生什么:**

**`DEBUG`模式输出:**

```
**2020-09-17 14:18:54,075 - root - DEBUG - You're a woman
2020-09-17 14:18:54,075 - root - INFO - Your barbell's weight is 15
2020-09-17 14:18:54,075 - root - INFO - Your barbell is now 35
2020-09-17 14:18:54,075 - root - INFO - Congrats, you've jerked 35 easily
2020-09-17 14:18:54,075 - root - ERROR - You forgot to init your barbell, please call genderBarbell function first !
2020-09-17 14:18:54,076 - root - ERROR - Cannot clean and jerk here, please init your barbell, please call genderBarbell function first !
2020-09-17 14:18:54,076 - root - DEBUG - You're a man
2020-09-17 14:18:54,076 - root - INFO - Your barbell's weight is 20
2020-09-17 14:18:54,076 - root - INFO - Your barbell is now 140
2020-09-17 14:18:54,076 - root - ERROR - Weight is too high for you ! Decrease weight even if there is no function for that because it is a tutorial !**
```

**`ERROR`模式输出:**

```
**2020-09-17 14:20:36,809 - root - ERROR - You forgot to init your barbell, please call genderBarbell function first !
2020-09-17 14:20:36,809 - root - ERROR - Cannot clean and jerk here, please init your barbell, please call genderBarbell function first !
2020-09-17 14:20:36,809 - root - ERROR - Weight is too high for you ! Decrease weight even if there is no function for that because it is a tutorial !**
```

**正如你所看到的，这里没有什么复杂的，但这将使你的代码更具可读性和可理解性！**

**我希望你喜欢这个快速教程。下一集再见！**