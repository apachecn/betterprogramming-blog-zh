# Bash 如何工作

> 原文：<https://betterprogramming.pub/how-bash-works-8424becc12f3>

## 了解 shell 脚本如何节省您的时间并减少错误

![](img/a88aae3c1c7bb7439c6966aa44793498.png)

摄于 [Unsplash](https://unsplash.com/s/photos/funny-cat?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [Sereja Ris](https://unsplash.com/@kimtheris?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 。

最近，我为各种各样的应用程序重构了整个部署设置。这意味着一大堆 Docker 图像都必须灵活但稳定。一些 web 应用程序也需要以用户友好的方式重新启动，向具有不同技能水平的开发人员显示有用的错误消息。

工作量很大，但我确实在 [Bash](https://www.gnu.org/software/bash/) 脚本方面做得更好了。我很适合写这篇文章，因为在这个项目中，我写下了花费我调试时间的每一件奇怪的小事。

Bash 是如此怪异，以至于一篇中型文章甚至不能容纳所有这些内容。对于每一部分，我都链接了一篇文章或教程来进行更详细的介绍。

# 你奶奶的编程语言

[](https://en.wikipedia.org/wiki/Bash_%28Unix_shell%29) [## Bash (Unix shell)

### GNU Bash 或简称 Bash 是 Brian Fox 为 GNU 项目免费编写的 Unix shell 和命令语言…

en.wikipedia.org](https://en.wikipedia.org/wiki/Bash_%28Unix_shell%29) 

Bash 由 Brian Fox(这家伙是一个被低估的传奇人物)创建，于 1989 年发布，作为 1976 年发布的 Bourne Shell 的开源替代品。它的名字是英文的缩写。

如果您习惯于编写几乎任何其他编程语言，Bash(以及一般的 shell 脚本)可能会非常不直观。语法是不可记忆的，变量是奇怪的，范围是一个狂野的旅程，控制流似乎永远不会做你认为它会做的事情。

与 CSS 非常相似，当我了解了一些关于它的关键事情时，我不再害怕编写 Bash 脚本:它如何工作，它真正擅长什么，以及如何让它工作。我也遇到了很多我必须学会的愚蠢的小陷阱。

一旦你掌握了基础知识，Shell 脚本是非常有趣的！没有什么比写一个运行 first try 的恶心的一行程序更让你觉得自己是一个黑客高手了。

注意:我假设您事先了解一些编程和 shell 脚本。如果那不是你，[这里有一个开始](https://catonmat.net/bash-one-liners-explained-part-one)的好资源。我假设您至少知道如何使用终端和以下命令:`ls`、`cd`、`pwd`、`cat`、`grep`，并且已经编写(或试图编写)了一两个脚本。

顺便说一句，既然这涉及到 Linux 和操作系统的东西，我有一个建议给住在这里的人:没关系(甚至鼓励！)如果我说错了请纠正我，只是请你礼貌一点。我确信我会从发表这篇文章中学到一些东西:这是我的目标。

# 版本

[](https://stackoverflow.com/a/52860837/4718615) [## 首选的 Bash shebang 是什么？

### 使用 shebang 行来调用适当的解释器不仅仅是为了 BASH。你可以用它做任何…

stackoverflow.com](https://stackoverflow.com/a/52860837/4718615) 

如今我们大多数人编写的 shell 脚本语言是 Bash Mac 和 Linux 在`/bin/bash`中用于终端模拟器的版本。

Debian (通过扩展，Ubuntu 和 Linux Mint)现在为系统使用一种不同的但是大部分兼容的 shell 脚本语言([破折号](https://packages.debian.org/sid/dash))。您也可能已经安装了 [Zsh](https://www.zsh.org/) 作为您的主 shell，它与您的相似但不同。

由于所有这些微小的变化，最好将`#!/bin/bash` (或者您想要使用的任何特定的 shell 脚本语言)放在文件的顶部，以指定 shell 脚本应该使用它，而不是机器上的任何其他语言。

这将使它更容易预测。例如，堆栈溢出回答通常会假设您使用的是这个版本。

# 基础知识

首先我们将复习一些基础知识。

外壳脚本的核心是相互传递数据的文本流。它使用 Unix 哲学，即做好一件事，将小工具链接成大程序——就像工厂生产线上的小机器。每个工具按顺序输出到下一个。

 [## Unix 哲学的基础

### “Unix 哲学”起源于 Ken Thompson 早期对如何设计一个小型但功能强大的操作系统的思考

homepage.cs.uri.edu](https://homepage.cs.uri.edu/~thenry/resources/unix_art/ch01s06.html) 

## 句法

语法是宽松的，就像在语法不严格的语言中一样:如果你愿意，可以在行尾使用分号，缩进不是什么大问题。

这是个陷阱。语法很重要，而且非常具体。此外，Bash 语法错误是最糟糕的。

正确使用空格和分号**尤为重要**。

这些可能会导致令人困惑的错误，比如当你忘记这些`[]`中的空格时的`"[grep isn’t a valid command"`，或者当你忘记这些`{}`中的分号时的`“Unexpected end of file”`。

当声明一个变量时，在变量、等号和值之间加一个空格将意味着完全不同的东西。单引号和双引号之间有一个非常重要的区别。

语法错误总是听起来像逻辑错误，这使得发现拼写错误更加困难。

## 结构

Shell 脚本有控制流:if 语句，while 循环，for 循环，case 语句等。

不同之处在于条件和范围。然而，因为它更适合单行和一次性脚本，所以不像在其他语言中那样经常使用。

下面是一个使用控制流而没有任何`if`语句的一行程序的例子:

```
tac ~/error.log \
| grep -m1 -E "Error|Running restart" \
| grep -q "Error" \
&& echo "Found error since last restart"
```

(`\`是行继续符，`tac`类似于`cat`，但向后输出文件。)

它很难看，但是很有效，说明了 shell 脚本的优点和缺点。

Bash 有可能非常简短，难以阅读。你可以用几行代码做很多事情，但是当事情发生时，很难找出原因。是福是祸。权力越大，搞砸一切的可能性就越大。

## 什么是溪流？什么是命令？

每个命令都是一个做一件事的程序。例如，Grep 搜索事物并返回行。一个查询进去，文件进去，行出来。

你可能会想:“是的，很明显，所有的编程都是这样工作的，”但它比这要复杂一点，在这里理解起来特别重要。

这些输入和输出以*文本流*的形式在命令间来回传递。这些溪流从三个地方流入流出:

*   `stdin`:标准输入。
*   `stdout`:标准输出。
*   `stderr`:标准误差输出。

[](https://catonmat.net/bash-one-liners-explained-part-three) [## Bash 一行程序解释，第三部分:所有关于重定向

### 这是 Bash 一行程序解释系列文章的第三部分。在这一部分，我将教你所有关于…

catonmat.net](https://catonmat.net/bash-one-liners-explained-part-three) 

这是一个“流”,因为行是在命令/函数执行的不同点输出的，而不是像你想的那样都在最后输出。

您使用类似于`printf`和`echo`的命令向`stdout`发送文本。没有经验的 shell 脚本编写人员可能会认为这些只是用于调试的日志工具，就像 Python 或 JavaScript 一样。并非如此。

流使命令和函数能够串成一条代码流水线。说明这一点的一个好方法是解释函数是如何工作的。

## 功能

我这样定义一个函数:

现在，如果您使用`$ hello`从终端运行它，您将得到:

```
Hello World! 
Something 
some more stuff to print Something
```

`echo`和`printf`都向`stdout`发送文本流。如果您从像您的终端这样的交互式 shell 中运行我们的`hello`功能，`stdout`将打印到您的控制台。

我们可以通过输入重定向来改变这一点，并且*将输出发送到一个文件或者作为另一个命令的输入。*

这有点像常规脚本语言函数中的返回值，只不过你可以拥有任意多的返回值，而且它们不会结束函数。

如果你想结束这个函数，有几个命令可以完成。`exit`和`return`命令采用一个数字代码:`0`表示成功，其他表示失败。`return`将退出该功能，而`exit`将退出外壳本身。

退出代码是无符号整数，这意味着如果您不幸选择 256 作为失败错误代码，您将度过一个有趣的调试下午。如果出于某种原因，你的脚本需要超过 255 种不同的失败方式，那么，你就不走运了。

## 流重定向

这些是基本的。

[](https://developer.ibm.com/technologies/linux/tutorials/l-lpic1-103-4/) [## 学习 Linux，101:流、管道和重定向

### 如果你认为流和管道让一个 Linux 专家听起来像一个水管工，那么你有机会了解它们以及如何…

developer.ibm.com](https://developer.ibm.com/technologies/linux/tutorials/l-lpic1-103-4/) 

*   `|`被称为管道，你用它来发送输出给其他命令。比如我们可以试试`hello | grep 'Hello'`。这将把`hello`的所有输出发送到`grep`，后者将返回包含“Hello”的行。我最喜欢的管道的日常用法是`history | grep ‘command’`,当我忘记了我之前输入的确切命令，但我知道它包含了某个单词。
*   `>`右边有一个文件将输出重定向并打印到一个文件而不是控制台。该文件将被`>`完全覆盖。比如`logging_function > tmp_error.log`。如果你喜欢 Python，可能用过`pip freeze > requirements.txt`。
*   `>>`类似于`>`,但是追加到一个文件，而不是完全替换其内容。例如，`logging_function >> error.log`。
*   `<`是`>`的反义词。它将右边文件的内容发送给左边的命令。试试`grep foo < foo.txt`。

管道平行延伸。例如，以下内容将只运行一秒钟:

```
sleep 1 | sleep 1 | sleep 1 | sleep 1 | sleep 1
```

管道成员不会让队列中的下一个命令等待它们完全完成。他们一边处理一边发送输出。

## If 语句

没有什么比 Bash 的`if`陈述更能说明“微型工具”了，它实际上是风衣中的五个关键词。

```
if *[ <expression>* ]; then
*<commands>*
fi
```

注意如何用`fi`结束`if`语句？

对于 case 语句也是如此；`case … esac`。当我得知这一点时，我真的希望`while`以`elihw`结束，`until`以`litnu`结束，但不幸的是，这些都以`done`结束。

`[`是一个命令，`]`是一个参数，告诉它停止接受其他参数。`Then`、`else`、`elif`、`fi`都是关键词。

以这个错误为例:

```
/bin/sh: 1: [: true: unexpected operator
```

你可能认为这个脚本遇到了一个流氓`[`，抛出了一个语法错误。不是这样的！

实际发生的是*命令* `[`得到了一个意外的参数:`true`。这个错误实际上是因为我使用了`==`而不是`=`，而`=`的计算结果是`true`，因为它使用了一个奇怪版本的`[`命令(这是一个为什么需要那个`#!/bin/bash`的例子)。

## **控制流程**

我更喜欢只在特定情况下使用`if`语句。一般来说，我更喜欢 Bash 的操作符:`&&`和`||`。

你把它们放在一个命令/函数之后，如果它返回 0，`&&`将运行它后面的东西，而`||`不会。

```
$ will_return_0 && echo "I will print"
$ will_return_0 || echo "I will not print"$ will_return_1 || echo "I will print"
$ will_return_1 && echo "I will not print"
```

你也可以把它们链起来。运行这两个命令:

```
$ /bin/true && echo "I will print" || echo "I will not print"
$ /bin/false && echo "won't print" || echo "will print"
```

但是要小心！顺序很重要，所以你必须先做`&&`然后再做`||`，否则就无法工作。这不会像你期望的那样:

```
/bin/false || echo "will print" && echo "won't print"
```

如果你不习惯阅读 shell 脚本，那么阅读它们会有点困难，但是它们也不会做任何奇怪的事情。If 语句更适用于需要组合在一起的命令序列，在这种情况下，将它们作为一个函数是没有意义的。

我也更喜欢这个命令:`test`。这和没有误导语法的`[`是一样的。不太熟悉的语法(我认为)更好，因为它向读者发出信号，他们可能不明白到底发生了什么。

从长远来看，这种选择会导致一些浪费时间的错误假设。这不是常见的最佳实践，只是我的看法。

## 变量

Bash 中的变量是 *wild* 。它们的工作方式就好像您已经将它们的值放入脚本并运行它。

它们不是类型化的(像 int、string、arrays 等),但是 act anywhere 对它们来说很方便:可以是字符串、命令、数字、几个数字等。如果你的“字符串”中有空格，它们甚至可以扩展成多个关键词。

这可能会导致一些疯狂的错误，这就是为什么你应该*永远不要*接受有风险的用户对 shell 脚本的输入(比如来自互联网)。如果你是一名 web 开发人员，并且知道`eval`有多危险:每个 shell 脚本都是一个巨大的`eval`语句。远程代码执行丰富！

例如，尝试在终端中键入以下行:

```
$ MY_VAR="echo stuff"
$ $MY_VAR
```

您应该看到它执行命令并向控制台回显“stuff”。这种行为会使更长的脚本充满错误和不可预测。例如，试试这个:

```
$ HELLO="hello world"
$ test $HELLO = "hello world" && echo yes
```

它抛出一个错误，因为 Bash 是这样读取的:`test hello world = “hello world”`。这就是为什么一个重要的最佳实践是总是将变量放在**双引号**中。像这样:`test “$HELLO” = “hello world”`或者`[ "$HELLO" = “hello world” ]`。

在这里，不把双引号当作字符串分隔符可能会有所帮助。Bash 不像其他语言那样对待字符串。引号有点像括号(但不是 Bash 括号)。那些是子壳)。

Bash 中单引号和双引号的区别很重要。大多数情况下，您会想要使用双引号。有什么区别？双引号会扩大变量，单引号从字面上理解。例如:

```
var=stuff
echo $var
echo "$var"
echo '$var'
```

输出将是:

```
stuff
stuff
$var
```

变量的另一个恼人或有用的地方(取决于你如何看待它)是它们从不抱怨未声明。您可以检查某个变量是否已经这样设置:

```
test *-z* *"*$empty" && echo "variable is empty"
```

还可以在脚本中添加一个设置来公开未设置的变量:

```
set -o nounset
```

默认情况下，变量对整个 shell 都是可用的，但是这是可以改变的。

## 范围

理解范围对于最小化 bug 非常重要。

一个未被充分利用的特性是使用`local`和`readonly`变量。`local`会将一个变量限制为一个函数，如果你试图重新设置它的话`readonly`会抛出一个错误。你甚至可以将这些链接在一起，成为局部`readonly`变量，或者全局`readonly`变量。

只有全局变量应该是大写的。要使一个变量对整个 shell 可用，请使用`export VAR="value"`。这里的大写是一种约定，表示变量是全局的*而不是*像在其他语言中一样是常量/不可变的。

## 命令

我最不喜欢 Bash 的一点是必须记住晦涩难懂的小命令。`Sed`？`Cat`？这些是什么意思？名字当然不会告诉我。伙计，页面很难解析，我当然不会记得所有东西应该按什么顺序排列。

这是因为在过去，每个角色都比现在贵得多。认为代码也应该是人类可读的想法也明显不是主流。

有时候，寻找一个命令到底应该做什么的准确信息就像在一个巨大的图书馆里搜索布满灰尘的档案。图书馆员大多是自鸣得意的人，他们给你旧手册，上面有一句用维多利亚时代的英语写的相关句子。

幸运的是，你偶尔会在 Stack Overflow 上遇到超级巫师，他们不介意分享他们辛苦获得的知识。向超级巫师呼喊。

## 特殊变量

有时候，你会遇到奇怪的无意义的变量，比如`$@`和`$!`。你可以阅读它们的列表:

 [## 特殊参数(Bash 参考手册)

### 3.4.2 特殊参数 shell 对几个参数进行了特殊处理。这些参数只能被引用…

www.gnu.org](https://www.gnu.org/software/bash/manual/html_node/Special-Parameters.html) 

一些对编写脚本和一行程序有用的是:`$-`和`$*`。

这两者都为命令/函数的运行提供了参数。`$-`为您提供标志(小破折号修饰符)，而 `$1–9`为您提供输入。例如:

```
curl -s example.com
```

这里的修饰符是`-s`，输入是`example.com`。

用`$`获取输入关键词时，重要的是这样使用:`${2}`、`${25}`等。Bash 不会理解`$42`这种有两位数的东西。你也可以这样做:

```
iterator=4
echo ${$iterator}
```

一个更好的例子是`$!!`，当它在终端中运行时，会扩展到最后一个命令。当你忘记用 sudo 运行一个命令时，试试`sudo $!!`。

## 副壳和支架

如果你看到这个:

```
while ( something ); do
```

那些括号不是你想的那样。

Bash 中的括号实际上产生了*子外壳*。这意味着它们是同一个脚本的子流程。它们本质上是子外壳，可以获得父外壳的所有上下文(变量、函数等)，但不能修改父外壳的任何内容。

为了保持头脑清醒，子 shell 是(在变量之后)第二重要的关于 shell 脚本的东西。它们会突然出现在你意想不到的地方，使你的程序更难预测。

首先，他们做什么？

让我们看一些例子:

您将得到以下输出:

```
foo
Inside the sub-shell
foo
bar
Back in the main shell now
foo
```

请注意变量是如何在子外壳的上下文中改变的，但在子外壳的外部*没有*。子壳的另一个优点是:

这将产生:

```
We start out at root
/home/username
In the subshell
/tmp
Back in the main shell now
/home/username
```

在子外壳中使用`exit`将只退出该子外壳，而不退出父脚本。

括号不是产生子外壳的唯一方法。如果你像使用`&`或`nohup`一样将一个进程放入后台，这些也会进入子外壳。

用`./`运行的脚本运行在它们自己的 shell 中(不是终端的子 shell ),而用`source`运行的脚本运行起来就像你直接输入命令一样。

你可能期望*函数*在子外壳中运行，但是这些实际上更像是分组命令。

为了让您的函数在每次使用时都在子 shell 中运行，您实际上必须将整个函数包装在 parens 中。要让您的函数返回一个特定的退出代码，而不在子 shell 中运行或退出整个脚本，请使用`return`而不是`exit`。

## 关注点分离

有时你可能需要把你的脚本分成几个文件。`source`就是你怎么做到的。

`parent.sh`:

```
!#/bin/bash
source /path/to/script.sh
```

这基本上就好像你已经将`script.sh`的全部内容输入到了`parent.sh`中——所以，这有点像如果这不是 Bash 的话你会怎么想。但是要小心！如果您在父 shell 中设置了一个变量，内部脚本将可以访问它。

像使用库一样使用这个方法是一个更好的主意:导入可以在父脚本中使用的帮助函数。

当心退出代码和将值传递给主脚本！这不是你想的那样。如果它在子 shell 之外，源文件中的`exit`将不会退出内部脚本:它将退出整个脚本！

## 错误处理

我喜欢将分组的命令打包成一个函数，然后通过执行以下操作来处理失败的情况:

```
my_function || handle_error
```

# 结论

感谢一路读到最后！

还有很多要写的，但这实际上是一篇中型文章的最大长度，所以请继续关注第 2 部分。