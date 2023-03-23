# 你应该知道的 6 个标准 Python 模块

> 原文：<https://betterprogramming.pub/6-standard-python-modules-you-should-know-f476e3c4ef7f>

## 它们是内置的，易于使用，并且保持轻便高效

![](img/6f1e853a5d3748b7a21ff0f44dc530b3.png)

照片由[阿诺德·弗朗西斯卡](https://unsplash.com/ja/@clark_fransa?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/programmer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

我们都喜欢 Python 的可扩展性。从 pip 使用第三方模块轻而易举。它们易于安装，并且有一个模块可以满足您的任何需求——无论它有多特殊。

第三方模块的问题是它们可能成为失控的依赖列车。您引入了一个第三方模块，它引入了更多的模块，在您意识到这一点之前，您已经使您的系统变得臃肿不堪。大多数现代系统都有大量的资源和磁盘空间，所以这很少是个问题。但是如果你在嵌入式平台上工作呢？如果你需要尽可能的高效呢？

即使您没有在严格的限制下工作，仅使用您需要的模块也将保持事情顺利运行。

这就是为什么你必须熟悉 [Python 标准库](https://docs.python.org/3/library/)的原因。这个库包含 Python 默认附带的所有内置模块。这是一个由有用的基本软件包组成的广阔的生态系统。虽然它们可能不像某些第三方工具那样光彩夺目，但是如果你知道去哪里找，它们也可以一样能干。

在本文中，我们将探索一些真正突出的内置模块。这些模块有助于快速解决常见问题，并提供足够的灵活性而不会臃肿。

## 1. [textwrap](https://docs.python.org/3/library/textwrap.html)

如果您正在处理文本，并且想要对诸如行长度或空白之类的东西实施规则，这个库是必不可少的。`textwrap`模块有一套操作文本数据总长度的工具。

有了这个模块，你可以包装和缩短你的方式更漂亮的字符串。让我们看看如何缩短一个较长的句子:

```
import textwrap

sentence = "hello, this is a really long sentence of words"

wrapped = textwrap.shorten(sentence, width=25, placeholder=" ...")

print(wrapped)
```

结果将缩短我们的句子，并插入一个占位符，表明我们截断了一些文本:

```
>>> hello, this is a ...
```

答对了。更短的文本和一个占位符。这个模块可以用于显示较长描述的预览，或者只是确保输入的数据符合您的视觉准则。

## 2. [pprint](https://docs.python.org/3/library/pprint.html)

内置的漂亮打印机。没有它不要离开家。如果没有像这样的解析器，查看原始数据输出是一件非常痛苦的事情。幸运的是，Python 的标准库中包含了一个。

`pprint`模块让您以人类可读的格式显示复杂的数据结构。如果您正在使用 Python 解释器进行尝试，这可能会特别有帮助。

让我们用下面的例子打印一个字典:

```
import pprint

data = {'one':1,'two':2,'three':3,'four':4}

pprint.pprint(data, indent=4, width=10, sort_dicts=False)
```

这里我们有一个简单的数据字典。我们打印字典，指定我们希望输出如何缩进和总宽度。我们不对 dict 排序的原因是，当它被赋值时，它已经被排序了。它应该是这样的:

```
>>> {    'one': 1,
         'two': 2,
         'three': 3,
         'four': 4}
```

我们可以看到，这显然比单行定义更具可读性。随着数据变大，这变得越来越有用。有了这个库，能够阅读深度嵌套值的大规模字典，或者搜索特定值的列表变得非常容易。

## 3.[临时文件](https://docs.python.org/3/library/tempfile.html)

有多少次您需要快速地将数据存储在临时文件中？也许您正在缓冲传入的数据或编写配置文件。构建自己的临时文件解决方案就像因为无聊而重新发明轮子一样。不要这样做。请改用`tempfile`模块。

这个内置模块将处理创建临时文件和读写临时文件的所有繁重工作。您只需要提供一些基本的细节，然后您就有一个健壮的文件界面可以使用:

```
import tempfile

tf = tempfile.TemporaryFile()

tf.write(b'i am some temporary data')

tf.seek(0)

tf.read()
```

使用上面的例子，您可以自动生成一个临时文件，写入其中，然后用不到几行代码读回。如果您不想的话，您不必提供目录路径、处理权限或弄乱任何低级文件系统属性。

如果你想指定一个命名文件，你可以使用`NamedTemporaryFile`对象。这将为一个文件提供一个接口，您可以在操作系统级别与它进行实际的交互。

## 4.[平台](https://docs.python.org/3/library/platform.html)

如果您正在构建跨平台或多架构的 Python 应用程序，那么这个模块是至关重要的。`platform`模块提供关于底层操作系统和硬件的基本信息。您还可以获得大量关于正在运行的 Python 解释器的信息。

这个模块对于检查硬件架构之类的东西很方便。如果我们想确定我们所在的底层系统的结构，我们可以运行:

```
import platform

print(platform.machine())
```

这应该会产生系统的架构。例如，在 M1 的 MacBook Pro 上应该是:`arm64`。

我们还可以通过运行以下命令来找出我们当前运行的 Python 解释器版本:

```
import platform

print(platform.python_version())
```

这将产生一个完整版本号的字符串，如`3.9.13`。

`platform`模块也包含操作系统特有的方法。您可以确定有关 Linux、macOS 和 Windows 机器的特定于操作系统的信息。

## 5.[网络浏览器](https://docs.python.org/3/library/webbrowser.html)

这是一个超级酷的模块，直到最近我才知道它的存在。这个模块将让您在底层机器上操作 web 浏览器。你可以打开网址，获取系统浏览器的基本信息。

对于像集成测试这样的事情来说，`webbrowser`模块可能不是最实用的，但是如果你只是想向最终用户显示网页，这是一个快速简单的方法。

如果我们想在底层机器上打开[google.com](https://www.google.com/)，我们需要做的就是执行:

```
import webbrowser

webbrowser.open('https://google.com')
```

这将打开一个新的浏览器或新的标签(如果浏览器已经打开)并导航到[google.com](https://www.google.com/)。相当整洁！一些很酷的用途可能是向新用户显示关于你的应用程序的文档，或者打开一个生成二维码的网页。一旦你控制了浏览器，就有无数的可能性。

## 6.[IP 地址](https://docs.python.org/3/library/ipaddress.html)

`ipaddress`模块已经成为我的最爱有一段时间了。如果您正在使用 Python 进行任何类型的联网，这将是非常宝贵的。手动操作 IP 地址字符串令人麻木，而且效率低下。这个库给你一个光滑，简单的界面来生成和分析 IP 地址和网络空间。

这个模块最好的部分是它将一个简单的 IP 地址字符串转换成一个元数据丰富的对象。假设我们有一个基本的 IP 地址，比如:`10.1.2.3`。使用这个模块，我们可以从这个地址获得大量信息:

```
import ipaddress

ip_addr = ipaddress.ip_address('10.1.2.3')

print(ip_addr.is_private)
print(ip_addr.version)
print(ip_addr.is_loopback)
```

我们可以确定一些基本的事情，比如地址是公开的还是私有的。我们可以算出是什么 IP 版本(v4 还是 v6)。我们还可以测试许多其他条件，以确定我们正在处理的是哪种地址。这对于接受用户输入或批量收集的任何东西都非常有用，这样您就可以过滤掉不正确的地址类型。

一个非常方便的功能是分析子网的能力。假设我们有一个用 CIDR 符号表示的网络:`10.1.2.0/24`。我们可以像这样获得网络的基本信息:

```
import ipaddress

ip_addr = ipaddress.ip_network('10.1.2.0/24')

print(ip_addr.netmask)
print(ip_addr.num_addresses)
print(ip_addr.broadcast_address)
```

这些方法将为我们提供子网掩码(十进制)、地址总数和网络的广播地址。结合主机地址，您可以确定大量关于 IP 地址空间的信息。

感谢阅读！你喜欢这篇文章吗？看看我下面的几个帖子:

*   [*6 更独特的 Linux 实用程序你忘了*](/6-more-unique-linux-utilities-you-forgot-about-1215ac0c58da)
*   [*5 个 Python 模块，让构建 API 更简单*](/5-python-modules-that-make-building-apis-easier-a8d39911f6a8)
*   [*使用亚马逊 EventBridge*](/scheduling-aws-lambdas-using-amazon-eventbridge-7440c6270c15) 调度 AWS Lambdas