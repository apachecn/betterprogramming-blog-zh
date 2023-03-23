# 解决这 5 个问题来理解 Python 日志记录

> 原文：<https://betterprogramming.pub/address-these-5-questions-to-understand-python-logging-e8a45718819>

## 当您不应该将打印用于记录目的时

![](img/b2c36c33a0eaf22b6eef0c7067bc229d.png)

由 [Artiom Vallat](https://unsplash.com/@virussinside?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

软件开发中最令人沮丧的事情是调试一个无法重现的问题。您可能不得不依靠一些不太懂技术的最终用户的各种轶事描述来猜测发生了什么——如果您在交付软件之前没有正确设置日志记录。但是，如果您碰巧有足够的日志信息可以帮助您跟踪问题的根源，那么消除 bug 可能会花费更少的时间。

在本文中，我将讨论您在 Python 项目中登录时可能会遇到的五个问题。本文的重点将是内置的日志模块。看完这篇文章，希望你对 Python 中适当的日志记录有一个合理的认识。

# 1.它为什么不使用 snake_case 命名约定？

众所周知， [PEP 8](https://www.python.org/dev/peps/pep-0008/) 规定了我们应该如何在 Python 中命名事物。本质上，这就是所谓的“snake_case”命名约定。例如，我们有一个发送消息的函数。蟒名应该是`send_message`。然而，其他一些语言使用 camelCase 作为函数名。在这种情况下，函数应该命名为`sendMessage`。

与标准库中的其他模块不同，`[logging](https://docs.python.org/3/library/logging.html)`模块不使用 snake_case 命名。相反，它遵循 camelCase 命名约定。原因是这个模块是作者 Vinay Sajip 贡献的。根据[作者](https://old.red-dove.com/python_logging.html)的说法，这个模块的开发受到了 Java 的[日志包](https://docs.oracle.com/javase/8/docs/api/java/util/logging/package-summary.html)和 Apache 的 [log4j](https://logging.apache.org/log4j/2.x/index.html) 的启发，这两者都使用了 camelCase 命名约定。作者在本模块中继续了这一约定。好的一面是，尽管该模块不遵循 snake_case 命名约定，但由于它使用了 camelCase 命名约定，因此具有内部一致性。因此，考虑到模块的一致性，您不太可能对模块中的各种名称感到困惑。

对于感兴趣的用户，关于这个话题可以参考这个[关于堆栈溢出的讨论](https://stackoverflow.com/questions/22993667/how-come-the-pythons-logging-module-doesnt-follow-pep8-conventions)。

# 2.伐木工是什么？

当我们使用日志模块时，我们通常做的第一件事是实例化`logger`对象。从技术上讲，我们可以通过调用`Logger`构造函数来创建一个`logger`对象:

```
import logginglogger_not_good = logging.Logger("my_app")
```

然而，正如上面的`logger`名称所示，使用构造函数创建`logger`并不是一个好的做法。相反，最佳实践是使用模块级函数`getLogger`，它将为给定的应用程序检索相同的`logger`对象:

```
logger_good = logging.getLogger("my_app")
```

如下所示，多次调用`getLogger`函数检索同一个对象，如其相同的内存地址所示。相比之下，使用构造方法将创建多个实例:

记录器对象

`logger`对象是控制应用程序或模块日志行为的中心对象。您应该考虑的一些重要事情包括日志级别、处理程序和格式，这些将在接下来的几节中讨论。

# 3.日志记录级别意味着什么？

并不是所有的软件问题都有相同的优先级。有些现在需要修复，有些则不需要。我们可以将相同的逻辑应用于我们的日志系统。通过使用不同的日志记录级别，我们可以突出问题的紧迫性/重要性。

在日志模块中，有五个日志级别(或者六个，如果包括初始的`NOTSET`级别)。这些级别在日志模块中被定义为常量，如下所示:

日志记录级别

使用`logger`，我们可以设置应该保留的日志记录信息的级别。我们将级别设置为`warning`，这意味着`warning`及以上级别(即`error`和`critical`)的所有日志消息都将被记录:

```
logger = logging.getLogger(**"my_app"**)
logger.setLevel(logging.WARNING)
```

从概念上讲，我们可以把`logger`的级别看作是记录器对日志事件的响应。级别越低，记录器的响应性或包容性就越强。例如，如果您将`logger`的级别设置为`debug`，所有日志记录消息都将被记录器捕获。

# 4.什么是操控者？

在上一节中，我们讨论了设置我们的`logger`的日志记录级别。然而，突出的问题是`logger`如何写它接收到的日志事件。这是训练者发挥作用的地方。

为了长期保存，通常的做法是将日志事件写入文件。在这种情况下，我们需要为`logger`指定文件处理程序:

记录到文件

*   我们首先通过实例化`FileHandler`类创建了一个文件处理程序，并将其设置为`logger`。
*   我们向`logger`发送了一些日志事件，包括`error`、`critical`和`info`日志消息。
*   通过检查日志的内容，我们注意到只有`error`和`critical`消息被记录。还记得我们把`logger`的等级设为`warning`吗？因为`info`电平低于`warning`，所以`info`消息不会被`logger`捕获。

这个例子只是向您展示了文件处理程序如何与`logger`一起工作。日志模块还支持其他类型的处理程序。下面的代码片段向您展示了我们可以创建一个`StreamHandler`实例，它将在默认情况下登录到交互控制台:

记录到控制台

如您所见，本质上，处理程序是这样指定的，即`logger`知道在哪里记录日志消息。

# 5.我怎样才能以一种更方便读者的方式登录？

我没有提到的一件事是*如何*记录日志。但是您可能已经注意到了我们为发送日志消息所做的工作。最方便的方法是简单地使用模块级函数，这些函数用函数本身指定的级别创建日志事件，如下所示:

```
logger.critical(**"This is a critical message."**)
logger.error(**"This is an error message."**)
logger.warning(**"This is a warning message."**)
logger.info(**"This is an info message."**)
logger.debug(**"This is a debug message."**)
```

实际上，这些日志消息是日志模块的`LogRecord`类的实例。`LogRecord`实例的一些重要属性包括日志的时间戳(即`asctime`)、日志事件的级别(即`levelname`)和日志的消息(即`message`)。这些属性对于通过应用格式来进行更有意义的日志记录是必不可少的，下面将对此进行讨论。

但是，从调试的角度来看，这些消息并没有太大的意义。至少，我们应该知道这些事件是何时发生的。我们可以对`logger`应用一些格式来为我们记录这些信息。更具体地说，格式是通过处理程序配置的。换句话说，我们可以为不同的处理程序设置不同的格式。

格式化日志记录

*   在这段代码中，我们创建了一个`Formatter`对象，它将记录格式化的时间、日志级别和日志事件的消息。
*   如果需要额外的格式，可以进一步设置显示所需信息的`Formatter`对象，比如模块名。
*   日志记录的输出更加结构化，显示了时间戳，这对将来的调试非常有用。

# 结论

本文通过关注日志模块，概述了 Python 中的日志记录过程。我们讨论了与本模块相关的五个具体问题。我希望读完这篇文章后，您可以对日志模块有一个初步的了解。如果想进一步探讨这个话题，可以参考[官方文档](https://docs.python.org/3/library/logging.html)。

简单回顾一下，您可以尝试以下步骤来登录您的项目:

1.  通过调用`getLogger` 函数为您的应用程序检索`logger`。
2.  给你的`logger`设置一个合适的日志级别。
3.  创建适用的处理程序。为了长期保存，您可能希望将信息记录到物理文件中。
4.  为处理程序指定格式，处理程序可以有不同的格式设置。
5.  将手柄连接到您的`logger`上。
6.  检查你的日志。