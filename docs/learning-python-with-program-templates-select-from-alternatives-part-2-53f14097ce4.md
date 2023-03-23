# 使用程序模板学习 Python:从备选方案中选择第 2 部分

> 原文：<https://betterprogramming.pub/learning-python-with-program-templates-select-from-alternatives-part-2-53f14097ce4>

## 探索布尔运算符

![](img/93c924dc35003aafee4eed32399141c8.png)

Patrick Amoy 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在我的上一篇文章中，我向您介绍了 Select From Alternatives 程序模板，以及如何使用 Python 的 if 语句实现该模板。在本文中，我将继续讨论 Select From Alternatives 模板，这次使用布尔逻辑使我们的条件语句更加复杂。

# 布尔运算符

布尔逻辑允许我们一次分析多个关系表达式。例如，如果一个工人是小时工，并且该工人一周工作超过 40 小时，那么工资程序可以支付加班费。为了能够编写条件语句来处理这个逻辑，我们需要利用 Python 的布尔运算符。

Python 有三个布尔运算符:

*   `and`
*   `or`
*   `not`

我们可以用真值表来说明这些操作符是如何工作的。真值表描述了两个布尔值(真和假)以及当布尔运算符应用于这两个布尔值时会发生什么。

以下是`and`操作员的真值表:

*   真实与真实->真实
*   真假->假
*   假与真->假
*   假与假->假

下面是`or`操作符的真值表:

*   真实还是真实->真实
*   对还是错->对
*   假或真->真
*   假还是假->假

最后，这里是`not`操作符的真值表:

*   不真->假
*   非假->真

现在让我们看看如何在我们的程序中使用这些布尔运算符来做决定。

# 从数字分数中选择等级

在我的上一篇文章中，我演示了如何获得一个数字分数并给它分配一个字母等级。在这个例子中，我将更进一步，给数字分数分配加减分。为了防止这个项目变得太大，我将坚持只给 A 分。

在这个例子中，我将在 90 到 93 之间给 A-打分，在 94 到 97 之间给 A+打分，在 98 到 100 之间给 A+打分。程序如下:

```
numGrade = int(input("Enter a score from 90 to 100: "))
if numGrade >= 90 and numGrade <= 93:
  letterGrade = "A-"
if numGrade >= 94 and numGrade <= 97:
  letterGrade = "A"
if numGrade >= 98:
  letterGrade = "A+"
else:
  print("Numeric score out of range.")
print(numGrade, "is a",letterGrade)
```

以下是该程序的一些运行情况:

输入一个从 90 到 100 的分数:96
96 是 a。

输入一个 90 到 100 的分数:99
99 是 A+。

输入一个从 90 到 100 的分数:91
91 是 A-。

这个例子演示了布尔运算符`and`。我的下一个例子向您展示了`or`操作符是如何工作的。

# 做出贷款决策

银行有一个贷款项目给那些没有很多信用记录的人。从银行获得初始贷款的标准是:1)申请人已经工作了至少五年，或者 2)申请人的年薪至少为 30，000 美元。

下面是一个程序，它使用这些标准对贷款做出是或否的决定:

```
yearsOnJob = int(input("How many years have you been employed at your current job? "))
salary  = int(input("What is your current salary? "))
if yearsOnJob >= 5 or salary >= 30000:
  decision = "Yes"
else:
  decision = "No"
print("The loan decision is ",decision)
```

以下是该程序三次运行的输出:

你在目前的工作岗位上工作了多少年？6
你现在的工资是多少？25000
贷款决定是。

你在目前的工作岗位上工作了多少年？3
你现在的工资是多少？40000
贷款决定是。

你在目前的工作岗位上工作了多少年？4
你现在的工资是多少？25000
贷款决定为否

现在我们已经看到了如何使用`or`操作符，让我们看看最后一个布尔操作符— `not`。

# 确定一个数是偶数还是奇数

判断一个数是偶数还是奇数的一个巧妙的技巧是求这个数取模 2 的结果。如果结果是 0，这意味着 2 被这个数整除，所以这个数必须是偶数。如果结果是 1，这意味着 2 没有被这个数整除，所以这个数一定是奇数。

下面是一个使用`if-else`来判断一个数字是奇数还是偶数的程序:

```
number = int(input(“Enter a number: “))
if number % 2 == 0:
  print(number,”is even.”)
else:
  print(number,”is odd.”)
```

我们也可以使用`not`操作符来编写这个程序。方法如下:

```
number = int(input("Enter a number: "))
if not number % 2 == 0:
  print(number,"is odd.")
else:
  print(number,"is even.")
```

这个程序运行了两次:

输入一个数字:3
3 是奇数。

输入一个数字:4
4 是偶数。

这似乎是扭曲的知识，但有时使用`not`操作符比用其他方式表达逻辑更有意义。

当我们在本系列后面讨论循环和函数时，我将演示一些更简单的操作符`not`的用法。

# 最后的想法

在一些情况下，你需要布尔操作符来表达在你的程序中做决定所需的逻辑。本文展示了三个布尔操作符的例子— `and`、`or`和`not`。

你还需要记住，通常有几种方法可以表达决策逻辑，如果你得到正确的结果，每种方法都是正确的。在学习计算机编程的过程中，很多时候你会认为只有一种方法可以写出正确的代码，但是正如著名的 Perl 程序员拉里·沃尔喜欢说的，“有多种方法可以做到这一点。”

在我的下一篇文章中，我们将了解 Python 的另一个非常重要的特性——重复执行一个或多个语句的能力。这被称为循环，我将介绍两种类型的循环语句——`while` 语句和`for`语句。我将要介绍的程序模板是输入模板，处理模板。

感谢阅读，并请留下意见和建议。