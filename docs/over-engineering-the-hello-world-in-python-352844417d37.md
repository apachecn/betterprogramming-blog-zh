# 在 Python 中过度设计“Hello World”

> 原文：<https://betterprogramming.pub/over-engineering-the-hello-world-in-python-352844417d37>

## 不使用 print()打印到控制台的 7 种方式

![](img/355607b8cc3654b89cc296436897edf1.png)

使用 [DALL-E 2](https://openai.com/dall-e-2/) 生成的图像

Python 编程语言以对初学者非常友好和高度灵活而闻名。然而，正因为如此，它允许多种方法来实现相同的最终目标——有些方法比其他方法更有效。

在本文中，我将向您展示用 Python 将字符串打印到控制台的七种方法，从标准的 Python 方法开始，逐渐过渡到更奇特的打印技术。

# 传统的内置打印功能

这是将字符串打印到控制台的最 Pythonic 化的方式，没有比老式的`print()`语句更简单的了。

```
Hello World
```

但是，你会变得偏执，认为自己只是众多普通程序员中的一员，并开始寻找更具创造性的技术来证明自己并不平庸。

# 使用 Sys 模块写入 Stdout

如果你曾经用 Java 编程过，你会同意仅仅为了打印一个值到控制台而输入`System.out.println()`有点夸张，这也是你迁移到 Python 的原因。

这就是内置`TextIO`对象带回 Java 噩梦的地方:

```
Hello World
```

此外，您需要手动添加换行符。今年是哪一年，1972 年？而且，说到 70 年代，UNIX 也是在这个时期发布的。那么，为什么不从 Python 调用 UNIX 命令来打印字符串呢？

# 调用 Echo 命令

在现代操作系统中，您可以使用`echo`命令将字符串打印到控制台。您可以使用`os`模块从 Python 调用系统 shell 命令。

```
Hello World
```

这根本不是 Pythonic 的风格，但它仍然有效。

# 写入一个文件，并对其进行编目

既然我们触及了系统 shell 命令主题，为什么不使用`cat`将文件内容输出到控制台呢？顺便说一句，“猫”这个名字并不是来自那些可爱的宠物，而是来自“连接”这个词。

```
Hello World
```

这种技术有点像你在[捕获旗帜安全挑战](https://en.wikipedia.org/wiki/Capture_the_flag_(cybersecurity))中使用的黑客技术。顺便说一下，这些年来，黑客变得越来越流行。那么，为什么不黑一个内置的 Python 特性来实现我们的目标呢？

# 破解 Python 异常

黑客是一种通过非标准手段实现目标的艺术，例如，通过制作东西(软件、对象等)。)以不希望的方式行事。记住这一点，让我们破解异常特性，让它将内容打印到控制台。

黑客攻击的第一步是了解目标，在本例中是`raise`关键字。`raise`接受一个`Exception`对象并抛出一个错误，打印堆栈跟踪和最终的错误消息。Python 允许我们通过输入任意字符串来处理错误消息。这就是我们想要打印到控制台的内容。

现在，我们可以像这样抛出一个异常:

然而，输出结果一点也不好，如下所示:

```
Traceback (most recent call last):
  File "/home/nic/Desktop/prints.py", line 42, in <module>
    raise(e)
Exception: Hello World
```

我们绝对不希望每次打印消息时都有堆栈跟踪。这就是`sys`模块的用处:我们可以将 Python 的回溯限制设置为`0`来完全抑制它；

```
Exception: Hello World
```

这更好，但仍然有“例外:”伤害眼睛的部分。幸运的是，我们可以利用 shell [回车](https://en.wikipedia.org/wiki/Carriage_return)特性，该特性允许我们将文本光标位置重置到行首。在 Python 中，回车符表示为`"\r"`或者在 ASCII 中表示为数字`13`。

记住这一点，我们可以重写我们不想要的“异常:”部分，方法是将光标重置到行首，然后在它上面写一条消息。

这段代码产生了预期的结果:

```
Hello World
```

但是，如果消息短于字符串“Exception:”，例如“Hello”，则它们会像这样重叠:

```
Hellotion:
```

为了解决这个问题，让我们根据我们的消息长度和“异常”之间的差异，在行尾添加一些填充:

代码运行完美。

```
Hello
```

# 作为文件写入标准输出

到目前为止，我们已经看到一些不受欢迎的方法效果很好。现在让我们来探索一种技术，一旦写入 stdout，它就会失效，并且不允许从中打印任何内容。

```
Hello World
```

这将使用内置的`open()`函数打开 stdout，然后关闭它，这样您就不能再打印了。如果您之后试图打印任何内容，将会显示以下错误消息:

```
Traceback (most recent call last):
  File "/home/nic/Desktop/prints.py", line 38, in <module>
    print("New message")
OSError: [Errno 9] Bad file descriptor
Exception ignored in: <_io.TextIOWrapper name='<stdout>' mode='w' encoding='utf-8'>
OSError: [Errno 9] Bad file descriptor
```

# 编写自己的 Print()函数

Python 中打印的下一个层次是不使用 Python，而是用 c 编写一个定制的`print()`函数。

对于这项技术，我们将编写一个 C 扩展来代表我们处理控制台的打印。为了保持文章简洁，我不打算解释为 Python 创建 C 扩展的完整过程。如果你有兴趣了解关于这个话题的更多信息，请查看本指南。

首先，让我们创建 C 库。如果你不懂 C，可以直接看评论。

然后创建`setup.py`脚本来构建扩展:

最后，构建并安装 C 扩展:

```
python3 setup.py build
python3 setup.py install --user
```

现在，您可以轻松地在 Python 脚本中导入新的 C 模块，因为它是一个普通的库，并调用自定义的`print()`函数:

```
Hello World
```

恭喜你，你刚刚在 C 中创建了你自己的完全可用的`print()`函数！而且都没有任何分段错误。毕竟 c 也没那么难。

# 结论

在 Python 中，有无数种方法可以将消息打印到控制台。虽然有些是有意义的，但其他只是过度设计的外来技术，旨在作为概念验证或加深语言知识的编码练习。

我希望你喜欢这篇文章。如果你知道任何其他有趣的用 Python 将字符串打印到控制台的技术，请在评论中分享。

感谢阅读！

这是一个系列的第三篇文章，在这个系列中，我探索了用 Python 实现简单任务的各种有趣和过度设计的方法。如果你错过了最后一个故事，你可以在下面找到它:

[](/10-ways-of-iterating-over-a-list-in-python-f068da25875f) [## Python 中遍历列表的 10 种方法

### 可以不使用 for 关键字循环吗？

better 编程. pub](/10-ways-of-iterating-over-a-list-in-python-f068da25875f)