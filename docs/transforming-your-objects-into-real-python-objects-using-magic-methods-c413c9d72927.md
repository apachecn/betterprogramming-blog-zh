# 用神奇的方法给 Python 类增压

> 原文：<https://betterprogramming.pub/transforming-your-objects-into-real-python-objects-using-magic-methods-c413c9d72927>

## 了解常见 Python 语法背后的方法，以及如何让您的对象使用这些语法。

![](img/6e1040b8bc3d1f44b6535d7fe45c3bd3.png)

朱利叶斯·德罗斯特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

也许，你已经注意到有可能使用某些内置方法(例如`len(my_list_or_my_string)`)，以及语法(例如。`my_list_or_my_string[:3]`、`my_fancy_dict['some_key']`上的一些本土类型如`list`、`dict`。也许你很好奇为什么不能在你写的一些类中使用相同的语法。

本机类型的变量(`list`、`dict`、`int`、`str`)具有独特的行为，并对某些语法做出响应，因为它们在各自的类中定义了一些特殊的方法——这些方法被称为魔术方法。

> 神奇的方法只是指以双下划线开始和结束的特殊方法。它们也被称为“邓德”方法。

神奇的方法不是设计来直接调用的，它们是在某个动作上或者遇到特殊语法时从类内部调用的。

在本文中，我们将探索哪些神奇的方法适用于常见的本机类型，以及如何让我们的对象表现出常见本机类型的流行行为。

但是，在深入研究每个原生类型的特殊方法之前，让我们先熟悉一下所有变量共有的基本方法。

# 理解 Python 中的魔法方法

## 实例化函数

`__new__(self, *args, **kwargs)`是实例创建的第一步。它首先被调用，负责**返回**你的类的一个新实例。如果希望控制创建新实例的方式，请使用此方法。

`__init__(self, *args, **kwargs)`每当我们想要初始化一个新的实例时，这个类方法就会被调用。这样， ***就在通过`__new__`方法创建了*** 实例之后被调用。Python 中的`__init__`方法不返回任何东西。

有时调用一个对象就像调用函数一样是可能的。这是通过`__call__`方法实现的。

## 演示功能

如果没有内置函数的帮助，对象将是非常匿名的，内置函数可以帮助识别它们并以一种不仅有意义而且在开发和测试期间有用的方式格式化它们。

`__dir__(self)`当用户想知道一个对象的属性时，他们可能会调用该对象的`dir(...)`函数。然后解释器调用该对象的`__dir__`方法。

`__len__(self)`适用于包含多个元素的对象。由此我们可以调用对象上的方法`len(my_object)`。

`__repr__(self)`为了调试和开发，当解释器想要明确地识别一个对象时，这个方法被调用。这个值通常包含在尖括号`< . . . >`中。

目标是让一个对象可读。如果该方法在类中与`__repr__`一起使用，则`__str__`方法的值优先。

`__sizeof__(self)`帮助识别对象的字节大小。

## 等号

当解释器遇到两个变量之间的等号时，这些函数被调用。其中包括:

`__eq__(self, x)`当我们比较两个变量的值是否相等时，解释器触发这个方法得到一个结果。

`__gt__(self, x)`直观上，这代表“大于”,当大于算术符号`>`在两个变量之间时调用。它通常用于两个数值变量之间，但也可用于比较其他类型的变量。

`__ge__(self, x)`这代表“大于或等于”,当大于或等于算术符号`>=`在两个变量之间时调用。

`__lt__(self, x)`直观上，这代表“小于”,当大于算术符号`<`在两个变量之间时调用。

`__le__(self, x)`这代表“小于或等于”,当大于或等于算术符号`<=`在两个变量之间时调用。

# 本机类型的神奇方法

## 数字

数字最明显的特征是它们的算术运算。通过简单地实现下面列举的一两个特殊方法，我们可以让我们的对象表现出与数字相同的行为，并像数字一样响应算术符号。

当两个变量之间出现“+”号时，Python 解释器调用该方法。

`__mul__(self, x)`对于乘法算术运算，这个特殊函数由解释器隐式调用。

`__sub__(self, x)`为了给我们的对象以从中减去的能力，我们可以加上这个方法。

**真实世界的例子#1:一个更加 Pythonic 化的温度类** 首先，让我们定义一个简单的类来保存特定房间的温度。

![](img/95842bb96c015764917da0e85b3dddc6.png)

照片由[马泰奥·富斯科](https://unsplash.com/@matteofusco?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

当我们试图向我们的温度类的实例添加一个值时，我们得到一个错误。

```
>>> Temperature(30, 'C') + 1TypeError: unsupported operand type(s) for +: 'Temperature' and 'int'
```

现在，让我们扩展这个类，通过实现`__add__`方法赋予它执行算术运算的能力。

现在，让我们看看在我们的温度类中添加一个自定义的`__add__`方法后的结果。

```
>>> Temperature(30, 'C') + 6
<Temperature: 36°C>>>> Temperature(30, 'C') + Temperature(35.6, 'F')
<Temperature: 38°C>>>> Temperature(34, 'C') + Temperature(66, 'C')
<Temperature: 100°C>
```

## **Iterables:列表、元组、字符串、集合**

`__contains__(self, x)`当`in`关键字被用来检查一个元素是否存在于一个 iterable 中时(例如:`x in ["a", "b"]`)

`__delitem__(self, key)`当通过`del`关键字从 iterable 中删除一个项目时，该方法被触发(例如`del my_dict[x]`)

`__getitem__(self, key)`当访问器语法用于变量时，该方法由解释器调用(例如:`my_variable[x]`

`__iter__(self)`在生成器表达式中使用`iter`方法，一次生成数组中的一项。

`__next__(self)`可以和`__iter__`一起使用，使任何对象可迭代。一旦`__iter__`和`__next__`对出现在任何类中，对象将响应`for i in my_object`语法。

这个方法被解释器用来在任何给定的位置设置元素。你会注意到`str`和`tuples`不支持项目赋值，因为它们是**不可变的**。

## 现实生活中的例子#2:拼字游戏的更 Pythonic 化的方法

让我们通过对一个简化的拼字游戏的概念化，将我们在这篇文章中学到的东西都变成现实。我们的拼字游戏将利用一些神奇的方法，使其以更友好的方式与解释者互动。

![](img/9a1620ef1e5bece8ff61ebb458b9331b.png)

照片由[布雷特·乔丹](https://unsplash.com/@brett_jordan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

没有字母牌就没有拼字游戏可玩。拼字游戏包含字母表中的一个字母和一个数字，该数字表示玩家通过在一个回合中玩它而得分。在拼字游戏中，玩家通常会在方块之间进行比较，以选出得分最高的方块，所以让我们在设计中支持这一点。

然后可以像这样在游戏中声明和使用瓷砖:

```
tile_a = Tile('A', 1)
tile_q = Tile('Q', 10)print(tile_a)
# A (1)print(tile_q)
# Q (10)print(tile_q > tile_a)
# True
```

**单元格** 接下来，我们将定义拼字板上的方块。我们知道棋盘上有 255 个方格。这些单元中的一些已经向玩它们的玩家奖励特殊点数。我们的细胞将保存以下数据:`row`、`column`、`letter_multiplier`、`word_multiplier`。因为我们在整个游戏中也有一个单元格实例，所以我们应该使它成为一个单独的，以防止在棋盘上多次创建一个单元格。

**董事会** 最后，我们将定义我们的董事会。棋盘上有游戏开始以来的所有棋步。我们将需要一个完整的游戏会话板，**我们将使这成为一个** [**单例**](https://en.wikipedia.org/wiki/Singleton_pattern) **类。**

**移动
为了在代码中表示这一点，我们可以保存一个瓷砖数组和播放瓷砖的单元格。玩家还不断权衡潜在的动作，以确定哪一个动作能给他们最高分。**

现在，当一个玩家想要比较两个移动来看哪一个更有利时，我们可以优雅地比较移动，就像我们对自然数字、字符串等所做的那样。

```
move_one = Move([
    [tile_a, cell_1], [tile_c, cell_2], [tile_e, cell3]
])
print(move_one)
# ACE = 5move_two = Move([
    [tile_z, cell_1], [tile_o, cell_2], [tile_o, cell3]
])
print(move_two)
# ZOO = 12print(move_one > move_two)
# Falseprint(move_one < move_two)
# True
```

# 结论

如果大型 Python 项目的大部分组件接受并响应我们对原生类型习以为常的大量语法——我们在 Django ORM 中看到了这一点，能够迭代一个`QueryDict`,那么使用大型 Python 项目肯定会更加直观。

```
**Want to Connect?**[Get weekly articles and coding tricks on only the topics you care about by subscribing to my channel.](https://tomisin.dev/subscribe)
```