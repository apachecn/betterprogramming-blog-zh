# 使用程序模板学习 Python:提示，然后阅读

> 原文：<https://betterprogramming.pub/learning-python-with-program-templates-prompt-then-read-89ffe01ba765>

## 知道如何正确地接收用户的输入

![](img/2f9b9c9464fccbaa1c50e3480f3c99ad.png)

照片由[史黛芬·谭](https://unsplash.com/@stefentan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

将数据输入 Python 程序的最常见方法之一是让用户从键盘输入数据。有一个程序模板描述了如何实现这种类型的输入。是提示，然后读模板。

在本文中，我将演示如何实现这个模板，以及如何正确地接收来自用户的输入，以便您的程序能够处理它。

# 输入功能

Python 提供了从键盘接收数据的函数——`input`函数。当用户向程序中输入数据时，该函数使 Python 程序暂停，然后当用户按下回车键时，程序继续运行。

有两种方法可以使用`input`功能——没有提示和有提示。

下面是不带提示的输入函数的语法模板:

```
variable = input()
```

该函数获取在键盘上输入的任何数据，并将其存储在赋值运算符另一端的变量中。

以下是一些例子:

```
name = input()
radius = input()
```

使用这个版本的输入功能的问题是用户可能不知道应该输入什么数据。这个问题的解决方案是使用函数的第二个版本。以下是该版本的语法模板:

```
variable = input(prompt)
```

下面是这个版本的`input`函数的一个例子:

```
name = input("What is your name? ")
print("Hello," name)
```

以下是该程序的输出:

```
What is your name? Ringo Starr
Hello, Ringo Starr
```

当你向用户提供一些文本解释他们应该输入什么时，你提供了一个提示。在从用户那里收集数据时提供提示是使你的程序对用户友好的一个重要方法。

# Python 中的数字输入

当用户完成向键盘输入数据时，input 函数返回一个字符串。如果这是您想要的数据类型，就没有问题。但是如果您输入的是数字数据，您不希望接收数据的变量将其存储为字符串。

让我演示当数字数据作为字符串输入变量时会发生什么。下面的程序从用户那里获得一个工资金额，然后尝试将其增加 10%。程序如下:

```
salary = input("Enter your salary: ")
salary = salary * 1.10
print("The new, increased salary is: ",salary)
```

下面是我运行这个程序时的输出:

```
Enter your salary: 20000
Traceback (most recent call last):
File “C:\Users\mmmcm\Documents\python\prog.py”, line 2, in <module>
salary = salary * 1.10
TypeError: can’t multiply sequence by non-int of type ‘float’
```

这是一个数据类型不匹配错误的例子，因为我试图将一个字符串乘以 1.10。Python 做不到这一点，所以它只打印一条错误消息并停止程序。

这个问题的解决方案是将来自输入函数的数据转换成适当的数值类型。有两种数值类型可以将数据转换为 int 和 float。

因为将数据从字符串转换为数字类型非常常见，所以 Python 为这种转换提供了两个内置函数，方便地命名为 int 和 float。

以下是描述如何使用这些函数的语法模板:

```
variable-name = conversion-function(data)
```

但是，要将这些函数与输入函数一起使用，我们需要一个不同的语法模板:

```
variable-name = conversion-function(input(prompt))
```

让我们使用这个模板从用户那里获取数字数据。下面是一个例子，要求用户输入他们当前的薪水，然后显示他们加薪 20%会赚多少钱:

```
salary = int(input("What is your salary? "))
print("Your current salary is:", salary)
increase = 1.1
salary = salary * increase
print("Your increased salary is:", salary)
```

请注意，在用户输入金额后，薪水由 int 函数进行转换。

以下是该程序的输出:

```
What is your salary? 35000
Your current salary is: 35000
Your increased salary is: 38500.0
```

现在让用户输入增加的数量，这样我们可以看到浮点转换函数是如何工作的。这是新的程序:

```
salary = int(input("What is your salary? "))
print("Your current salary is:", salary)
increase = 
  float(input("What is the percent of your increase (10% = 1.10)? "))
salary = salary * increase
print("Your increased salary is:", salary)
```

下面是这个程序运行一次的输出:

```
What is your salary? 30000Your current salary is: 30000What is the percent of your increase (10% = 1.10)? 1.10Your increased salary is: 33000.0
```

# 提示，然后读取模板

现在我们知道了如何使用 input 函数从用户那里收集输入，我们可以讨论一下*提示符，然后阅读*模板。

让我们从这个模板的伪代码开始:

*显示要求给定值的信息
读取数值*

以下是我们如何实现该模板的几个示例:

```
print("Enter your name: ")
name = input()print("What is your street address? ")
address = input()city = input("What city do you live in? ")state = input("Enter your residence state:")
```

这里还有一个例子:

```
first = input("What is your first name? ")
middle = input("What is your middle name? ")
last = input("What is your last name? ")
fullName = first + " " + middle + " " + last
print("Your full name is", fullName)
```

下面是这个程序运行一次的输出:

```
What is your first name? EdgarWhat is your middle name? AllenWhat is your last name? Poe
Your full name is Edgar Allen Poe
```

# 最后的想法

在请求用户输入之前，没有必要给用户提供提示，但是这样做被认为是好的界面设计。Python 让程序员能够将提示放在输入函数中，从而使实现这个模板变得更加容易。

在我的下一篇文章中，我将讨论*从选项中选择*程序模板。该模板用于根据某些条件或一组条件来决定执行什么代码。

感谢您的阅读，请回复这篇文章或发邮件给我，告诉我您的意见和建议。