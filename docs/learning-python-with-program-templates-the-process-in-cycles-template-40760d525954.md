# 使用程序模板学习 Python:循环中的过程模板

> 原文：<https://betterprogramming.pub/learning-python-with-program-templates-the-process-in-cycles-template-40760d525954>

## 这一过程的简要指南

![](img/3c2c78c6b15edf06ba9357f6fbe59ab3.png)

[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

计算机编程中有几个问题涉及到循环过程。下面我要演示的一个例子是从一个数字中提取数字。这种处理类型有一个程序模板，即循环处理。它可以用一个`while`语句或一个`for`语句来实现，我将向您展示每个语句的例子。让我们开始吧。

# 循环流程计划模板

需要 Cycles 模板中的过程的编程问题的一般示例并不多，所以我将向您展示我将要演示的两个示例的伪代码。

第一个例子是从一个数字中提取数字。数字提取包括使用模数运算符返回数字的最后一位，然后使用整数除法将该数字从数字中删除。下面是伪代码:

```
*while there are digits to extract:
 extract the last digit by returning the number modulo 10
 integer-divide the number by 10*
```

当我说整数除法时，我的意思是将数除以 10，但忽略任何剩余的小数部分。我们可以通过在除法表达式上调用`int`函数来实现。

下一个例子是以表格形式显示数据。例如，如果您有 30 个数字，并且您想在一行中显示 10 个数字，您可以使用“循环处理”模板来执行此任务。当计数器变量模 10 等于 0 时，程序决定何时结束一行数据。

下面是伪代码:

```
*set item count to 0
 for each data item in the set:
 display item
 increment count by 1
 if count % 10 equals 0 then move to the next line*
```

现在我们已经看到了这个模板是如何工作的，让我们为我刚刚讨论的两个问题实现它。

# 数字提取

下面是一个程序，它提示用户输入一个数字，并使用“循环处理”模板从数字中提取每个数字:

```
number = int(input("Enter a number: "))
while number > 0:
  print(number % 10, end = " ")
  number = int(number / 10)
```

下面是该程序几次运行的输出:

输入一个数字:232
2 3 2
输入一个数字:1234
4 3 2 1
输入一个数字:123945
5 4 9 3 2 1

请注意，输出首先显示最后一个数字，然后继续显示数字的第一个数字。

# 格式化时间表

我的第二个例子是数字 1 到 100 的格式化时间表。这个程序也使用了模数操作符，这一次是为了控制一个新行何时开始。因此，例如，在程序显示 1 乘以 10 之后，打印一个新行来开始 2 的行。

程序是这样的:

```
for i in range(1, 11):
  for j in range (1, 11):
    print(i*j, end = "\t")
    if j % 10 == 0:
      print()
```

下面是程序的输出:

1 2 3 4 5 6 7 8 9 10
2 4 6 8 10 12 14 16 18 20
3 6 9 12 15 18 21 24 27 30
4 8 12 16 20 24 28 32 36 40
5 10 15 20 25 30 35 40 45 50
6 12 18 24 30 36 42 48 54 60
7 14 21 28 38 40 48 56 64 72 80
9 18 27 36 45 54 63 72 81 90
10 20 30 40 50 60 70 80 90 100

# 最后的想法

“循环过程”程序模板在计算机编程中并不常见，但在某些情况下会很有用。当您试图以表格格式排列输出时，您肯定会发现它非常有用。

感谢阅读。请留下意见和建议。