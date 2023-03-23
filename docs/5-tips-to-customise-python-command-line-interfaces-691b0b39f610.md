# 定制 Python 命令行界面的 5 个技巧

> 原文：<https://betterprogramming.pub/5-tips-to-customise-python-command-line-interfaces-691b0b39f610>

## 构建更好的命令行工具

![](img/6f56d5966a2fcc7ea7b08dfec9a5e6fe.png)

由[詹姆斯·柯文](https://unsplash.com/@james2k?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Python 的一个非常常见的用法是创建一个工具箱，来帮助您完成重复的任务。

如果您正在阅读这篇文章，我认为您已经用 Python 创建了自动化脚本。

然后，也许在某个时候，你开始重构你的脚本，使它成为一个合适的命令行工具。添加参数、配置、提示和指令。随着时间的推移，我发现总有一组我喜欢添加到我的 CLI 中的通用功能。

我相信对你来说也一定是一样的。因此，每当我创建一个新的命令行时，我决定编译并分享我倾向于添加到命令行中的最佳技巧。

# 1.选择正确的工具来分析你的论点

从自动化脚本到 CLIs 的第一个升级是允许参数。参数为您的程序声明值，并使其可自定义。

有一点是肯定的，我讨厌使用原生的 argparse 包。这个包非常冗长，这意味着它需要大量代码来完成简单的任务。这里有一个来自 [argparse](https://docs.python.org/3/library/argparse.html) 文档的简单例子供参考:

通过 [POSIX 实用程序约定](https://pubs.opengroup.org/onlinepubs/009695399/basedefs/xbd_chap12.html)和 [GNU 扩展](https://www.gnu.org/prep/standards/html_node/Command_002dLine-Interfaces.html)，我们传递参数的方式已经标准化。

因为解析参数是基于约定的，所以您可以找到一个 Python 包来帮助您编写更好、更智能的命令行。

都有自己的长处和短处。但在我看来，最好的还是 [docopt](http://docopt.org/) 。

迫使你专注于文档，并神奇地为你解析所有的参数。让我们使用 Docopt 编写相同的示例:

你注意到了吗？我们真正做的只是为我们的命令行编写文档，其余的由`docopt`处理。这个一行程序实现了这个目的:

```
arguments = docopt(__doc__, version='Utility 20.0')
```

Docopt 将文件的文档字符串( **doc** )和您调用的命令作为输入，并返回一个包含所有标志和位置参数的字典。

```
{'--help': False,
 '--sum': False,
 'N': ['test']}
```

[这是 GitHub 上第一个例子的代码](https://github.com/xNok/pystatemachine/blob/master/example_build_better_cli/2_docopt.py)，还有[说明](https://github.com/xNok/pystatemachine/tree/master/example_build_better_cli)。

# 2.实施一次演习

通常，在为自动化创建 CLI 时，您希望能够知道您的 CLI 将执行什么操作而不会产生任何影响。

在这种情况下，您很想编写一个简单的模拟机制，让您可以运行命令行，但跳过所有有影响的更改，比如对 API 的 HTTP 调用。

幸运的是 [dryable](https://github.com/haarcuba/dryable) 是一个简单的 Python 包，由于巧妙使用了 [decorator](https://realpython.com/primer-on-python-decorators/) s，使得实现变得简单。

如您所见，我在这个例子中也重用了 docopt。添加一个新的参数`--dry-run`很容易，这个参数将反映在 dict `arguments`中，dict`arguments`保存所有被 docopt 解析过的参数。

我们使用`dryable.set(arguments['--dry-run'])`来激活或不激活标志`--dry-run`的存在。如果 dryable 被激活，那么所有用`@dryable.Dryable()`装饰的功能都将被跳过。

[这是 GitHub 上第二个例子的代码](https://github.com/xNok/pystatemachine/blob/master/example_build_better_cli/3_dryable.py)，还有[的说明](https://github.com/xNok/pystatemachine/tree/master/example_build_better_cli#2-implement-a-dry-run)。

# 3.创建. rc 文件

一旦命令行达到一定的复杂性，每次都定义所有参数会变得非常乏味。

你可能已经熟悉了`.rc`文件，比如`.npmrc`、`.bashrc`。`.vimrc`。这些文件是特定命令行界面的配置文件。他们的名字被编入法典；它以`.`开头，然后是 CLI 的名称，后跟`rc`。

如果我们参考维基百科得到一个合适的定义，我们会得到:

> *在类 Unix 系统的上下文中，术语 rc 代表短语“运行命令”。它用于包含命令启动信息的任何文件。*

在您的 CLI 中支持一个`rc`文件使得通过将该文件提交到您的存储库来共享项目旁边的配置变得容易。它还防止用户重复他们经常使用的参数。这简化了命令行的使用。

通过使用 docopt，创建一个`.rc`文件很容易。在我们的例子中，只需将生成的名为`arguments`的字典保存到一个文件中。给你一个`.rc`文件。现在您需要做的就是检查该文件是否存在。每当调用该命令时，就会将您的`.rc`文件的内容与`arguments`文件合并。这里有一个例子:

在我们之前的例子中，您必须始终包含`--agg=sum`才能让命令工作。

```
python .\2_docopt.py 2 3 --agg=sum
```

如果你知道自己只想用`sum`并且厌倦了重复自己怎么办？用我们的名为`.foorc`的`.rc`文件很容易知道。将以下内容添加到`.foorc`:

```
{
  "--sum": true
}
```

现在你自由了，可以不用`--agg`运行命令行了

```
python .\4_rc_file.py 2 3
```

这里是代码和[。本例中的 foorc](https://github.com/xNok/pystatemachine/blob/master/example_build_better_cli/.foorc) 和[有说明](https://github.com/xNok/pystatemachine/tree/master/example_build_better_cli#3-creating-a-rc-file)。

# 4.安装命令行

一旦您的命令行脚本开始看起来像其他开发人员会使用的东西。你可能想让它可以安装。

这是创建想要共享的命令行的基础。CLI 将被添加到路径中，因此您可以直接调用它，而无需调用 python 脚本。

使用 pip 时，安装命令行发生在安装阶段。以下`setup.py`将安装 CLI:

最重要的部分是`entry_point`属性。

```
'console_scripts': [
    'foobar=5_setup:main'
],
```

这告诉 python 注册一个名为`foobar`的控制台脚本，该脚本将调用`5_setup.py`的方法`main`(`5_setup.py`与`4_rc_file.py`相同，但一切都包装在方法`main()`中)。使用 pip 安装命令行:

```
pip install .
```

那么您的新命令行应该如预期的那样工作:

```
foobar 2 3 --agg=max
```

一个额外的提示是，如果您在涉及 pip 时使用标志`-e`，您将在可编辑模式下安装 CLI。

换句话说，代码中的更改将在调用您的命令时直接反映出来，这在您的 CLI 仍处于开发阶段时非常方便。

```
pip install -e .
```

只需按照这些说明[进行尝试](https://github.com/xNok/pystatemachine/tree/master/example_build_better_cli#4-installing-the-command-line)

# 5.使命令行可扩展

一旦你的命令行成熟了，很多用户都在使用它并添加新的特性，你可能想要一个插件系统。以便您的 CLI 仅包含核心功能，但其他用户可以扩展它。

我从这篇文章中学会了这个窍门。通常，在开发开源工具时，您希望为其他人留下一些空间来扩展您的 CLI 功能。

另一方面，并不是每个特性都对社区有用。因此，您可能希望将该特定功能保留在核心之外，而使用插件机制使其成为可选功能。

总之，这种方法是为您的 CLI 创建插件系统的一种简单方式。

在我们当前的实现中，我们可以用标志`--agg`定义我们想要使用的聚合函数。我们还没有实施任何基于该标志的具体行动。演示前面的例子是没有用的。让我们解决这个问题。

实现一个方法，该方法采用一个数字列表、一个聚合器的名称和一个聚合器的映射。

该功能可按如下方式使用:

使用这种方法，您会受到您在字典中列出的聚合器的限制。如果有人想为你的系统提供一个插件，用非常花哨的操作符来应用于一个数字序列，该怎么办？

使用 python `entry_points`并不太难。让我们重构代码，使其具有可扩展性。

`get_aggregators`函数将读取所有名为`aggregators`的`entry_points`，并将它们添加到我们的可能动作字典中。

现在只需创建一个定义了名为`aggregators`的`entry_points`的新包并安装它。

这里有一个`setup.py`来安装你的新插件。注意第`fancy = fancy:main`行，这意味着聚合器调用 fancy 引用了 yo 插件包中的文件`fancy.py`和方法`main`。

这里有一个非常奇特的 now 运算符

现在安装你的新软件包，我的叫做`extandable_fancy_aggregator`

```
python -m pip install -e ./extandable_fancy_aggregator
```

你自己试试吧

```
python .\6_extandable.py 1 2 --agg=fancys
```

对于这个，你需要[这个包](https://github.com/xNok/pystatemachine/tree/master/example_build_better_cli/extandable_fancy_aggregator) `[extandable_fancy_aggregator](https://github.com/xNok/pystatemachine/tree/master/example_build_better_cli/extandable_fancy_aggregator)`和例子的[代码。你可以在这里找到说明。](https://github.com/xNok/pystatemachine/blob/master/example_build_better_cli/6_extandable.py)

# 结论

随着你的 Python 脚本成长为一个完整的命令行，我采用了上面的五个元素。我喜欢将它们视为改善我的开发工作生活的一种方式。

1.  在解析参数时让我的生活变得简单
2.  实施一次演习以控制局面
3.  实现`.rc`,因为命令变大了，我讨厌写长命令行。
4.  安装脚本作为一个完整的命令行，我可以使用了
5.  使命令行可扩展以促进协作，并使内核尽可能小

感谢阅读。