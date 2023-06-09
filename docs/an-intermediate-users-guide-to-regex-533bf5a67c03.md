# Python 中正则表达式的中级指南

> 原文：<https://betterprogramming.pub/an-intermediate-users-guide-to-regex-533bf5a67c03>

## 理解原始字符串、方法调用模式、分组及其特征

![](img/584f418b369b7ec36d264123028406e0.png)

照片由 [Gautam Arora](https://unsplash.com/@gautamarora1991?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

本文的目标读者是:

*   理解编程的基本概念——对象、变量、函数
*   理解 Python 语法—字符串、列表、循环
*   熟悉正则表达式和简单正则元字符的用法，如`*`、`?`和`\d`
*   并且希望将他们的正则表达式工具包扩展到基本模式之外

# 文章动机

经过几年的专业编码，我在堆栈溢出和代码库中遇到了许多正则表达式模式，但是直到最近我才完全理解了核心正则表达式的概念。

例如，我经常在其他人的代码中看到一个构造，但是我以前完全没有想到，这个构造就是`(?:…)`。

我现在知道这被称为非捕获组，更重要的是，我应该什么时候使用它。

我认为我(以及我的同行)无知的原因是，大量在线正则表达式教程讨论的是特定用例的解决方案，比如检测名称或日期，而不是构建基础知识。

为了解决这个问题，我决定研究 RegEx，并对它进行深入的实验…本文试图记录我的学习成果，并与社区中的其他人分享。

*(剧透)*:解锁 RegEx 很多强大功能的主要成分是`Grouping`，正确理解它对你大有裨益。我将在本文中讨论这一点以及更多。

首先，让我回顾一下与 RegEx 相关的一些基础概念，您可能知道也可能不知道。

# 1.原始字符串

Python 中的“原始字符串”是通过在字符串前面加上`r`或`R`来创建的，比如:`r”Hello world”`。

常规字符串和原始字符串的区别在于，原始字符串将反斜杠(`'\’`)视为文字字符。

然而，在常规的 Python 字符串中，反斜杠用于表示转义序列，如`\n`(换行符)、`\r`(回车符)或`\t`(制表符)。

所以当我们想要创建一个包含反斜杠的字符串，并且不想让它被当作转义字符时，我们使用原始字符串。

这就是为什么我们使用原始字符串在 Python 中创建正则表达式字符串。因为 RegEx 引擎以一种特殊的方式解释反斜杠，而我们不希望 Python 因为自己对反斜杠的解释而修改字符串。

来自 Python 的官方文档:

> 正则表达式使用反斜杠字符(`'\'`)来表示特殊形式，或者允许使用特殊字符而不调用它们的特殊含义。这与 Python 在字符串文字中出于相同目的使用相同字符的做法相冲突；例如，要匹配一个文字反斜杠，可能必须将`'\\\\'`写成模式字符串，因为正则表达式必须是`\\`，并且每个反斜杠必须在一个常规 Python 字符串文字中表示为`\\`。另外，请注意，Python 在字符串中使用反斜杠时，任何无效的转义序列现在都会生成一个`[DeprecationWarning](https://docs.python.org/3/library/exceptions.html#DeprecationWarning)`，将来它会变成一个`[SyntaxError](https://docs.python.org/3/library/exceptions.html#SyntaxError)`。即使它是正则表达式的有效转义序列，也会发生这种行为。

**要点:**虽然没有必要使用原始字符串来创建 Python 中的正则表达式模式，但是强烈建议这样做。你会看到很多程序员使用 Python 中的原始字符串创建正则表达式模式。

# 2.`re`中方法调用的两种模式

首先，Python 的 RegEx 模块`re`中只有 4 个匹配函数。我所说的匹配函数是指在正则表达式模式和目标字符串之间执行实际模式匹配的函数:

1.  `match(...)` —返回目标字符串中的第一个匹配子字符串，作为`re.Match`对象(如果在字符串的开头找到)
2.  `search(...)` —返回目标字符串中的第一个匹配子字符串，作为一个`re.Match`对象，可在字符串中的任何位置找到
3.  `findall(...)` —以字符串列表的形式返回目标字符串中所有匹配的子字符串
4.  `finditer(...)` —返回目标字符串中所有匹配子字符串的`re.Match`对象的迭代器

但是`re`模块实际上允许您以两种等价的方式调用所有 4 个匹配的函数——或者

1.  通过编译正则表达式模式字符串(`p = re.compile(regex_pattern)`)创建的`re.Pattern`对象，或者
2.  通过模块级函数(`re.search()`举例)

方法 1 的示例:

模式级调用

方法 2 的示例:

模块级调用

在这个模块中调用方法的两种方式都会产生相同的结果。为了美观，你可能更喜欢其中一个，但仅此而已。

性能也相当，因为`re`模块缓存了您的模式对象。因此，重复调用模块级函数并不比对同一个 RegEx 字符串使用预编译的模式对象更糟糕。

**要点:**在 Python 的 RegEx 模块中有两种使用匹配函数的等效方法。你会在栈溢出和代码库中看到这两种类型的代码，但是不要强调这两种类型的区别，因为它们是等价的。

# 分组

最后，让我们分组讨论。

在正则表达式模式中，组是由圆括号括起来的任何东西。

换句话说，在`(`和`)`元字符之间的任何东西都是一个组。

从概念上讲，它们的解释方式与数学表达式中圆括号表达式的解释方式相同。

例如，当你在一个代数表达式中看到`(2+3)*5`时，你知道`2+3`是它自己的实体，或者一个组，根据 BODMAS 法则，它在其他任何东西之前被求值。

同样，在 RegEx 模式`r”(ab)c”`中，`ab`是一个组。

使用组有很多好处。首先，正则表达式组可以单独检索，也可以与其他组一起检索。

它还可以与其他元字符如`*`结合起来创建有意义的模式。

例如,`r”(ab)*c”`将捕捉表达式，其中有 0 个或多个组`ab`的实例，后跟一个`c`。

类似地，`r”(ab)?c”`将捕获为`"c"`或`"abc"`的字符串(允许组`ab`的 0 或 1 个实例)

这就是团队。现在让我们看看 RegEx 中一些重要的分组特性:

## 分组特征#1

一旦将包含组的正则表达式模式与目标字符串匹配，就可以通过调用`re.Match`对象上的`.group()`或`.groups()`来分别或共同检索匹配中的单个组(存储在`re.Match`对象中)。

正如我上面提到的，通过调用`re`模块的 4 个匹配函数之一，返回一个`re.Match`对象。

例如:

这里我们检索了正则表达式模式中的第一个，也是唯一的一个组。是的，组的索引从 1 开始，而不是 0。

实际上`m.group(0)`是一个特例，因为它返回整个匹配的字符串，而不是任何单独的组。上例中，那就是`abc`。

此外，`m.group(0)`相当于说`m.group()`，或者不向方法传递任何参数，因为默认参数是 0。

这可能就是为什么组的索引从 1 开始——`re`的开发者想为这个特殊函数保留 0？

事实上，当你在 Stackoverflow 上看到 RegEx 代码时，大多数时候，匹配的字符串是通过在 match 对象上调用`.group()`返回的。

## 分组功能#2

分组的第二个重要特性是，以后可以在相同的正则表达式模式中引用一个组。

这使您可以动态地引用目标字符串中的子字符串，而无需事先确切知道它是什么。

您可以将反斜杠与组索引结合使用。例如:

这里，`\1`与组`ab`匹配，因为它是正则表达式模式中的第一个组。

## 分组功能#3 —非捕获组

现在我们来看看文章开头提到的特征——非捕获组。

当您的小组以问号和冒号`?:`开头时，它被称为非捕获小组。

例如，我们可以将之前的 RegEx 模式修改为`r”(?:ab)c”`，以指示组`ab`为“非捕获”。

换句话说，您是在告诉 RegEx 引擎不要费心记住这个组，因为您对以后引用它或单独检索它不感兴趣。

让我们来看看实际情况:

试图引用非捕获组

这将返回一个错误，因为我们试图在表达式的后面用*引用*一个组，但是没有要引用的捕获组。

让我们看另一个例子:

尝试检索非捕获组

这一次我们得到了一个错误，因为我们试图从匹配的对象中检索第一个组(索引从 1 开始)，但是和以前一样，没有捕获的组。

这对于大型复杂的正则表达式模式很有用，在这种情况下，您可能有许多不同的组，但是您只对引用或检索其中的一些感兴趣。

Python 中 RegEx 组的一个相关特性是命名组，您可以给组命名，然后通过名称引用它们。

就是这样。这就是核心的分组逻辑和一些重要的分组特性。如果您想练习一些正则表达式代码，现在是暂停并消化上述概念的好时机。

本文的其余部分讨论了更多利用组的正则表达式特性，我认为它们也非常有用。

我将它们称为中间特性，但我知道这是主观的，所以请不要全信。

## m 组()与 m 组()

这是两个非常常见的正则表达式方法，一旦你理解了组，就很容易理解，但是为了全面起见，提到它们是很重要的。

1.  `m.groups()`给出正则表达式和目标字符串匹配的所有捕获组的列表。可以想象，非捕获组不包括在内。
2.  `m.group([group1, …])`给出对应于索引参数的特定组。如果您指定多个组索引，您将获得一个相应组字符串的元组。如前所述，`m.group()`或`m.group(0)`将返回整个匹配的字符串。

让我们来看看实际情况:

首先，让我们分析一下上面的 RegEx 模式中发生了什么。该模式希望目标字符串包含以下内容:

*   开始时应该至少有 1 个`ab`，但是可以有很多个，一个接一个(这就是元字符`+`的含义)。另外，`ab`是第一个捕获组
*   这后面应该是字母`c`
*   这后面应该是`de`，这是我们的第二个捕捉组
*   接下来应该是我们的第一个捕获组
*   最后，这后面应该跟一个`f`

## `findall()` vs. finditer()

这是另一个重要的二分法，它让我困惑了很久:

1.  `findall()`:顾名思义，这将返回一个 RegEx 模式和目标字符串之间所有匹配的列表，按照它们被找到的顺序。但是实际上有三种不同的场景使用这个函数，每一种都给出不同类型的结果。首先，让我们定义一个目标字符串，我们将根据它来匹配正则表达式模式:

*   **场景 1** :如果您的正则表达式中没有使用任何捕获组，`findall()`将返回与整个正则表达式模式匹配的所有子字符串的列表。简单。

*   **场景 2** :如果您在正则表达式中使用 1 个捕获组，`findall()`将返回一个列表，列出所有*匹配该组*的子字符串(不是全部匹配的子字符串)

如前所述，这只返回`['ab', 'ab', 'ab']`，而不是`['ab c', 'ab c', 'ab c']`。

实际上，这里还有另一个微妙之处——子串`ab test`不会被捕获，因为其中没有`ab c`。

因为在这种情况下,`.findall()`返回的是所有`ab`出现的列表，你可能认为答案中会有 4 个`ab`。

但事实并非如此，因为正则表达式模式`r"(ab) c"`与`"ab test"`不匹配。

*   **场景 3** :如果在正则表达式模式中使用多个捕获组(多于 1 个)，`findall()`将返回所有匹配的所有组的元组列表，格式为`[..(<group 1>, <group 2>…, <group m>)..]`

原来如此。

老实说，在我阅读它的文档之前，我经常被这个函数绊倒，我仍然不知道它的有效用例。

我将它包含在这里只是为了消除它的神秘性，但我不会在我的代码中使用它。

如果我想要一个字符串中正则表达式模式的所有匹配，我使用`finditer()`函数，解释如下:

2.`finditer()`:这个函数返回一个迭代器(read: Lazy Evaluation)来迭代目标字符串中 RegEx 模式的所有匹配(不管 RegEx 模式中有多少个组)。看起来是这样的:

迭代器对象中的对象类型是`re.Match`，可以用来访问整个匹配的子串(`m.group()`)，或者访问特定的组，比如 so `m.group(1, 2)`。

## 使用格式化字符串创建正则表达式模式

如果您想动态地创建正则表达式，也许是为了将用户输入合并到模式中，您可以将格式化的字符串传递给 Python 中的 RegEx 引擎。

如您所知，格式化的字符串，用`f"..."`表示，计算花括号`{…}`中的表达式，并将结果插入字符串中。

所以如果`variable1 = "world”`，那么格式化字符串`f“Hello {variable1}”`的值就是`“Hello world”`。

将格式化字符串传递给 RegEx 引擎并不奇怪，因为格式化字符串实际上就是一个字符串。

但是，你可能不知道你可以做一个格式化的原始字符串！例如`a = fr"Hello {variable1} \d"`也是一个有效的字符串。

当然，如果没有细微的差别，这一部分将会很无聊，所以这里是这样的:当你想在你的正则表达式模式中使用花括号`{}`时会发生什么，它表示正则表达式引擎中的重复限定符？

请记住，花括号在格式化字符串中有特殊的用途，所以它们将由格式化字符串构造来解释，而您的最终字符串实际上没有花括号。

例如，`r"hello{1,4}"`作为 RegEx 模式意味着匹配字符串至少需要有`1` `hello`，并且可以有多达`4` `hello`的重复。但是对于一个格式化的字符串，它意味着别的东西。

所以`fr"hello{1,4}"`实际上等于`“hello(1, 4)”`，因为 Python 中的格式化字符串结构将`1,4`解释为一个元组。

**回答:**为了保留被求值的字符串中的花括号，需要用双花括号对其进行转义！所以构建这个模式的正确方法是`fr"hello{{1,4}}"`，会翻译成`"hello{1,4}"`。

就是这样。这是我目前所有的正则表达式内容。希望您能从这篇文章中获得一些有用的东西。