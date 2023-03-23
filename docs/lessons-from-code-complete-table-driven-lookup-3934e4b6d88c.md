# 史蒂夫·麦康奈尔“代码完成”的教训

> 原文：<https://betterprogramming.pub/lessons-from-code-complete-table-driven-lookup-3934e4b6d88c>

## 表驱动查找

![](img/cf5062ea3f3c81900991d0c84a9fdd6c.png)

[drmakete 实验室](https://unsplash.com/@drmakete?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/future?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

*本文是 Steve McConnell 的计算机编程和软件工程经典著作《代码完整》系列的第一篇。*

麦康奈尔在《代码完整》中给出了各种很棒的建议，但他的一些例子使用了不再流行的语言，比如 Visual Basic。我的文章将通过使用 JavaScript 和 Python 等更流行的语言来更新示例。

本文将着眼于我们如何通过使用表驱动的查找而不是一长串的`if-else if`语句来改进我们的程序。

# 长 if-else if 语句的解决方案

先说一个日期问题。您需要能够将一个月与该月的天数相匹配。您的第一个直觉是编写一个长的`if-else if`语句，将日期分配给特定的月份。这是 Python 中的样子:

这是很长的一系列陈述。

稍加观察，您应该能够看到月份号对应于数组索引。有了这种认识，我们可以创建一个数组(或 Python 中的一个列表),通过将一个月中的日期与正确的数组索引相关联来存储每个月的日期。

下面是创建该列表的语句:

```
monthDays = [0,31,28,31,30,31,30,31,31,30,31,30,31]
```

请注意，我们在第一个列表元素中有一个 0，因为我们没有从 0 开始对月份进行编号。另一种方法是从一月的天数开始，然后从用户输入的数字中减去 1。

以下是完整的程序:

```
monthDays = [0,31,28,31,30,31,30,31,31,30,31,30,31]
month = int(input("What month is it (enter a number: )? "))
days = monthDays[month]
print("Month",month,"has",days,"days in it.")
```

我们已经把一个 26 行的程序删减到了 4 行，使它更具可读性和效率。

因为我们对一个月中的日子这样做，所以我们也可以对月份的名称这样做，这样我们的程序就可以通过名称而不仅仅是数字来引用月份。下面是一个修改过的 Python 程序，它有一个月份名称列表:

```
monthDays = [0,31,28,31,30,31,30,31,31,30,31,30,31]
monthNames = ["", "January", "February", "March", "April",
              "May", "June", "July", "August", "September",
              "October", "November", "December"]
month = int(input("What month is it (enter a number: )? "))
days = monthDays[month]
monthName = monthNames[month]
print(monthNames[month],"has",days,"days in it.")
```

让我们看看用 Python 解决这个问题的另一种方法。

将一个月与其中的天数进行匹配的问题对于字典来说是一个完美的问题，字典是一种将键与值进行匹配的数据结构。在这种情况下，月份是键，月份中的天数是值。

下面是一个月中的日子问题的字典解决方案:

我在这些例子中创建了两种类型的*查找表*。查找表是一种数据结构，通过访问索引(在数组或列表的情况下)或键(在字典的情况下)可以找到所需的数据。

# 布尔函数示例

布尔函数(返回布尔值的函数)可以包含一个查找表，并用来代替`if-else if`语句。不过，首先让我们看一个不使用 JavaScript 中的查找表的例子。

这个问题要求你计算一个字符串中元音的个数。您可能会尝试使用一个`if-else if`语句来检查字符串中的每个字符，如下所示:

但是更好的方法是将元音字母存储在一个数组中，然后检查数组中是否有指定的字符。下面是使用布尔函数编写程序的一种方法:

与之前使用`if-else if`语句的程序相比，这个程序的一个优点是我使用了内置的 JavaScript 数组函数`includes`。使用内置函数比遍历数组将每个元素与给定字符进行比较更有效，这将导致我在函数定义中使用`if-else if`。

# 使用阶梯式查找表

麦康奈尔在*代码完成*中演示的另一种类型的查找表是阶梯式查找表。当问题不能用“整洁的”数组或列表索引来建立时，使用这种类型的表。

McConnell 使用的示例是一个评分应用程序，您必须为数字分数分配一个字母等级。如果数值分数的范围不规则，则不能使用普通的查找表。

分数的分界点是:50 分或低于 F；高达 65.0D；高达 75.0°C；高达 90.0 B；高达 100.0 A。

程序是这样的:

这段代码的工作方式是遍历整个分数范围，并将学生的分数与`gradeRange`数组中列出的上限进行比较。当一个学生的分数超过一个范围的顶端时，一个字母等级被确定。

试图使用`if-else if`逻辑编写这个程序会使代码过于复杂，并可能导致逻辑错误。与前面的示例一样，使用这种类型的表的另一个优点是，如果需要，使用查找表可以更容易地更改范围。

# 查找表的优势

与`if-else if`逻辑相比，使用查找表有几个优点。

首先，使用查找表的代码更容易阅读，尤其是当`if-else if` 语句很长的时候。另一个优点是，如果逻辑发生变化，查找表更容易更新。试图改变一个复杂的`if-else if` 语句可能会导致错误。

正如 McConnell 所说，如果您发现在遵循 if-else if 语句的逻辑时遇到问题，可以考虑将其更改为查找表。

感谢阅读这篇文章。请给我发电子邮件，提出意见和建议。