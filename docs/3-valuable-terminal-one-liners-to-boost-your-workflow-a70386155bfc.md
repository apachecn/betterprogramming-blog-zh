# 3 个有价值的终端一行程序来加速您的工作流程

> 原文：<https://betterprogramming.pub/3-valuable-terminal-one-liners-to-boost-your-workflow-a70386155bfc>

## 使用这些简单的 shell 命令完成更多工作

![](img/eeb96d31ec0a84026887d2485653f5d3.png)

[日本摄影](https://unsplash.com/@nihongraphy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

作为一名成熟的程序员，开发命令和脚本是你日常工作流程中不可或缺的一部分。

在本文中，我将分享三个单行命令，它们是我工作流程中很有价值的一部分。这些命令在这里是因为它们有用，而不是因为它们复杂！

这三个命令是:

1.  使用`grep`在文件中查找文本，
2.  查看 JSON 和 CSV 文件。
3.  更新 Python 需求文件。

*注意:本文遵循用* `*$*` *启动 shell 命令的惯例。对于由 shell 扩展的命令，我将在下面显示不带* `*$*` *的扩展命令。*

我交替使用外壳和终端这两个术语。这些命令都与 zsh 兼容。

# 任务 1 —使用 grep 在文件中查找文本

在终端中工作的一个常见任务是查找包含特定文本字符串的文件。

这是用来回答以下问题的:

*   我在哪个文件中定义了`Model`类？
*   我在哪里写了关于反向传播的笔记？
*   哪个文件里有我奶奶的秘方？

有许多工具可以完成这项任务。`awk`、`sed`、`find`、`grep`都能做到这一点。有这么多不同的方法来完成相同的任务是令人不知所措的——关键是找到适合你的工具。

对于我们来说，这个工具就是`grep`。它用于基于正则表达式(regex)匹配文本。然而，您不需要了解 regex 就能从`grep`中获得价值！

当试图学习一个像`grep`这样强大的工具时，一个挑战是它可以做大量的事情。使用强大的工具通常遵循帕累托分布——少数用例提供了大部分价值。

对于`grep`，这些强大的用例之一是在文件中搜索文本，如果匹配就返回文件名。为此，我们向`grep`传递三个参数:

*   `-i`忽略大小写。
*   `-r`递归搜索(深入文件系统)。
*   `-l`返回文件名。

例如，要在当前目录和更深的目录中搜索包含文本`class Model`的文件(因此是`.`):

```
$ grep -irl 'class Model' .
```

我发现`grep`的这个配置非常有用，所以我将它映射到一个别名`g`(在我的`.zshrc`中定义):

```
alias g='grep -irl '
```

这允许仅使用单个字符进行搜索:

```
$ g 'class Model' .
```

如果您想缩小要搜索的文件夹范围，您可以指定它，而不是使用`.`。例如，要仅在名为`src`的文件夹中搜索，我们可以使用:

```
$ g 'class Model' src
```

# 任务 2 —查看 JSON 和 CSV 文件

将数据存储在文本中的好处之一是，我们可以使用与编写代码相同的工具来处理它们。然而，这些工具通常不会以最佳方式可视化数据。

让我们以一个简单的 JSON 文件为例，`data.json`:

```
# data.json
{"outer":{"inner":"value"}}
```

使用`cat`将打印出文件，而不会试图使结构更具可读性:

```
$ cat data.json
{"outer":{"inner":"value"}}
```

这里更好的方法是将`cat`的输出通过管道传输到`jq`，这是一个为处理 JSON 而设计的程序。这将以更有用的方式显示文件的结构:

```
$ json data.json
{
  "outer": {
    "inner": "value"
  }
}
```

我经常使用它，以至于在我的`.zshrc`中定义了一个简单的函数:

```
# ~/.zshrc
json() {
  cat $1 | jq | less
}
```

这允许随时从 shell 中快速、轻松地查看 JSON 文件:

```
$ json data.json
{
  "outer": {
    "inner": "value"
  }
}
```

文本数据的另一种常见形式是 CSV。假设我们在`data.csv`中以 CSV 格式存储了一些数据:

```
# data.csv
col1,col2,col3
10,20,30
30,40,50
```

使用`cat`查看该文件会使结构难以阅读:

```
$ cat data.csv
col1,col2,col3
10,20,30
30,40,50
```

我们可以通过程序`column`使用以下标志来改进该文件的显示方式:

*   `-t`创建一个表格。
*   `-s,`表示数据用逗号分隔。

因为 CSV 通常有许多行，所以将`column`的输出传递到`less`中会很有用，这样就可以对文件进行分页。

我们也可以使用带有`less`的`-S`标志在屏幕的宽度上切断列:

```
$ cat data.csv | column -t -s, | less -S
col1  col2  col3
10    20    30
30    40    50
```

类似于`json`命令，我在我的`.zshrc`中保存了一个名为`csv`的命令:

```
# ~/.zshrc
csv() {
  cat $1 | column -t -s, | less -S
}
```

这样可以从外壳上轻松快速地查看 CSV:

```
$ csv data.csv
col1  col2  col3
10    20    30
30    40    50
```

# 任务 3 —更新需求文件

许多编程语言使用文本文件来跟踪依赖性，例如 JavaScript 中的`package.json`或 Python 中的`requirements.txt`。

Python 的一个常见工作流是在开发项目时安装包，而不指定包的版本。稍后，当我们想要与其他人共享项目时，我们想要在我们的`requirements.txt`中指定我们最终使用的版本。

缓慢的方法是:

*   运行`pip freeze`(将安装包打印到 STDOUT)。
*   滚动以查找包。
*   复制这个包并粘贴到我们的`requirements.txt`文件中。

相反，我们可以在一行代码中完成所有这些工作！

首先，我们用管道将`pip freeze`连接到`grep`，使用`grep`来匹配具有我们的包名(在本例中是`streamlit`)的行:

```
$ pip freeze | grep streamlit
streamlit==0.79.0
```

然后，我们可以将该命令的输出附加到我们的`requirements.txt`文件中:

```
$ pip freeze | grep streamlit >> requirements.txt
```

我们的`requirements.txt`文件现在将包含`streamlit`，并指定了正确的版本:

```
$ tail -n 1 requirements.txt
streamlit==0.79.0
```

# 结论

感谢阅读！

如果你喜欢读这篇文章，一定要看看我关于三个不常见的 Bash 技巧的其他更好的编程文章。

*原载于*[*https://www.datasciencesouth.com*](https://www.datasciencesouth.com/blog/terminal-one-liners)*。*