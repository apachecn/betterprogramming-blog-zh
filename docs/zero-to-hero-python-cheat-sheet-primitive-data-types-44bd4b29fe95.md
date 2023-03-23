# 从零到英雄 Python 备忘单:原始数据类型

> 原文：<https://betterprogramming.pub/zero-to-hero-python-cheat-sheet-primitive-data-types-44bd4b29fe95>

![](img/34b2ef9a94ef3ea764de7a5b7e8b4a4b.png)

照片由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

无论你是数据科学家还是工程师，还是刚开始编程，使用 Python 都是非常有趣的。

然而，学习和记忆语法是很难的，有时并不是本能的。查找最常见的用例会很方便。它让我们专注于手头的任务，而不是浪费时间查找语法。

这是零到英雄 Python 系列的第一篇帖子。接下来的帖子包括

1.  [使用控制台，以及列表和元组](https://medium.com/p/67baefbf5f12)。
2.  [使用集合和字典](https://medium.com/post/4af7aaa17598)。

*我们开始吧！*

# 使用数字

## 基本算术

对于基本算术运算，使用基本数学语法:

```
>>> 100
100
>>> 10 + 20
30
>>> 20 - 10
10
>>> 10 * 20
200
>>> 20 / 10
2.0
```

**注:**除法运算的输出总是`float`。

## 整数除法

整数除法去除小数位并提供整数响应。用`//`表示。

```
>>> 100 // 11
9
>>> 100.0 // 11
9.0
```

## 系数

模数运算符提供整数除法的余数。用`%`来表示。

```
>>> 100 % 11
1
>>> 100.0 % 11
1.0
```

## 倡导者

指数运算符用于计算某个数字的其他幂。用`**`表示。

```
>>> 4 ** 2
16
>>> 11 ** 3
1331
```

# 使用字符串

可以使用双引号(`"`)或单引号(`'`)来创建字符串。

```
>>> "I love Python!"
'I love Python!'
>>> 'I love Python!'
'I love Python!'
```

## 串联

和数字一样，使用`+`操作符。

```
>>> x = "Strings" + "Together"
>>> x
'StringsTogether'
```

## 字符串数组

字符串也是一个字符数组，可以这样处理。使用索引来访问字符。

```
>>> str = "I love Python!"
>>> ch = str[0]
>>> ch
'I'
```

## 长度

使用`len`函数查找字符串的长度:

```
>>> str = "I love Python!"
>>> len(str)
14
```

## 字符串格式

使用花括号`{}`和`format`函数从格式和模板中创建字符串。

```
>>> template = "{} love {}"
>>> template.format("I", "Python")
'I love Python'
```

要在模板的多个位置重用值，请使用索引。

```
>>> template = "{0} love {1}. {2} also loves {1}."
>>> template.format("I", "Python", "John")
'I love Python. John also loves Python.'
```

如果不想使用太多的索引，也可以使用命名参数。

```
>>> template = "{person1} loves {thing}. {person2} also loves {thing}."
>>> template.format(person1="John", person2="Harry", thing="Python")
'John loves Python. Harry also loves Python.'
```

如果您的代码中已经有了变量，您可以使用格式化字符串直接在模板中引用它们，也称为 f 字符串。用`f`启动模板即可。

```
>>> name = "John"
>>> str = f"{name} loves Python"
>>> str
'John loves Python'
```

# 使用布尔值

在 Python 中，布尔值可以作为文字`True`和`False`使用。

```
>>> b = True
>>> b
True
>>> b = False
>>> b
False
```

## 否认

布尔变量可以使用`not`关键字翻转。

```
>>> not True
False
>>> not False
True
>>> b = True
>>> not b
False
>>> b = False
>>> not b
True
```

## 布尔运算 And 和 Or

分别使用关键字`and`和`or`进行布尔运算。

```
>>> True and False
False
>>> True or False
True
>>> a = True
>>> b = False
>>> a and b
False
>>> a or b
True
```

## 算术运算

在算术运算符中，`True`被视为`1`，而`False`被视为`0`。

```
>>> True + False
1
>>> True + True
2
>>> False + True
1
>>> True + 2
3
>>> False - 2
-2
>>> True * 10
10
>>> False * 7
0
```

## 铅字铸造

可以使用`bool`函数将数字类型转换为布尔值。`0`评估为`False`，所有其他数字评估为`True`。

```
>>> bool(0)
False
>>> bool(1)
True
>>> bool(3.14)
True
>>> bool(-3.14)
True
>>> bool(0.0)
False
```

使用布尔运算符`and`和`or`自动将数字类型转换为布尔类型。

```
>>> 5 or 0 #equivalent to True or False
5
>>> 5 and 0 #equivalent to True and False
0
```

空字符串类型转换为`False`，而非空字符串类型转换为`True`。

```
>>> bool("")
False
>>> bool("I love Python")
True
```

## 评估布尔值

使用`==`操作符可以检查是否相等。

```
>>> 5 == 5
True
>>> 5 == -5
False
```

所有的不等式检查都有与其含义相对应的语法。

```
>>> 5 < 4
False
>>> 5 > 4
True
>>> 5 <= 4
False
>>> 5 >= 4
True
```

简单的不等式(不等于)可以使用`!=`来检查。

```
>>> 5 != 5
False
>>> 5 != 4
True
```

# **无工作**

`None`关键字用于表示一个空对象。

```
>>> x = None #Variable defined and set as None
>>> x # No error but no result either as x is None
>>> y # We see error as y is undefined, which is different from None
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'y' is not defined
```

`None`转换为布尔值时，计算结果为`False`。

```
>>> bool(None)
False
```

本文是 Python 备忘单系列的一部分。接下来的部分将涵盖[变量和集合](https://medium.com/p/67baefbf5f12)，以及[使用字典和集合](https://medium.com/post/4af7aaa17598)。