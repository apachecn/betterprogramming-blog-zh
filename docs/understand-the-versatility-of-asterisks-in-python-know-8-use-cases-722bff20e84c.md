# 了解 Python 中星号的多功能性——了解 8 种用例

> 原文：<https://betterprogramming.pub/understand-the-versatility-of-asterisks-in-python-know-8-use-cases-722bff20e84c>

## 复习星号的基本用法和高级用法

![](img/2812cb14f18b3c5ed3c7d43c1577a377.png)

由[迈克尔·基尔科因](https://unsplash.com/@mikekilcoyne?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 Python 编程中，我们大多使用字母和数字，但不会使用太多符号。最常见的符号是运算符，包括加法(+)、减法(-)、乘法(*)和除法(/)。在这些运算符中，星号的用法比其他运算符多。在本文中，我想回顾一下除了作为乘法运算符之外的这些用法。

# 1.权力运营商

第一种用法是作为幂运算符。不使用内置的`pow`函数，可以简单地使用`**`来计算取幂。

```
>>> pow(5, 3)
125
>>> 5**3
125
```

# 2.捕获所有可迭代的解包

也被称为*明星表情*。当我们处理一个 iterable 时，比如一个 tuple，我们可以通过创建多个变量来解包这个 tuple 以检索各个条目，如下所示。

```
data = (1, 2, 3, 4)
a, b, c, d = data
# *a=1, b=2, c=3, d=4*
```

但是，也可以使用带星号的表达式(带有*前缀的变量名)来捕获解包中的多个项目。

```
a, *b = data
*# a=1, b=[2, 3, 4]*
```

在带星号的表达式中，有三点需要注意。

1.  **拆包中只能有一个带星号的表达式。**因为它旨在捕获所有未被其他变量考虑的项目，所以使用多个带星号的表达式将无法知道哪些项目与哪些变量相关。
2.  **带星号的表达式将创建一个列表对象**，尽管我们从一个元组对象开始。
3.  **带星号的表达式不仅可以和元组一起使用**，我们还可以将这种技术应用于其他可迭代对象(例如，列表、字符串)。

# 3.解包 Iterables 以创建变量

在前面的示例中，星号出现在赋值语句的左侧，用于创建新的 list 对象。但是，它也可以出现在赋值语句的右侧。在这种情况下，它解包现有的 iterable。

当您使用共享项和其他不同的项创建多个 iterables 时，这种技术非常有用。考虑下面的例子。我们首先创建一个由共享项组成的列表对象，并在其他列表对象中解包该列表对象以检索共享项。

```
shared_items = [1, 2, 3]

items1 = [*shared_items, 4, 5, 6]
items2 = [-3, -2, -1, *shared_items]
```

虽然我们可以使用列表连接来连接列表，但我个人认为这种方法可读性更好。更重要的是，它可以与任何 iterable 一起工作，而列表连接只在列表之间工作。观察下面的效果。

```
>>> shared_numbers = range(3)
>>> [*shared_numbers, 3, 4, 5]
[0, 1, 2, 3, 4, 5]
>>> shared_numbers + [3, 4, 5]
Traceback (most recent call last):
  File "<input>", line 1, in <module>
TypeError: unsupported operand type(s) for +: 'range' and 'list'
```

# 4.不同数量的位置参数

定义函数时，有两种类型的参数:位置参数和关键字参数。关键字参数是由标识符指定的，位置参数是由它们的位置指定的。在大多数情况下，我们定义了一定数量的位置参数。然而，我们可以定义带有不同数量位置参数的函数——带星号。解释前请看下面一个琐碎的例子。

```
def rounded_sum(*numbers):
    total = 0
    for number in numbers:
        total += round(number)
    print(**f"Received** {numbers} **-> rounded sum:** {total}**"**)
    return total
```

*   `rounded_sum`函数只有一个参数`*numbers`，这表示当您调用这个函数时，您可以设置任意数量的位置参数，如下所示。

```
>>> rounded_sum(2.4, 3.7, 4.8)
Received (2.4, 3.7, 4.8) -> rounded sum: 11
11
```

*   您会注意到函数将不同数量的参数作为一个 tuple 对象进行处理。在例子中，我们用三个数字调用函数，当我们打印`numbers`时，它是由这三个数字组成的元组对象。

虽然这里没有详细介绍，但是应该注意，位置参数的可变数量应该在其他位置参数之后。否则，Python 无法判断哪个位置变量与哪个参数对应。

# 5.打开字典

星号的另一个常见用法是打开字典。通过使用现有的字典，它通常用在赋值语句的右侧。假设我们有以下字典作为开始。

```
science_scores = {**"math"**: 90, **"physics"**: 95, **"chemistry"**: 92}
art_scores = {**"english"**: 93, **"spanish"**: 94}
```

与解包列表、元组或其他可重复项不同，解包字典需要使用两个星号。如下所示，我们通过解包现有的字典来创建一个新的字典对象。

```
>>> combined_scores = {**science_scores, **art_scores}
>>> print(combined_scores)
{'math': 90, 'physics': 95, 'chemistry': 92, 'english': 93, 'spanish': 94}
```

上述操作也可以称为字典的合并/更新。还有一些其他的技术，你可以在我之前的[文章](/dictionary-merging-and-updating-in-python-3-9-4ac67c667ce)中找到，我在这里不展开这个话题。

# 6.不同数量的关键字参数

除了不同数量的位置参数，还可以在 Python 函数中定义不同数量的关键字参数。惯例是用`**kwargs`来表示这样的特征。当你看到一个使用`**kwargs`的函数签名时，意味着你可以设置任意数量的关键字参数。当然，这些论点必须是有效的；否则，该功能无法正确使用它们。

在两种常见情况下，您可能希望在函数定义中使用`**kwargs`。

首先，你不知道函数将采用什么关键字参数。在这种情况下，使用`**kwargs`允许这样的灵活性。例如，有人可以使用下面的 API 函数，允许用户向服务器发送任何关键字参数。

```
def send_info_to_server(**kwargs):
    print(**f"Send the info:** {kwargs}**"**)
    *# do something to prepare the information*
```

这里有几个调用这个函数的例子。如您所见，我们可以传递任何关键字参数，这些参数在函数中被处理为一个`dict`对象。换句话说，当您编写自己的涉及`**kwargs`的函数时，您将它们作为字典对象来处理。

```
>>> send_info_to_server(postId="abc", userId="user")
Send the info: {'postId': 'abc', 'userId': 'user'}
>>> send_info_to_server(like=True, status="success")
Send the info: {'like': True, 'status': 'success'}
```

第二，通过将关键字参数“打包”为`**kwargs`，可以使函数定义更加清晰。对于这些关键字参数，理想情况下它们应该有已知的默认值，您可以在函数体中指定这些值。如果一些参数没有合适的默认值，最好明确地列出它们，因为让你的函数签名清晰比使用`**kwargs`让你的函数签名看起来干净更重要。

# 7.定义仅关键字参数

星号在函数定义中的另一个重要用途是创建只有关键字的参数。顾名思义，这些参数只能使用关键字来指定。通常，参数通过关键字或位置进行解析。(这里在参数解析方面有太多的技术细节，感兴趣的用户可以参考[官方 PEP](https://www.python.org/dev/peps/pep-3102/) 进行更详细的讨论。).

让我们考虑下面的例子来说明关键字参数可以按位置设置。正如你所看到的，`multiply(5, 4)`没有使用第二个参数的关键字。

```
def multiply(number, multiplier=1):
    return number * multiplier

multiply(5, 4)
multiply(5, multiplier=4)
```

然而，为了明确起见，我们可以用星号实现仅关键字参数特性，如下所示。

```
def explicit_multiply(number, *, multiplier=1):
    return number * multiplier
```

在上面的函数中，我们用`*`表示星号后面的所有参数都只能设置为关键字参数。这样，我们通过强制使用已定义的关键字来提高代码的可读性。有了这个更新的函数，通过设置参数的位置来调用这个函数就不起作用了。见下文。

```
>>> explicit_multiply(5, multiplier=4)
20
>>> explicit_multiply(5, 4)
Traceback (most recent call last):
  File "<input>", line 1, in <module>
TypeError: explicit_multiply() takes 1 positional argument but 2 were given
```

# 8.什么都导入(不要用！)

许多人犯的另一个常见错误是通过使用*作为通配符从模块中导入所有内容。你可能以前在什么地方见过这个。

```
from module_abc import *
from module_xyz import *
```

虽然看起来你不需要输入特定的组件(例如，函数和类)，但是它有两个问题。1)不清楚您从这些模块中使用了什么。读者会很困惑。2)它可能会有显著的副作用，其中许多副作用会导致 bug。例如，两个模块可能共享一些碰巧具有相同名称的函数。当所有内容都被导入时，您正在使用的函数可能与您想要使用的函数不同。因此，为了避免这些问题，最好的做法是显式地指定导入的项目，如下所示。

```
from module_abc import fun_a, ClassA
from module_xyz import fun_x, ClassY
```

# 结论

在本文中，我们回顾了 Python 中星号的八种常见用法。有些是与他人相关的，而有些人似乎是独立的。然而，他们有一个共同点:参与*。当你在学习一门新的编程语言时，找到在某些方面有联系的东西通常是一个好主意，这样你可以强化相关的技术并更好地了解它们。