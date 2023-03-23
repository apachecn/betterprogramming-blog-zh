# Python 编程入门—第 8 部分

> 原文：<https://betterprogramming.pub/beginning-python-programming-part-8-966f1189c1fe>

## 格式化技术、列表理解和 lambdas

![](img/9850399f03a7f696094f747e7cc9aab2.png)

照片由[阿玛多·洛雷罗](https://unsplash.com/@amadorloureiroblanco?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在上一篇文章中，我们看了一下类以及它们是如何被创建和销毁的。我们还研究了如何为不同的用例在子类中覆盖特定的功能。

[](https://medium.com/@broebling/beginning-python-programming-part-7-2f7df98b7c68) [## Python 编程入门—第 7 部分

### 在上一篇文章中，我们讨论了类、方法和属性。

medium.com](https://medium.com/@broebling/beginning-python-programming-part-7-2f7df98b7c68) 

我们没有讨论元类，元类被认为是高级 Python 编程。类控制实例如何工作，而元类控制类本身如何工作。如果你想了解更多这方面的信息，我建议你参考托马斯·伍特斯的这篇关于堆栈溢出的文章。

今天我们将讨论字符串格式、列表理解和 lambdas。

# 格式化

到目前为止，我们一直使用加法运算符连接字符串。虽然这适用于简单的字符串，但它的伸缩性不好。我们将讨论几个不同的格式化选项，所以当你的程序需要格式化的时候，你可以自己决定什么是最好的解决方案。

处理字符串的第一种方法是使用`%`。许多用 Python 2 编写的程序都是这样做的。

我们有三个变量:`dog_name`、`owner_name`和`dog_age`。我们使用%格式来构建使用这三个变量的句子。

Python 在一个字符串中寻找`%`符号，后跟一个字母，可以是`s`、`d`或`f`。它们分别代表字符串、十进制和浮点。在字符串的末尾，我们包含了一个`%`符号，后跟一个变量的*元组*，Python 应该用它来替换字符串中使用的*转义序列*。

我在上面的例子中故意排除了浮动，因为它们可能会产生意想不到的结果。

等等，不对，它应该只显示两位小数，而不是六位。发生了什么事？

默认情况下，`%f`显示六位小数。我们可以通过使用`%.2f`而不是`%f`来覆盖它。`.`是小数点，`2`是小数点后显示的小数位数。

如果你把`2`放在小数前面，你会从`$`得到整数前面足够的空格。

我们还可以通过使用字符串对齐来使它变得更简洁。

现在我们使用`%-10s`用十个字符填充第一个字符串。香草和巧克力周围加了空格，所以串和草莓一样长。如果我们不包括`-`符号，我们将在字符串的前面添加足够的空间，以便香草和巧克力将与草莓对齐。

下一个字符串格式化程序是`.format`。

`.format`是格式化字符串的“新”方法，使格式化字符串更容易阅读。

它不再像以前那样搜索`%`符号，而是用格式函数中传递的变量替换`{}`。在本例中，每个占位符的替换顺序与变量出现在`.format`中的顺序相同。

如果你有多个占位符需要用同一个变量来填充，你可以在花括号中放置数字来表示`.format`中参数的位置。例如`{0}`、`{1}`、`{2}`。在一个变量被多次使用的地方，你可以在花括号中重用它的位置。

如果需要对浮点值进行格式化，可以使用下面的语法来获得与上面相同的结果:`{0:2.2f}`。冒号将变量与格式分开。`2.2f`小数点前最多加两个空格，小数点后最多加两个零。

您还可以命名位置，并以任意顺序将它们作为名称传入。

`.format`还有一个锦囊妙计，但前提是你要使用指定的位置。

`**locals()`是一个处理程序(或者一个指向指针的指针),它在执行时查看所有可用的变量。它扫描已命名的位置，并查找具有相同名称的变量。如果找到一个，它就用已命名的位置替换找到的变量。如果没有，您会收到一个`KeyError`。这有助于保持代码简单。

还有更多——我可以写一整篇关于格式化的文章——但是现在，在我们继续列出理解之前，我们将讨论另一种类型的格式化程序。

转义序列有很多种，`\t`表示制表符，`\s`表示空格，`\\`表示反斜杠，`\"`表示双引号，`\'`表示单引号。只有在相同类型的字符串中使用双引号或单引号时，才需要两个引号。

要忽略这些，我们可以使用原始字符串。

```
"This is how you make a new line: \\n"# can also be typed asr"This is how you make a new line: \n"
```

第一个引号前的`r`告诉 Python 忽略转义序列。这在制作正则表达式模式时很有用。

本来想过去的是弦乐前面的`f`。`f`消除了在字符串末尾添加`.format`的需要，并寻找局部变量插入到字符串中。

看到那看起来有多干净了吗？它确实有效——复制并粘贴到您的编辑器中，让您自己看看。

当您在花括号内键入变量名称时，某些编辑器(如 PyCharm)可以帮助您。这可以节省时间，其他开发人员也喜欢这样阅读代码。

![](img/90aba1805b4213c92532483776901444.png)

[Kelly Sikkema](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 列表理解

我们可以用列表做各种很酷的事情。列表理解允许我们从字符串中创建列表，或者有步骤地生成编号列表:

这里我们可以看到，我们循环了“hello”中的所有字符，并创建了一个列表，其中每个字母都是列表中的一项。

您还可以在列表理解中使用条件来仅返回您想要的值。

在这里，我们循环遍历单词“hello”中的每个字母如果当前字母`x`也在列表`vowels`中，那么我们应该将它包含在列表`hello_vowels`中。最后，我们打印我们的列表，发现我们只去掉了“hello”中的元音。

这里有一个问题:如果我们检查的单词是“HELLO ”,我们不会从中提取任何元音，因为“E”不等于编程中的“E”——我们检查的是字节，而不是英文字符。更多信息参见 [ASCII 表](http://www.asciitable.com/)。

为了修复这个 bug，我们可以在我们的列表理解中执行`.lower()`函数，它属于所有的字符串和字符类型。

但是我们引入了一个新问题。如果我们想要这些字母的大写版本呢？在这种情况下，我们会将大写元音添加到我们的`vowels`列表中，并从我们的“HELLO”字符串中删除`.lower()`。

让我们更进一步:

在这里，我们可以确定一个字母在一个单词中是辅音还是元音，并创建一个列表，以后我们可以用它来做其他事情。

![](img/06aec786d2af604c27160ab16e2555f4.png)

照片由[蒂姆·马歇尔](https://unsplash.com/@timmarshall?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 兰姆达斯

不是那种羊肉！

Lambdas 是匿名函数。也就是说，它们没有函数签名。Lambdas 可以有任意数量的参数，但总是执行相同的表达式:

当你创建一个 lambda 时，你可以像传递变量一样传递它；但是，它应该用作高阶函数的参数。高阶函数将一个函数作为它们的参数之一，或者返回一个函数作为结果。

下面是几个高阶函数的例子和它们的功能描述:

*   `filter(func,x)` —过滤列表中符合表达式的元素

*   `map(func,x)` —对列表中的每个项目执行表达式

Lambdas 很棘手，虽然它们可以节省您的打字量，但它们会使您的代码难以理解。使用 lambdas 时，尽量确保您的代码是描述性的和明确的。

# 摘要

今天我们讨论了格式化技术、列表理解和 lambdas。也许我应该把它分成不同的部分，但是我想这已经足够让你理解每一部分的要点了。

我建议回顾一下以前的代码，使用字符串格式，以获得一些经验。尝试将一些使用列表的旧示例转换为列表理解，以获得相同的结果。对于 lambdas，我强烈建议使用它们来更好地了解它们是如何工作的。

# 推荐阅读

第 5.1、5.3 和 5.6 章

 [## 5.数据结构-Python 3 . 7 . 3 文档

### 移除列表中给定位置的项目，并将其返回。如果未指定索引，则移除并返回…

docs.python.org](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists) 

第 4.7 章

 [## 4.更多控制流工具— Python 3.7.3 文档

### Python 中的语句与您可能习惯的 C 或 Pascal 中的语句略有不同。而不是总是迭代一个…

docs.python.org](https://docs.python.org/3/tutorial/controlflow.html#more-on-defining-functions) 

和第 7.1 章

 [## 7.输入和输出-Python 3 . 7 . 3 文档

### 该函数旨在返回人类可读的值的表示，而旨在生成…

docs.python.org](https://docs.python.org/3/tutorial/inputoutput.html#fancier-output-formatting) 

# 下一步是什么

接下来是与处理代码中的错误相关的一切。我们都有错误——有时你对问题有远见，你知道如何处理它们，但有时却无法回避。敬请关注！与此同时，实践你所学到的，创造。

[](https://medium.com/better-programming/beginning-python-programming-part-8-45cad890e6b) [## Python 编程入门—第 9 部分

### 关于 Python 中的错误处理，您需要知道的一切

medium.com](https://medium.com/better-programming/beginning-python-programming-part-8-45cad890e6b)