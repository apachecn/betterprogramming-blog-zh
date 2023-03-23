# 掌握 Python 中列表理解的 9 件事

> 原文：<https://betterprogramming.pub/9-things-to-know-to-master-list-comprehensions-in-python-8bc0411ec2ed>

## 本教程将帮助你学习 Python 中列表理解的最常见用法

![](img/274bdf5a3ef7ea38a155b7abcd703c8e.png)

由[罗曼·辛克维奇](https://unsplash.com/@synkevych?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/list?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

越来越多的人开始学习 Python，因为它已经成为几乎所有领域最受欢迎的编程语言之一，比如 web 开发、科学计算，当然还有人工智能。

无论你在哪里使用 Python，你都不可避免地要学习 Python 的数据结构、变量和函数声明、条件语句、控制流和其他基本概念。

让许多初学者感到困惑的一个重要的“Pythonic”特性是*列表理解* — 一种创建列表的简洁方式。

以前听说过，但不知道是什么？

很好，这篇文章将为您在 Python 学习冒险中掌握 list comprehensions 提供一个良好的开端。

为了便于组织，我列出了我们应该知道的关于列表理解的九件事，包括它的语法和各种用例。

# 1.基本语法

最基本的列表理解具有以下语法。

如前所述，它是做某些事情的简洁方式，比如创建列表。扩展形式通常表示为一个`for`循环，其中`iterable`的每一项都运行表达式中指定的某些操作。

```
# list comprehension
[expression for item in iterable]# expanded form
for item in iterable:
    expression
```

# 2.创建列表

毫不奇怪，最流行的用法是简洁地创建一个列表。

假设我们不知道列表的理解，那么在创建列表时，我们可能会做如下的事情。为此，首先，我们将声明一个空列表。第二，在`for`循环中，我们将每个条目添加到列表中。

正如在基本语法一节中提到的，我们可以将`for`循环语句“压缩”成一行——使用 list comprehension，只需一行代码，我们就可以通过迭代原始列表方便地创建一个列表。

```
>>> pets = ('bird', 'snake', 'dog', 'turtle', 'cat', 'hamster')
>>> uppercased_pets = [pet.upper() for pet in pets]
>>> uppercased_pets
['BIRD', 'SNAKE', 'DOG', 'TURTLE', 'CAT', 'HAMSTER']
```

# 3.用于过滤的条件语句

有时，当我们使用列表理解来创建一个列表时，我们不想包含现有列表中的所有项目。

在这种情况下，我们需要一个条件语句来过滤掉现有列表中不符合特定标准的项目。相应的列表理解具有以下语法。

这里有一个这种用法的例子。

```
>>> primes = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
>>> squared_primes = [x*x for x in primes if x%10 == 3]
>>> squared_primes
[9, 169, 529]
```

如果我们有一个更复杂的条件评估，我们甚至可以使用一个函数。

# 4.有条件赋值

有时，我们不想从原始列表中筛选出项目。

相反，我们希望评估条件，以确定使用哪个表达式。下面给出了语法及其用法。下面也解释了语法。

## 5.替换地图()

在某些情况下，你可能见过人们使用`map()`来创建列表。具体来说，`map()`函数具有以下语法及其基本用法示例。

需要注意的一点是，`map()`函数返回一个`iterable`对象，因此我们可以使用`list()`函数从这个`iterable`生成一个列表。

如前所示，我们可以用列表理解替换`map()`函数。

```
>>> pets = ('bird', 'snake', 'dog', 'turtle', 'cat', 'hamster')
>>> uppercased_pets = [pet.upper() for pet in pets]
>>> uppercased_pets
['BIRD', 'SNAKE', 'DOG', 'TURTLE', 'CAT', 'HAMSTER']
```

# 6.嵌套列表理解

假设我们在下面的代码片段中有一个`tuple`，我们想要创建一个新的项目列表，这些项目是`tuple`中所有数字的平方。

在这种情况下，我们可以使用嵌套列表理解，其语法也如下所示。

尽管从技术上来说，嵌套列表理解可以有多个层次，但是为了可读性，不建议超过两个层次。

# 7.使用 Walrus 运算符

Python 3.8 中的一个新特性是引入了 walrus 运算符(`:=`)，该运算符用于赋值表达式。

假设我们想从一个字母列表中抽取 10 次，我们正在创建的列表将只包括这些图中的元音。下面是我们如何在列表理解中使用 walrus 操作符来实现它。

具体来说，在下面的例子中，我们评估从字母中随机抽取的一个字母是否是元音，如果是，它将被分配给 list comprehension 表达式可以访问的字母。

# 8.集合理解

虽然 list comprehension 是很多人都知道的，但是我们在创建集合的时候也可以使用 comprehension。基本语法及其用法如下所示。

一个主要的区别是我们用花括号代替方括号。当然，根据设计，一个集合中的元素不会有重复，这与允许重复的列表相反。

请注意，我们也可以在集合理解中使用条件语句。

# 9.词典理解

我们有列表和集合理解，你不会惊讶 Python 也有字典理解。下面的代码片段显示了基本语法及其用法。

# 结论

本文回顾了列表理解的基本语法及其在各种场景中的使用。

除了列表理解，我们还讨论了集合和字典理解。这些理解允许我们用 Python 非常方便地创建这些基本的集合数据类型，可读性更好。