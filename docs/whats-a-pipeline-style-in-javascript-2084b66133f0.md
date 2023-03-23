# JavaScript 中的管道风格是什么？

> 原文：<https://betterprogramming.pub/whats-a-pipeline-style-in-javascript-2084b66133f0>

## 采用这种编程风格编写更好的函数

![](img/264cc7c95c0267e0406ed5ce18b11e4a.png)

米歇尔·玛特隆在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在我的[上一篇关于 JavaScript 编程风格的文章](/whats-the-cookbook-style-in-javascript-be98658d5be1)中，我讨论并演示了菜谱风格。在本文中，我将讨论管道风格，并演示如何使用它来编写一个程序，从 Jonathan Swift 的文本*一个适度的建议*中计算词频。

这篇文章和我以前的文章都是基于 Cristina Videira Lopes 的《编程风格 中的 [*练习》一书。*](https://www.amazon.com/Exercises-Programming-Style-Cristina-Videira/dp/0367350203)

# 定义的管道样式

管道风格的最佳类比是工厂管道，例如汽车工业中使用的管道。汽车是沿着一条“传送带”一件一件地组装起来的，传送带上有完成单独任务的工位，比如安装发动机、装上车门、安装发动机罩和行李箱等。

每个任务也可以由子任务组成。例如，汽车内部必须分别安装座椅、仪表板和其他部件，尽管每个任务都是整个“装配汽车内部”任务的子任务。

流水线风格也借鉴了函数的数学理论。在数学中，函数是一个接受输入并返回唯一输出的过程。数学函数也可以由其他函数组成，使得函数 *f* 可以包含函数 *g* ，只要这两个函数在同一个域上运算。

在编程中，使用返回值函数来开发管道样式。问题中的每个任务都被分配了一个函数，前一个函数的输出成为后一个函数的输入。所有数据都表示为函数的输入或函数的输出，但作为管道中第一个函数的输入的原始数据除外。

# 管道样式示例:Unix 管道

软件管道的一个很好的例子是 Unix [pipe](https://en.wikipedia.org/wiki/Pipeline_(Unix)) 命令。您可以使用管道将一系列程序链接在一起，这些程序从上一个程序获取输出，以某种方式转换或操作数据，并将结果发送给另一个程序。

软件管道的概念是在 Unix 的早期版本中发展起来的，但是它已经存在了很长时间。1964 年，贝尔实验室的一名成员道格·麦克洛伊写了一份备忘录，概述了他对管道的概念。他在 Unix 诞生之初担任主管 Unix 开发团队的部门主管。在备忘录中，他提到应该有一些连接软件程序的方法，就像花园里的软管连接在一起，形成一条更长的软管。由此诞生了软件管道的概念。

用于管道的 Unix 命令是 bar ( `|`)。起初，使用了`>`字符，但是后来决定这个字符更适合用于输出重定向，所以选择了`|`字符。

下面是一个在 Unix 中使用管道的例子。假设您想要统计所有包含单词`system`的目录条目。单独的命令是`ls`列出目录条目，`grep “system”`匹配包含`system`的行，`wc`计算行数。

下面是实现这一点的管道命令:

```
ls | grep “system” | wc -l
```

允许管道在 Unix 中工作的一个关键概念是所有文件都是文本文件。开发一组希望输入是文本而不是其他专有格式(如 Word 文档格式或 Excel 电子表格格式)的程序要简单得多。当然，您可以将 Word 文档和电子表格转换成其他格式，但是您失去了许多要求文件为文本文件的简单性。

在 Windows DOS shell 中，您可以通过管道传输 DOS 命令，但只能传输 DOS 命令，而不能传输特定程序的输出。例如，我可以列出输出到`more`命令的文件夹和管道的目录，以便限制显示的文件数量。这里有一个例子:

```
C:\Windows\System32>dir | more
Volume in drive C has no label.
|Volume Serial Number is 1290-1953Directory of C:\Windows\System32
02/27/2021  01:50 PM    <DIR>          .
02/27/2021  01:50 PM    <DIR>          ..
12/07/2019  03:49 AM    <DIR>          0409
01/14/2021  01:38 PM    <DIR>          1028
01/14/2021  01:38 PM    <DIR>          1031
01/14/2021  01:38 PM    <DIR>          1033
01/14/2021  01:38 PM    <DIR>          1036
01/14/2021  01:38 PM    <DIR>          1040
01/14/2021  01:38 PM    <DIR>          1041
01/14/2021  01:38 PM    <DIR>          1042
01/14/2021  01:38 PM    <DIR>          1045
01/14/2021  01:38 PM    <DIR>          1046
01/14/2021  01:38 PM    <DIR>          1049
01/14/2021  01:38 PM    <DIR>          1055
01/14/2021  01:38 PM    <DIR>          2052
-- More  --
```

这里有另一个例子，我用这篇文章来统计词频——乔纳森·斯威夫特的《一个小小的建议》。这个示例将文章中的文本通过管道传递给`DOHere’s the`命令和`output:S`命令`find`，这两个命令搜索单词`children`，然后使用命令`sort`根据文本行中的第一个单词按字母顺序对包含`children`的行进行排序。

下面是命令和输出:

```
C:\js>type text.txt | find "children" | sort
children in the arms, or on the backs, or at the heels of their
children sound and useful members of the commonwealth, would deserve sodyet, there are more children born in Roman Catholick countries 
for those women who miscarry, or whose children die by accident or
four, or six children, all in rags, and importuning every passenger 
giving some pleasure to the rich. I have no children, by which I can
hundred and twenty thousand children, already computed, twenty 
murdering their bastard children, alas! too frequent among us,
not be wanting; although I rather recommend buying the children 
reason is, that these children are seldom the fruits of marriage, a
sterling per annum by the sale of their children, will be rid of the
the children of professed beggars: it is of a much greater extent,
their children, when they were sure of a settlement for life to the
Thirdly, Whereas the maintainance of a hundred thousand children, 
thirty thousand couple, who are able to maintain their own children,
thousand children of poor parents annually born. The question 
to have the best title to the children.
with their wives and children, who are beggars in effect; I desire
```

我希望您现在对软件管道是如何工作的有了很好的了解。

# 一个流水线式的词频统计程序

让我们开门见山吧。以下是词频程序的管道版本代码:

以下是该程序的输出:

```
children: 18
kingdom: 15
thousand: 15
country: 11
own: 10
child: 10
hundred: 8
parents: 7
little: 7
shillings: 7
```

如您所见，除了第一个函数`read_words`之外，每个函数都将前一个函数的输出作为其输入，第一个函数接收包含文章的文本文件。

正如 Lopes 在她的书中指出的那样，用这种流水线、函数式的风格编写程序，并不等同于仅仅编写一个包含返回值函数的程序。如果我选择以一种更有状态的方式来使用这些函数，我会编写这样的主程序:

函数式流水线风格在主程序中没有任何变量(状态),而是将数据结构传递给函数。函数式的流水线风格看起来有点像纯粹的函数式语言，比如 Lisp 或 Haskell，但只是一点点。如果你是一个函数式程序员，请不要对这种说法生气。

这就结束了关于管道风格的这篇文章。在我的下一篇文章中，我计划从面向对象编程(OOP)的角度来计算词频，但是使用一些您可能不熟悉的 OOP 风格。

感谢阅读。请随意留下任何意见或建议。