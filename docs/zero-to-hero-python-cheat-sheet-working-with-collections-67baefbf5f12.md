# 从零到英雄 Python 备忘单:使用集合

> 原文：<https://betterprogramming.pub/zero-to-hero-python-cheat-sheet-working-with-collections-67baefbf5f12>

## 了解如何与控制台、列表和元组进行交互

![](img/34b2ef9a94ef3ea764de7a5b7e8b4a4b.png)

照片由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在[之前的 Zero to Hero Python 备忘单](https://medium.com/@arindamroy/zero-to-hero-python-cheat-sheet-primitive-data-types-44bd4b29fe95)中，我们介绍了如何处理原始数据类型，如整数、字符串和布尔值。在这篇文章中，我们将会看到与控制台的交互，并学习列表和元组。

下一篇文章将介绍使用字典的[和设置](https://medium.com/post/4af7aaa17598)。

*让我们开始吧！*

# 使用控制台

## 从控制台读取数据

使用内置的`input`函数从控制台接受参数。`input`接受一个字符串参数，这是打印到控制台要求输入的消息。

```
>>> str = input("Input your string : ")
Input your string : I love Python!
>>> str
'I love Python!'
```

*注意:* `input`*函数只从控制台读取字符串值。使用类型转换将它们转换为所需的数据类型。*

## 将数据写入控制台

使用内置的`print`功能将数据写入控制台:

```
>>> print("I love Python!")
I love Python!
>>> str = "I love Python variables too!"
>>> print(str)
I love Python variables too!
```

默认情况下，打印功能会在末尾打印一个`newline`:

```
>>> print("Line 1"); print("line 2");
Line 1
line 2
```

要改变这种行为，将`end`参数传递给`print`函数:

```
>>> print("I love Python", end="!"); print("No newline here.")
I love Python!No newline here.
```

# 使用列表

## 初始化

列表用于存储数据序列。列表中的元素用逗号分隔，写在方括号内(`[]`)。

以下是前五个整数的列表:

```
>>> numbers = [1, 2, 3, 4, 5]
>>> print(numbers)
[1, 2, 3, 4, 5]
```

您也可以初始化一个空列表:

```
>>> emptyList = []
>>> print(emptyList)
[]
```

## 添加元素

使用内置的`append`函数向列表添加元素:

```
>>> numbers = [1, 2, 3, 4, 5]
>>> numbers.append(6)
>>> print(numbers)
[1, 2, 3, 4, 5, 6]
```

要将一个元素插入特定的索引，使用内置的`insert`函数。`insert`取两个参数，分别是 index 和 element:

```
>>> numbers = [1, 2, 3, 4, 5]
>>> numbers.insert(2, 99)
>>> print(numbers)
[1, 2, 99, 3, 4, 5]
```

## 获取元素

可以使用索引来访问元素，类似于数组:

```
>>> numbers = [1, 2, 3, 4, 5]
>>> x = numbers[0]
>>> y = numbers[4]
>>> print(x,y)
1 5
```

如果您试图访问列表中不存在的索引，您将得到一个`IndexError`:

```
>>> numbers = [1, 2, 3, 4, 5]
>>> x = numbers[9]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range
```

## 获取一系列元素

可以使用`:`语法从列表中提取一系列元素。

```
>>> numbers = [1, 2, 3, 4, 5]
>>> lessNumbers = numbers[1:3]
>>> print(lessNumbers)
[2, 3]
```

**注:*第一个参数包含*，第二个参数*不包含。***

如果只希望元素达到某个索引，则可以忽略第一个参数。同样，如果只需要某个索引中的元素，则可以忽略第二个参数。

```
>>> numbers = [10, 20, 30, 40, 50]
>>> firstThree = numbers[:3]
>>> print(firstThree)
[10, 20, 30]
>>> lastThree = numbers[2:]
>>> print(lastThree)
[30, 40, 50]
```

要从列表中获取第 n 个*元素，请使用`::n`:*

```
>>> numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> thirdNumbers = numbers[::3]
>>> print(thirdNumbers)
[1, 4, 7, 10]
```

要从第*个*元素开始获取第 n 个*元素，请使用`m::n`:*

```
>>> numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> evenNumbers = numbers[1::2]
>>> print(evenNumbers)
[2, 4, 6, 8, 10]
```

## 删除元素

要从特定索引的列表中移除元素，请使用内置的`del`命令:

```
>>> numbers = [1, 2, 3, 4, 5]
>>> del numbers[2] #deletes element at index 2
>>> print(numbers)
[1, 2, 4, 5]
```

要从您知道其值的列表中删除特定元素，请使用内置的`remove`函数:

```
>>> numbers = [1, 2, 3, 4, 5]
>>> numbers.remove(3) #deletes the element with value=3
>>> print(numbers)
[1, 2, 4, 5]
```

注:`remove`从列表中删除该值的*第一个出现的*:

```
>>> numbers = [1, 2, 1, 2]
>>> numbers.remove(2) # removes first occurence of 2 at index=1
>>> print(numbers)
[1, 1, 2]
```

## 反转

要反转列表，请使用`::-1`:

```
>>> numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> reversed = numbers[::-1]
>>> print(reversed)
[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
```

## 串联

要将两个列表加入新列表，请使用`+`操作符:

```
>>> n1 = [1, 2, 3]
>>> n2 = [4, 5, 6]
>>> sum = n1 + n2
>>> print(sum)
[1, 2, 3, 4, 5, 6]
```

要将一个列表附加到另一个列表，请使用内置的`extend`功能:

```
>>> bigList = [1, 2, 3]
>>> smallList =[4, 5, 6]
>>> bigList.extend(smallList)
>>> print(bigList)
[1, 2, 3, 4, 5, 6]
```

## 长度

要查找列表的长度，请使用内置的`len`功能:

```
>>> numbers = [1, 2, 3, 4, 5]
>>> len(numbers)
5
```

## 存在

要检查列表中是否存在某个元素，请使用`in`命令:

```
>>> numbers = [1, 2, 3, 4, 5]
>>> 1 in numbers
True
>>> 10 in numbers
False
```

# 使用元组

元组就像列表一样持有数据序列。列表和元组的关键区别在于*元组是不可变的。*这意味着一旦创建，就不能修改元组。

要创建元组，请在方括号(`()`)中创建以逗号分隔的元素列表。

```
>>> numbers = (1, 2, 3, 4, 5)
>>> print(numbers)
(1, 2, 3, 4, 5)
```

## 正在获取元素

元素可以使用索引来访问，类似于数组。

```
>>> numbers = (1, 2, 3, 4, 5)
>>> numbers[0]
1
```

## 获取元素范围

从一个元组中获取一系列元素的所有语法都是*类似于上面的列表*的语法。

## 更改元组

任何更改元组的尝试都会引发`TypeError`:

```
>>> numbers = (1, 2, 3, 4, 5)
>>> numbers[2] = 99
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
```

## 长度

使用内置的`len`函数查找元组的长度:

```
>>> numbers = (1, 2, 3, 4, 5)
>>> len(numbers)
5
```

## 拆包

使用以逗号分隔的变量将元组解包为其元素:

```
>>> numbers = (100, 200, 300)
>>> x, y, z = numbers
>>> print("x=%d, y=%d, z=%d" %(x, y, z))
x=100, y=200, z=300
```

通过在包含多个元素的变量前使用`*`，也可以非对称地进行解包。这些变量存储为列表:

```
>>> numbers = (1, 2, 3, 4, 5, 6)
>>> first, second, *remaining = numbers
>>> print(first)
1
>>> print(second)
2
>>> print(remaining)
[3, 4, 5, 6]
```

*在下一版的 Zero to Hero Python 系列中，我们将查看* [*字典和集合，这是一种在 Python 中存储键值映射的方法*](https://medium.com/post/4af7aaa17598) *。如有任何问题或建议，请在评论中与我联系。*