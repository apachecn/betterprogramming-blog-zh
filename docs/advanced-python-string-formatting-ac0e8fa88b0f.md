# Python 中的高级字符串格式

> 原文：<https://betterprogramming.pub/advanced-python-string-formatting-ac0e8fa88b0f>

## 了解 Python 中 format()函数的高级选项。

![](img/4f66e1d97c7cc72599bfdb75be409a10.png)

由[卡尔·海尔达尔](https://unsplash.com/@carlheyerdahl?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，您将了解字符串格式，以及如何创建一个格式良好的变量字符串，无论是使用简单的连接，还是以更专业的方式使用`format()` 函数。这里使用了“变量”一词，因为在很多情况下，您可能需要创建一个带有占位符的字符串，您希望根据可用数据来更改这些占位符。现在不要再拖延了，让我们开始吧。

**目录**

1.  [字符串串联](#36d0)
2.  [format()函数](#44e0)
3.  [带字典和列表的 format()函数](#625c)
4.  [带有类对象的 format()函数](#e2b5)
5.  [带关键字参数的 format()函数](#535f)
6.  [带数字的 format()函数](#0c3d)
7.  [带日期和时间的 format()函数](#6b3d)

# 1.串并置

要用您的数据创建一个字符串，您可以使用字符串连接，如下所示。假设我们有一个代表学生信息的字典，你想用一句话打印这个信息。

```
student = {'name': 'Adam', 'age': 22, 'year': 4}information = 'The student name is ' + student['name'] + ', he is ' + str(student['age']) + ' years old and he is in the ' + str(student['year']) +'th year'print(information)
```

输出:

```
The student name is Adam, he is 22 years old and he is in the 4th year
```

你可以看到，前面的方法不容易阅读，如果你有一个整数，你需要把它转换成字符串。此外，你应该记住空格。因此，你必须用一种更实际的方法，这就是为什么你将学习`format().`

# 2.format()函数

要使用它，你需要做的就是给你的字符串句子加上花括号`**{}**`，然后按照你想要的顺序把信息传递给`format()`函数。

```
student = {'name': 'Adam', 'age': 22, 'year': 4}information = 'The student name is {} , he is {} years old and he is in the {} th year'.format(student['name'], student['age'], student['year'])print(information)
```

输出:

```
The student name is Adam , he is 22 years old and he is in the 4 th year
```

此外，您可以对占位符的顺序进行编号。当您有重复的值时，这很有用。假设学生的电子邮件由`first_name`和`last_name`组成，你想把`email` 和`first_name` 和`last_name`**打印出来，这样你就不需要再传递它们了。**

```
student = {'first_name': 'Adam', 'last_name': 'Smith'}information = 'The student name is {0} {1}, and his email {0}_{1}@email.com'.format(student['first_name'], student['last_name'])print(information)
```

**输出:**

```
The student name is Adam Smith, and his email Adam_Smith@email.com
```

# **3.带有字典和列表的 format()函数**

**您还可以直接访问花括号`**{}**`中的字典字段，然后将字典对象传递给`format()`函数，而不需要在`format()`函数中显式确定字典的键。**

```
student = {'first_name': 'Adam', 'last_name': 'Smith'}information = 'The student name is {0[first_name]} {0[last_name]}, and his email {0[first_name]}_{0[last_name]}@email.com '.format(student)print(information)
```

**输出:**

```
The student name is Adam Smith, and his email Adam_Smith@email.com
```

**同样，你也可以对列表使用同样的方法**

```
student_list = ['Adam', 'Smith']information = 'The student name is {0[0]} {0[1]}, and his email {0[0]}_{0[1]}@email.com'.format(student_list)print(information)
```

**输出:**

```
The student name is Adam Smith, and his email Adam_Smith@email.com
```

# **4.具有类对象的 format()函数**

**假设我们有一个具有这些实例属性的`Student` 类**

*   **学生的姓名**
*   **学生的年龄**
*   **他所在的那一年**

```
class Student:
    def  __init__(self, name, age, year):
        self.name = name
        self.age = age
        self.year = year
```

**现在，让我们从`Student`类创建一个对象，并打印其属性值。**

```
s1 = Student('Adam', 23, 5)information = 'The student name is {0.name}, his age is {0.age} and he is in the {0.year}th year'.format(s1)print(information)
```

**输出:**

```
The student name is Adam, his age is 23 and he is in the 5th year
```

# **5.带有关键字参数的 format()函数**

**您可以将关键字参数传递给花括号`{}`，如下所示。**

```
information = 'The student name is {name}, his age is {age} and he is in the {year}th year'.format(name='Adam', age=22, year=4)print(information)
```

**输出:**

```
The student name is Adam, his age is 22 and he is in the 4th year
```

**此外，您可以解包字典并使用关键字参数。**

```
student = {'name': 'Adam', 'age': 22, 'year': 4}information = 'The student name is {name} , he is {age} years old and he is in the {year}th year'.format(**student)print(information)
```

**输出:**

```
The student name is Adam , he is 22 years old and he is in the 4th year
```

# **6。带数字的 format()函数**

*   ****整数****

**有时，我们的数字没有相同的位数，但我们希望以一种漂亮的方式打印它们，通过添加前导零，所有数字都有相同的位数。我们可以添加一个冒号，后跟花括号中的数字。**

```
numbers = [1, 7, 15, 100]for x in numbers:
    print('the number is {**:03**}'.format(x))
```

**输出:**

```
the number is 001
the number is 007
the number is 015
the number is 100
```

**此外，如果我们有一个很大的数字，您可以使用逗号分隔数字，使其更具可读性。**

```
print('the number is {:,}'.format(5000000000000))
```

**输出:**

```
the number is 5,000,000,000,000
```

*   ****浮动****

**有些浮点数有许多十进制数字，如果您不想打印所有的数字，您可以按如下方式指定十进制数字的位数。**

```
e = 2.7182818284590452353602874713526
print('the number is {:**.3f**}'.format(e))
```

**输出:**

```
the number is 2.718
```

# ****7。带日期和时间的 format()函数****

**`format()`功能还可以帮助您处理日期和时间，并以您想要的方式打印出来。因此，让我们创建一个 DateTime 对象，并以“月日，年—小时:分钟:秒”的方式打印它。**

```
import datetimeobj = datetime.datetime(2022, 4, 5, 11, 25, 17)
information = '{:%B %d,%Y - %H : %M : %S}'.format(obj)print(information)
```

**输出:**

```
April 05,2022 - 11 : 25 : 17
```

**你可以在这里了解更多关于日期和时间格式[中使用的符号。](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-format-codes)**

**就是这样！！现在让我们继续讨论文章摘要。**

**在本文中，您已经了解到:**

*   **如何使用简单的字符串连接创建格式化字符串。**
*   **如何使用`format()`函数创建格式化字符串？**
*   **如何将`format()`函数用于字典、列表、对象、数字以及日期和时间。**