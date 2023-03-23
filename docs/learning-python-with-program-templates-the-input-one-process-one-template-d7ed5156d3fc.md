# 用程序模板学习 Python:输入模板，处理模板

> 原文：<https://betterprogramming.pub/learning-python-with-program-templates-the-input-one-process-one-template-d7ed5156d3fc>

## 在程序中实现循环的有趣方式

![](img/109ac2accd23c6932697f9d854d9e525.png)

由 [David Rangel](https://unsplash.com/@rangel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在以前的文章中，我讨论了用于输入数据、输入、处理和输出数据的程序模板，以及通过从备选方案中进行选择在程序中做出决策。在本文中，我将讨论一个用于编写程序的程序模板，该程序接收数据输入，然后在处理新数据之前处理这些数据。

这个模板叫做输入一，过程一。当您的程序在执行处理之前无法接收所有数据时，或者当您只想让程序一次输入并处理一个数据元素时，应该使用此模板。

您可以将这个模板与我们之前看到的模板进行比较:输入、流程、输出。当您希望在执行任何处理和输出之前将所有数据输入到程序中时，可以使用该模板。使用输入一个，处理一个模板，输出在处理步骤中发生。

输入 One，Process One 模板是使用循环语句实现的。我将使用 Python 的`for`语句来演示这个模板，在另一篇讨论实现不同模板的文章中，我将介绍 Python 的 while 语句来实现循环。

# for 语句

`for`语句是一个循环结构，它指示程序将一组编程指令重复设定的次数。用 Python 写 for 语句有几种方法。首先，我将向您展示一个语法模板:

```
*for var in range(start, end):
 do something*
```

这个语法模板使用内置的`range`函数。此函数允许您指定一个数字范围，该范围表示您希望 for 语句进行的迭代(循环或执行)次数。例如，如果我想让`for`语句迭代十次，我可以使用范围`(1,11)`。即使我只想要十次迭代，我也必须指定`11`作为范围的停止点。

现在，让我们看看`for`语句是如何工作的。第一个示例打印数字 1 到 10:

```
for i in range(1, 11):
  print(i)
```

`for`语句获取范围(1 到 10)的每个值，并将其赋给变量`i`。然后执行`for`中的语句，使`i`的值显示在屏幕上。

现在我们已经回顾了这个版本的`for`语句是如何工作的，让我们看看如何使用它来实现输入一个，处理一个模板。

# 实现输入一个，处理一个模板

输入一，流程一模板的伪代码是这样写的:

```
*Repeat the following steps:
 Input data
 Process the data*
```

实际上，循环迭代的次数通常是预先知道的。如果不是，应该使用另一个循环语句，即`while`语句。

现在我们来看一个例子。在早期的一篇文章中，在从备选方案中选择模板中，我使用了一个例子，其中一个数字测试分数被分配了一个字母等级。让我们用这个例子来演示如何使用输入模板和处理模板。

该示例提示用户使用 for 语句输入五个数字等级。输入等级后，会显示一个字母等级。程序是这样的:

```
for i in range(1,6):
  numGrade = int(input("Enter the test grade: "))
  if numGrade >= 90:
    print(numGrade,"is an A.")
  elif numGrade >= 80:
    print(numGrade,"is a B.")
  elif numGrade >= 70:
    print(numGrade,"is a C.")
  elif numGrade >= 60:
    print(numGrade,"is a D.")
  elif numGrade < 60:
    print(numGrade,"is a F.")
  else:
    print(numGrade,"is not a valid grade.")
```

下面是该程序运行一次的输出:

进入测试等级:56
56 是 F.
进入测试等级:78
78 是 C.
进入测试等级:82
82 是 B.
进入测试等级:99
99 是 A.
进入测试等级:66
66 是 d

# 将字符串改为大写

另一个输入的例子，处理一个模板，让我们写一个程序，接受小写字母的字符串并将其转换成大写字母。要完成这项任务，我们必须认识到字符串可以分解成单个字符，就像 range 函数将一个范围分解成单个数字一样。

为了演示如何将字符串分解成单个字符，下面的程序获取一个字符串，并在它自己的行上打印每个字符:

```
for i in “hello”:
  print(i)
```

这个简短的程序产生以下输出:

h
e
l
l
o

现在我们准备写程序把一个字符串从小写变成大写。不过，首先，我需要向您介绍将小写字符转换为大写字符的函数— `upper`。

该函数的工作原理是将它附加到一个字符的末尾。调用时，该函数将字符作为大写字符返回。下面是一个使用 Python shell 的示例:

```
>>> “a”.upper()
‘A’
```

现在我们准备写程序了。这是:

```
lowercase = input("Enter a string: ")
for letter in lowercase:
  print(letter.upper(), end="")
```

以下是该程序几次运行的输出:

输入一个字符串:hello
HELLO
输入一个字符串:hello world
HELLO WORLD
输入一个字符串:supercalicaligilicious sexperidocious
supercalicaligilicious sexperidocious

# 最后的想法

Input One，Process One 模板只是利用循环语句实现的几个模板之一。我们使用 for 语句实现了这个模板。

在我的下一篇文章中，我将介绍一个新的程序模板 Input and Process Until Done，并介绍 Python 中的另一个主要循环结构 while 语句。

感谢阅读。请留下您的意见和建议。