# Python 中的*args 和**kwargs 是什么？

> 原文：<https://betterprogramming.pub/what-are-args-and-kwargs-in-python-6aaf9e3cad73>

## 学习使用可变长度的参数列表

![](img/702385e762cd457a21e0aac9003bffaa.png)

由[车头时距](https://unsplash.com/@headwayio?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[挡泥板](https://unsplash.com/s/photos/argument?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上拍摄

功能就是生命，对吧？如果您是 Python 的新手——无论是全新的编码还是来自另一种语言——您将了解到函数定义中的参数数量与要传递的参数数量相匹配。

这是基础——它有助于理解这个世界。然而，一旦你在一个函数定义中看到`*args`或`**kwargs`，它也为你设置了早期的心理障碍。

不要让语法吓到你。这些都不是超级特殊的参数。它们甚至没有那么花哨，我们将学习如何使用它们。

# 位置参数与关键字参数

为了了解什么是`*args`和`**kwargs`，我们需要区分两个概念。

首先是位置参数和关键字参数之间的区别。在最基本的函数中，我们玩一个匹配游戏——参数 1 和参数 1 一起使用，参数 2 和参数 2 一起使用，以此类推。

```
def printThese(a,b,c):
   print(a, "is stored in a")
   print(b, "is stored in b")
   print(c, "is stored in c")printThese(1,2,3)
"""
1 is stored in a
2 is stored in b
3 is stored in c
"""
```

这三个参数都是必需的。缺少一个将导致错误。

```
def printThese(a,b,c):
   print(a, "is stored in a")
   print(b, "is stored in b")
   print(c, "is stored in c")printThese(1,2)
"""
TypeError: printThese() missing 1 required positional argument: 'c'
"""
```

如果我们在函数定义中给一个参数一个默认值，那么它就变成可选的了。

```
def printThese(a,b,c=None):
   print(a, "is stored in a")
   print(b, "is stored in b")
   print(c, "is stored in c")printThese(1,2)
"""
1 is stored in a
2 is stored in b
None is stored in c
"""
```

此外，这些可选参数也可以用关键字限定，这意味着您可以在函数调用中指定参数名来相应地映射它。

让我们将所有三个变量都默认为`None`，看看我们如何仍然可以映射它们，而不管它们的顺序如何。

```
def printThese(a=None,b=None,c=None):
   print(a, "is stored in a")
   print(b, "is stored in b")
   print(c, "is stored in c")printThese(c=3, a=1)
"""
def printThese(a=None,b=None,c=None):
   print(a, "is stored in a")
   print(b, "is stored in b")
   print(c, "is stored in c")printThese(c=3, a=1)
"""
1 is stored in a
None is stored in b
3 is stored in c
"""
```

# Splat 运算符

首先，我想说我喜欢这个操作符的名字——它太…直观了。`*`通常与乘法相关联，但是在 Python 中，它还兼作 splat 操作符。

我认为这个操作者是一个 piata。我已经将 spread 操作符——相当于 splat 的 JavaScript 描述为拆开一系列多米诺骨牌以形成一个更大的序列，但是 splat 需要一个更有力的类比。

一个简单的例子将使这一点更加清楚。

```
a = [1,2,3]
b = [*a,4,5,6]print(b) # [1,2,3,4,5,6]
```

在代码示例中，我们获取`a`的内容并将其分散(解包)到新列表`b`中。

# 如何使用*args 和**kwargs

所以我们知道 splat 操作符解包多个值，并且有两种类型的函数参数。好吧，如果你到现在还没想明白，`*args`是参数的简称，`**kwargs`是关键字参数的简称。

每一个都用于解包它们各自的参数类型，允许使用可变长度的参数列表进行函数调用。例如，让我们创建一个函数来打印学生的考试成绩。

```
def printScores(student, *scores):
   print(f"Student Name: {student}")
   for score in scores:
      print(score)printScores("Jonathan",100, 95, 88, 92, 99)
"""
Student Name: Jonathan
100
95
88
92
99
"""
```

喔，等等。我没给它起名`*args`？没错，“args”是一个标准约定，但仍然只是一个名称。在`*args`中，这个秘密被揭示了，单个星号是真正的玩家，它创建了一个列表，其内容是函数调用中的位置参数——在那些被定义的参数之后。

说完后，`**kwargs`应该是个容易消化的东西。名称并不重要，但是双星号创建了一个字典，它的内容是来自函数调用的关键字参数(在那些定义之后)。

为了说明这一点，让我们创建一个函数来打印一个人的宠物的名字。

```
def printPetNames(owner, **pets):
   print(f"Owner Name: {owner}")
   for pet,name in pets.items():
      print(f"{pet}: {name}")printPetNames("Jonathan", dog="Brock", fish=["Larry", "Curly", "Moe"], turtle="Shelldon")"""
Owner Name: Jonathan
dog: Brock
fish: ['Larry', 'Curly', 'Moe']
turtle: Shelldon
"""
```

# 离别赠言

一些有助于你避免常见陷阱和扩大知识面的智慧之言。

*   使用`*args,` `**kwargs`作为捕捉位置和关键字参数的标准约定
*   不能将`**kwargs`放在`*args`之前，否则会出现错误
*   当心关键字参数和`**kwargs`之间的冲突，这里的值应该作为`**kwarg`传递，但却不知道是关键字参数的名称
*   您也可以在函数调用中使用 splat 运算符