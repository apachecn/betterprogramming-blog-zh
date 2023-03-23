# 使用程序模板学习 Python:从备选方案中选择(第 1 部分)

> 原文：<https://betterprogramming.pub/learning-python-with-program-templates-select-from-alternatives-part-1-68b72acb214e>

## 创建灵活计划的有趣方式

![](img/6d84b80b4a29006f1c9e55f732fe04bd.png)

Alex Kotliarskyi 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在本文中，我将介绍*从选项中选择*程序模板。这个模板为程序员提供了一个在程序中做决定的过程。模板是使用 Python 的`if`语句实现的。

许多编程语言也提供了`case`语句作为实现该模板的方式，但是 Python 没有 case 语句，所以必须使用`if`语句。

然而，在介绍模板之前，我需要了解一些使用`if`语句的预备知识，主要是关系操作符。在我的下一篇文章中，我将继续讨论这个模板，但是是在处理需要使用布尔运算符的值范围的上下文中。

# 关系运算符

你想用计算机程序解决的大多数问题都需要比较数值来决定程序应该做什么。例如，在一个工资单程序中，程序必须检查一个雇员的时间表，以确定该程序是为工作 40 小时或更少的雇员支付正常时间工资，还是为工作超过 40 小时的雇员支付加班费。

为了比较值，我们必须使用一个关系运算符。以下是这些运算符的列表:

*   `>`大于
*   `>=`大于或等于
*   `<`小于
*   `<=`小于或等于
*   `==`等于
*   `!=`不等于

这些运算符是二元运算符，这意味着运算符的两边必须有一个值。该值可以是变量或文字。包含关系运算符和两个值的表达式称为关系表达式或条件表达式。

以下是一些例子:

```
100 > 50
"Joe" == "joe"
salary < 50000
hoursWorked <= 50
(regularPay * 1.1) > 1000
```

关系表达式返回一个布尔值。布尔值为`True`和`False`。这就是 Python 在程序中做决定的方式。编写一个关系表达式，如果结果是`True`，程序向一个方向运行，如果结果是`False`，程序向另一个方向运行。

要做到这一点，您必须使用带有`if`语句的关系表达式。我接下来会讨论如何使用这个语句。

# if 语句

Python 的`if`语句是用来在程序中做决策的。`if`语句分析一个关系表达式，然后根据关系表达式的结果执行一组或另一组代码。

if 语句有几种形式。它们是:1)简单的`if`语句；2)`if-else`声明；以及 3)`if-elif`语句。

让我们先来看看简单的 `if` 语句的语法模板:

```
*if relational-expression:
 statement(s)*
```

关于这个模板有两点需要注意。首先，注意关系表达式后面的冒号。这个冒号是必需的，省略它会导致错误。还要注意，术语“语句”表示如果关系表达式产生一个`True`值，可以有一个或多个要执行的程序语句。

让我们看一个例子。这个例子演示了如何检查一个数字是否是偶数。该程序使用模数运算符来检查当数字被二除时是否有余数。如果没有余数，这个数就是偶数。如果有余数，这个数就是奇数。代码如下:

```
number = int(input("Enter a number: "))
if number % 2 == 0:
  print(number,"is even.")
```

以下是该程序两次运行的输出:

```
Enter a number: 22
22 is even.
Enter a number: 133
133 is odd.
```

请注意，当输入一个奇数时，什么也没有发生。当我在下面讨论`if-else`语句时，我会立即解决这个问题。

# if-else 语句

大多数情况下，如果关系条件为真，您希望程序执行一组语句，如果条件为假，则执行另一组语句。要做到这一点，我们需要扩展`if`语句以包含一个`else`子句，这将导致执行另一组语句。这个语句被称为`if-else`语句。

以下是 if-else 的语法模板:

```
*if relational-expression:
 statement(s)
else:
 statement(s)*
```

让我们通过修改上面的程序来说明`if-else`语句是如何工作的，该程序用于确定一个数是否是偶数，也让程序识别奇数。程序是这样的:

```
number = int(input("Enter a number: "))
if number % 2 == 0:
  print(number,"is even.")
else:
  print(number,"is odd.")
```

以下是该程序两次运行的输出:

```
Enter a number: 2452
2452 is even.
Enter a number: 1333
1333 is odd.
```

`if-else`语句是一种非常常见的条件语句，也是您在编程中经常使用的语句。

还有一种 if 语句的形式需要讨论——用于做出多个`if-else`决策的`if-elif`语句。

# if-elif 语句

在程序中，很多时候你需要从多种可能性中做出选择，而`if-else`语句不够强大。Python 有一个合适的语句——`if-elif`语句。

让我们从这个语句的语法模板开始:

```
*if relational-expression:
 statement(s)
elif relational-expression:
 statement(s)
…
else:
 statement(s)*
```

省略号(…)表示语句中可以有多个`elif`子句。

让我们看一个例子来演示`if-elif`语句是如何工作的:

下面是这个程序运行一次的输出:

```
Choose one of these things to do outside:
1\. Go for a hike.
2\. Play tennis.
3\. Play golf.
Enter choice: 2
```

网球场在两个街区之外。

既然我们已经讨论了如何编写`if`语句，让我们看看如何使用它们来实现*从选项中选择*程序模板。

# 从备选方案中选择项目模板

该模板用于根据给定值从一组备选项中进行选择。老实说，我已经用上面的`if-elif`语句的例子演示了这个模板是如何工作的，但是我将用另一个例子再次演示这个模板。

下面的程序让用户输入一个数字测试分数，程序确定获得的字母等级。程序如下:

以下是该程序的一些运行情况:

```
Enter the numeric score: 77
A grade of 77 is the letter grade CEnter the numeric score: 80
A grade of 80 is the letter grade BEnter the numeric score: 54
A grade of 54 is the letter grade F
```

# 最后的想法

这篇文章演示了如何使用 *Select From Alternatives* 程序模板在您的程序中做出选择。在我的下一篇文章中，我将向您展示如何使用更复杂的替代方法来实现模板，比如从值的范围中进行选择，这将涉及到使用布尔运算符。

感谢阅读。请留下意见和建议。