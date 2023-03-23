# 关于用 Python 读写文件的备忘单

> 原文：<https://betterprogramming.pub/a-cheat-sheet-on-reading-and-writing-files-in-python-e78297adf413>

## 如何读写文件的快速参考

![](img/012c81c3411f6630cd32b446421e98b9.png)

照片由[Á·阿尔瓦罗·塞拉诺](https://unsplash.com/@alvaroserrano?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/read-write?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我们需要读写文件，这是 Python 编程中的一个常见任务。

这篇简短的教程并不打算涵盖 Python 中文件读写的所有内容。相反，我只想与您分享一些我在日常 Python 编程中为读写文件而创建的备忘单。

在我们继续下一步之前，只需要注意一些内务管理提醒。

*   这里给出的代码是用 Python 3.7.3 编写的，并不总是适用于 Python 的早期版本。
*   我们将把重点放在作为标准包的一部分的内置方法上，这样我们就不需要安装对初学者来说不太简单的第三方包。
*   您可以选择任何您喜欢的 Python IDE。对我来说，我使用 Visual Studio 代码和 Mac 的终端来运行我的代码。

# TL；速度三角形定位法(dead reckoning)

这里是本文的两个快速备忘单。第一页列出了本文中讨论的读写文件所涉及的关键方法/功能。

第二页列出了打开文件进行正确读/写操作时使用的常见打开模式。

备忘单 1:关键方法/功能

备忘单 2:常见的文件打开模式

# 打开一个文件

## 打开()

在 Python 中处理文件的第一步是打开文件。为此，您可以使用`open()`方法，这将创建一个文件对象。具体来说，这个方法有一组用于读取文件的参数，但是最常用的两个参数是文件名和模式。

假设出于本教程的目的，我们有以下名为`python_test_file.txt`的文本文件。如果您想在学习本教程的同时尝试这些方法，可以在 Python 工作目录中创建一个如下所示的方法。

```
0 Start Line 0 - Line 0 End 0
1 Start Line 1 - Line 1 End 1
```

要打开这个文件，我们可以像下面这样简单地调用`open()`方法。我们指定了文件名和打开模式。从终端可以看到，创建了一个文件对象或文本流(即`TextIOWrapper`)。

```
>>> open('python_test_file.txt', 'r')
<_io.TextIOWrapper name='python_test_file.txt' mode='r' encoding='UTF-8'>
```

上面的列表(即备忘单 2)总结了常见的开放模式及其预期用途。如果省略打开模式，默认模式将是`r`。

要以二进制模式打开文件，您需要在上面列出的模式后面加上字母`b`(例如`rb`、`rb+`)。当文件不包含文本时，应该使用二进制模式。

## with 关键字

当打开一个文件时，我们可以使用`with`关键字。

使用`with`关键字允许文件在套件完成后自动正确关闭，即使在过程中出现异常。

```
>>> with open('python_test_file.txt') as file:
...     file.read()
... 
'0 Start Line 0 - Line 0 End 0\n1 Start Line 1 - Line 1 End 1'
>>> file.closed
True
```

在上面的代码片段中，有几件事值得注意。

*   当省略 mode 参数时，文本文件以文本模式读取，行尾转换为`\n`。
*   使用`with`关键字，文件对象按预期关闭。

# 阅读文件

## 阅读()

你可能知道，我们刚才使用了`read()`方法。在此方法中，您可以指定要从 file 对象中读取的大小。

当`size`参数被省略或设置为负数，或设置为大于文件大小的整数时，将读取所有文件内容。

我们也可以指定一个小于文件大小的大小。在下面的示例中，通过将大小指定为 5，将只读取文本文件的前 5 个字符。

```
>>> with open('python_test_file.txt') as file:
...     file.read(5)
... 
'0 Sta'
```

如果读取后到达文件的末尾，将返回一个空字符串(`''`)。

对于下面给出的例子，第二个`read()`方法返回一个空字符串，因为第一个调用已经完成了整个文件的读取。

```
>>> with open('python_test_file.txt') as file:
...     file.read()
...     file.read()
... 
'0 Start Line 0 - Line 0 End 0\n1 Start Line 1 - Line 1 End 1'
''
```

## 读取线()

我们也可以使用`readline()`方法读取单独的行。该方法将读取整行的文本，包括行尾(即`\n`)。

然而，如果文件不以换行符结尾，那么文件的最后一行将省略`\n`。当读取到文件末尾时，将返回一个空字符串。

如果多次调用`readline()`方法，将从上次读取的地方继续读取。

实际上，`readline()`方法也可以接受一个`size`参数，该参数将用作读取行的限制。

在下面的代码片段中，`readline(2)`和`readline(5)`将分别读取 2 和 5 个字符。同样，在上次读取的地方继续读取。

还有一点需要注意，一个空行作为`'\n'`返回——一个只包含一个新行的字符串。

## 读取行()

如果你想用一个方法读取所有的行，你可以使用`readlines()`，它将创建一个单独行的列表。

```
>>> with open('python_test_file.txt') as file:
...     file.readlines()
... 
['0 Start Line 0 - Line 0 End 0\n', '1 Start Line 1 - Line 1 End 1']
```

`readlines()`方法也可以有选择地设置一个`size`提示。

当它被设置为正整数时，它将从文件中读取那么多的字符(或者二进制模式中的字节),足以完成那一行。

在我们的例子中，我们的第一行是 29 个字符。因此，1 到 29 的大小将导致读取第一行，而 30 或更大的大小将读取两行，如下所示。

## for 循环

如果我们需要迭代一个文件的所有行，你可以使用`for`循环，这是一个内存效率高、速度快、代码更清晰的循环。

```
>>> with open('python_test_file.txt') as file:
...     for line in file:
...         print(line, end='')
... 
0 Start Line 0 - Line 0 End 0
1 Start Line 1 - Line 1 End 1
```

# 写文件

## 写()

要向文件中写入新值(如文本)，我们可以使用`write()`方法。

但是，新值如何写入文件取决于备忘单 2 中总结的打开模式。

例如，`w`模式将截断文件，因此文件将只包含新值。`a`模式将允许您向现有文件添加新值。

```
>>> with open('python_test_file.txt', 'w') as file:
...     file.write('This is a writing method.')
... 
25
```

上面的代码在文件打开之前截断文本后，将指定的字符串写入文件。

另一个注意事项是，`write()`方法将自动打印所写的字符数，这可以通过将返回值赋给一个下划线来抑制:`_ = file.write(‘str to be written’)`。

在写入操作之后，您可以看到文件的当前文本。正如所料，文件中只包含新的字符串。

```
>>> with open('python_test_file.txt') as file:
...     file.read()
... 
'This is a writing method.'
```

## writelines()

我们也可以使用`writelines()`方法向文件中写入多行。

这个方法将接受一个字符串列表作为参数。使用这种方法如何写行(例如，重写或追加)类似于`write()`方法的实现。

```
>>> with open('python_test_file.txt', 'w') as file:
...     file.writelines(['Line 0\n', 'Line 1'])
...
```

执行上述代码后，文件的文本变成默认的`read()`方法读取的`‘Line 0\nLine 1’`。

# 关闭文件

当你完成了对文件的操作后，最好通过调用 file 对象上的`close()`方法来关闭文件。

如下图，文件关闭前，`closed`方法返回`False`，我们调用`close()`方法后它返回`True`。

```
>>> file = open('python_test_file.txt')
>>> file.closed
False
>>> file.close()
>>> file.closed
True
```

# 结论

这些是适用于大多数用例的常见读写方法。

如果您在 Python 编程中执行读写操作时遇到任何问题，可以参考这两个备忘单作为快速参考。