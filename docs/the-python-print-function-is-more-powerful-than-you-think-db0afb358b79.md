# Python 的' print()'函数比您想象的更强大

> 原文：<https://betterprogramming.pub/the-python-print-function-is-more-powerful-than-you-think-db0afb358b79>

## 探索分隔符、代码注入等等

![](img/8515f74110bb5b43cefe18f614713167.png)

埃斯特万·洛佩兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Python 编程语言非常适合初学者，这要归功于它简单的类似英语的语法和强大而直观的内置函数。您的第一行 Python 代码很可能是这样的:

```
print("Hello World")
```

随着您对这门语言越来越熟悉，您开始了解内置`print()`函数的不同功能。作为 Python 开发人员，您获得的经验越多，您发现的边缘案例、技巧、奇特的语法和高级特性就越多。

在本文中，我将向您展示`print()`函数可以做什么，从它的基本功能开始，然后涵盖越来越多的高级用例以及有趣的技巧。

# 基本 print()用法

使用`print()`语句的最基本方式，也是最常见的方式是将一条消息(字符串或另一个对象)记录到控制台。

不出所料，控制台输出如下:

```
Hello World
```

现在，让我们进入鲜为人知的功能。

# 结束字符和分隔符

`print()`函数还允许您用`end`参数指定一个附加在字符串末尾的字符。例如，如果您想在主参数后打印序列`"***"`，您可以这样做:

这将输出以下文本(末尾没有换行符):

```
Hello World***
```

如果您希望从输出中排除换行符，以便在同一行上打印所有内容，您可以像这样设置`end=''`:

分隔符是 Python `print()`语句的另一个关键特性。首先，您可以使用同一个函数调用打印多个值，用逗号分隔它们:

```
Hello World
```

`print()`函数隐式地设置它的参数`sep=' '`，这样传递的参数在打印时用空格隔开。但是，您可以指定自己的字符串来分隔每个参数:

```
Hello***World
HelloWorld
```

## 自定义对象表示

在 Python 中打印复杂对象时，您通常会指定它们应该如何显示为字符串，而不是依赖默认的非描述行为:

```
<__main__.Entry object at 0x7fc3ddd5ffd0>
```

所以，当你写一个类的时候，你可以覆盖`__str__()`和`__repr__()` [dunder 方法](https://www.geeksforgeeks.org/dunder-magic-methods-python/)，让 Python 知道它应该如何处理打印的对象。

```
Entry: a=1
```

我总是喜欢显式地设置`__str__()`和`__repr__()`，但是你不需要这样做。

# 使用 print()写入文件

你有没有想过`print()`是如何知道在哪里输出给定的消息的？默认行为是将字符串写入`sys.stdout`流，但是您可以用`file`参数指定输出文件。

这段代码创建了一个名为“output.txt”的文件，并向其中写入了“Hello World”。这是可能的，因为`print()`隐式地调用您传入的流的`.write()`方法，所以这相当于

```
f.write("Hello World\n")
```

考虑到这一点，您可以使用`print()`函数创建一个初级记录器。

或者，更好的办法是，您可以覆盖`print()`函数，通过将每个打印语句记录到一个文件中来扩展它的功能。

使用这种方法，您可以轻松地在文件中进行每个`print()`调用，并自动将消息记录到文件中。然而，如果你喜欢那种东西，你最好使用`[logging](https://docs.python.org/3/library/logging.html)` [模块](https://docs.python.org/3/library/logging.html)。

## 通过打印到文件来执行代码

如果您可以黑掉`print()`函数，让它执行任意代码会怎么样？如果你还记得，我提到过`print()`调用你传入的文件的`.write()`方法。记住这一点，您可以用自己的代码覆盖或重写此方法。

```
This is the payload.
Payload function called with arguments: Hello World
This is the payload.
Payload function called with arguments
```

或者，您可以覆盖`sys.stdout.write()`来触发任何默认`print()`调用的有效负载函数。

```
Executing payload
Hello WorldExecuting payload
```

您可能已经注意到，Python 调用了`payload()`函数(因此也调用了`.write()`方法)两次，所以您可能必须处理这个问题。

这种代码注入技术可能没有很多真实世界的用例，但是谁知道人们能想出什么呢？

# 第三方库代码注入

虽然前面显示的代码注入不会造成任何伤害，但下一个示例可能会。如果您从事编程已经有一段时间了，那么您一定听说过使用第三方库会给您的源代码带来一些安全威胁。

在这个例子中，我将向您展示供应商如何包含由`print()`函数触发的恶意代码。

然后，你可以在你的程序中使用这个类:

或者，您可以在恶意模块中覆盖全局`sys.stdout.write()`,这样每个`print()`调用都会执行您的有效负载。

现在，你可能会说有人在看源代码时会立即注意到这些恶意的代码行。我有时会检查我自己使用的图书馆。将恶意代码隐藏在 C 扩展中怎么样？

与 Python 文件相比，Python 开发人员不太可能从 Python 包中检查 C 源文件。另外，如果你在安装时在本地编译扩展，反病毒程序(如果你使用了的话)应该相信生成的二进制文件来自用户。或者说[代码混淆](https://en.wikipedia.org/wiki/Obfuscation_(software))怎么样？

意识到软件开发人员有多脆弱是很有趣的。然后你得到一个反向的 shell，用用户权限运行一个键盘记录器来获取 admin/root 密码，最后接管系统。

虽然我不会具体展示如何实现这一点，但您可以看看这篇关于为 Python 编写 C 扩展的指南:

[](/speed-up-your-python-codebases-with-c-extensions-94859875eb70) [## 用 C 扩展加速你的 Python 代码库

### 给你的 Python 程序带来 C 语言的速度

better 编程. pub](/speed-up-your-python-codebases-with-c-extensions-94859875eb70) 

# 结论

总而言之，`print()`函数比 Python 开发人员通常所知的功能多得多，从简单地打印到控制台，到写入文件，再到执行任意代码。毕竟，`print()`功能只是一个工具，因此，你可以破解它来实现不同的目标。

> 画笔只是一根粘有刚毛的棍子。这取决于艺术家创造性地运用它。

我们能说一个拿着画笔的艺术家只是一个拿着棍子的黑客吗？

我希望你喜欢这篇文章。如果您知道任何其他关于`print()`功能的实践或理论用例，请在评论中告诉我们。

感谢阅读！

如果你想让你的 Python 代码更专业、更易维护，看看这个故事:

[](/how-to-write-clearer-python-code-with-type-hints-330216291b7c) [## 如何用类型提示编写更清晰的 Python 代码

### 通过使变量和函数更加自文档化来避免错误

better 编程. pub](/how-to-write-clearer-python-code-with-type-hints-330216291b7c)