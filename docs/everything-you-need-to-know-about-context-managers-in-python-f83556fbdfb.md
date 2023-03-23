# 关于 Python 中的上下文管理器，您只需要知道

> 原文：<https://betterprogramming.pub/everything-you-need-to-know-about-context-managers-in-python-f83556fbdfb>

## 通过示例了解 Python 中“with”语句背后的魔力

![](img/be27b4a38b0de19ca2138ec6e37b8f1a.png)

照片由 [Aaron Lee](https://unsplash.com/@aaronhjlee?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

被一些人视为晦涩的特性，Python 中的`[with](https://docs.python.org/3/reference/compound_stmts.html#the-with-statement)`语句通常只在处理文件操作时使用。下面的例子展示了如何使用 Python 内置的`open`函数读写文件:

一个用 Python 打开文件的简单例子。

大多数情况下，所有人都知道使用`with`语句比使用`close`方法更适合管理文件。

事实是，大多数人都懒得去窥探幕后发生的事情。这里有一个提示:底层协议被称为[上下文管理器](https://docs.python.org/3/reference/datamodel.html#context-managers)。

对我来说，这一切都是魔法。老实说，谁会因为不知道我们为什么要使用它而烦恼呢？

在本文中，我将与您分享为什么我们应该使用上下文管理器以及一些涉及数据库连接的实际用例。您也许可以重构代码库的某个部分，将上下文管理器与`with`语句一起使用。让我们开始吧！

## TL；速度三角形定位法(dead reckoning)

*   避免打开任何文件或数据库连接，因为它们是有限的
*   上下文管理器通过告诉一个对象在被创建或销毁时做什么，允许我们更好地管理这些资源
*   `with`语句的使用允许我们减少代码重复

# 理由

## 资源管理

如果要我总结的话，就两个字:资源管理。

在构建任何应用程序时，我们都可以使用文件操作和数据库连接等资源。这里有一个关键要点:这些资源是有限的。

通常，我们需要在使用完这些资源后“释放”它们。例如，每当我们从文件系统中打开一个文件时，我们都需要在使用完该文件后显式地关闭它。

## 不要打开文件或资源

那么，这有什么不好呢？让文件或有状态资源不必要地打开是不好的，原因如下( [source](https://google.github.io/styleguide/pyguide.html#311-files-sockets-and-similar-stateful-resources) ):

*   它们可能会消耗有限的系统资源，比如[文件描述符](https://stackoverflow.com/questions/5256599/what-are-file-descriptors-explained-in-simple-terms/5256705#5256705)。如果在使用后没有及时返回系统，处理许多这样的对象的代码可能会不必要地耗尽那些资源。
*   保持文件打开可能会阻止其他操作，如移动或删除文件，或卸载文件系统。
*   在整个程序中共享的文件和套接字在逻辑上被关闭后可能会被无意中读取或写入。如果它们实际上是关闭的，试图从它们中读取或写入将会引发异常，使问题更快地被发现。

## “with”语句

那么，你会问,`with`语句或上下文管理器有什么用？

当然，每当我们在`[sqlalchemy](https://docs.sqlalchemy.org/en/13/orm/session_basics.html#when-do-i-construct-a-session-when-do-i-commit-it-and-when-do-i-close-it)`完成数据库事务时，调用`session.close()`没有任何问题。每次我们读和写一个文件的时候都必须调用内置的`close`方法也没有任何问题。就此而言，以下是其中一种方法的示例:

Python 中读写文件的一个烂例子。

正如您已经知道的，上面给出的例子非常冗长。现在，想象在你的代码库的每一个部分都这样做(我知道这很恶心)。

此外，一个贫穷、疲惫的开发人员很有可能在使用文件后忘记关闭文件(或数据库连接)。

因此，通常推荐使用`with`语句打开文件。使用`with`语句有助于编写更具表现力的代码，同时避免资源泄漏。

一个不太冗长的例子。

# 输入上下文经理

使用 Python 中的上下文管理器可以实现资源管理。本质上，上下文管理器有助于正确处理资源，为用户提供了轻松设置和拆卸资源的机制。

通俗地说，上下文管理器允许您控制在创建或销毁对象时做什么。

有几种方法可以在 Python 中创建可重用的上下文管理器。在下一节中，我将通过几个例子来演示如何用 Python 创建上下文管理器。

对于前两个例子，让我们创建一个简单的定制上下文管理器来替换 Python 中内置的 open 函数。

请注意，在实践中，我们应该始终使用 Python 提供的任何内置方法或上下文管理器。

## 1.基于类别

典型的例子是为您自己的上下文管理器创建一个 Python 类。默认情况下，每个上下文管理器类必须包含这三个 [Dunder 方法](https://www.geeksforgeeks.org/dunder-magic-methods-python/):

*   `__init__`
*   `__enter__`
*   `__exit__`

这些方法将按上述顺序执行。有关更详细的解释，请参考下面代码示例中的注释。

注意，下面的代码只能作为一个例子，应该用**而不是**来代替内置`open`函数的使用。

基于类的自定义上下文管理器，用于处理文件操作。

## 2.基于发电机

编写上下文管理器的另一个流行的替代方法是使用 Python 中内置的`[contextlib](https://docs.python.org/3/library/contextlib.html)`库。这是我个人喜欢的创建定制上下文管理器的方式。

总的来说，`contextlib`为我们提供了一组涉及`with`语句的常见操作的实用程序。

使用`contextlib`，我们可以省略为自定义上下文管理器编写 Python 类以及所需的 Dunder 方法。

一个基于生成器的定制上下文管理器，使用`contextlib`来处理文件操作。

## 3.使用内置的上下文管理器

一般来说，我们应该避免多此一举。我们应该总是选择使用任何可用的内置上下文管理器，如果它们可用的话。

例如，如果你正在使用 [SQLAlchemy](https://www.sqlalchemy.org/) ，这个库已经提供了一个管理[会话](https://docs.sqlalchemy.org/en/14/orm/session_basics.html#basics-of-using-a-session)的好方法。在使用 SQLAlchemy ORM 时，我们通常会这样做:

如何用`sqlalchemy`创建一个对象的例子。

我们可以使用内置会话作为上下文管理器，而不必每次都跨多个函数调用`session.rollback()`和`session.commit()`。

这里有一个更好的例子，我们可以将上下文管理器与`[sessionmaker](https://docs.sqlalchemy.org/en/14/orm/session_basics.html#using-a-sessionmaker)`一起使用:

使用带有`sessionmaker`的上下文管理器。

在这里使用`with`语句使我们的代码看起来更加整洁。

# 结束语

如果你已经做到了这一步，太棒了！总的来说，我们了解到的情况如下:

*   我们不应该让任何有状态的资源(文件、数据库连接、套接字)不必要地打开，因为它们是有限的。
*   上下文管理器允许我们通过在需要时分配和释放资源来更好地管理这些资源。
*   `with`语句有助于概括`try`、`finally`、`else`在异常处理时的标准用法。
*   上下文管理器的使用允许我们减少代码重复。

如果您的代码必须处理文件或数据库连接的打开和关闭，使用 Python 的上下文管理器和`with`语句是一个很好的选择。

就个人而言，我最喜欢使用上下文管理器的部分是它允许我们简化一些常见的资源管理模式。上下文管理器抽象他们自己的功能，因此允许他们被重构和重复使用。

仅此而已！感谢您的阅读！

## [最初发表于 jerrynsh.com](https://jerrynsh.com/when-to-use-context-managers-in-python/)

[](/3-useful-python-f-string-tricks-you-probably-dont-know-f908f7ed6cf5) [## 你可能不知道的 3 个有用的 Python f-string 技巧

### 关于 Python 的格式化字符串(f-string)你需要知道的事情

better 编程. pub](/3-useful-python-f-string-tricks-you-probably-dont-know-f908f7ed6cf5) [](/how-to-write-clean-code-in-python-5d67746133f2) [## 如何用 Python 写干净的代码

### 使用 Python 示例编写干净代码的 3 个技巧

better 编程. pub](/how-to-write-clean-code-in-python-5d67746133f2)