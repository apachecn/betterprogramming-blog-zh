# 从零到英雄 Python 备忘单:使用字典和集合

> 原文：<https://betterprogramming.pub/zero-to-hero-python-cheat-sheet-working-with-dictionaries-and-sets-4af7aaa17598>

## 学习使用 Python 中的集合和字典

![](img/34b2ef9a94ef3ea764de7a5b7e8b4a4b.png)

照片由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在*零到英雄 Python 系列*的前几期文章中，我们介绍了:

1.  [处理原始数据类型，如整数、字符串和布尔值](https://medium.com/better-programming/zero-to-hero-python-cheat-sheet-primitive-data-types-44bd4b29fe95)。
2.  [与控制台交互，了解列表和元组。](https://medium.com/better-programming/zero-to-hero-python-cheat-sheet-working-with-collections-67baefbf5f12)

在这篇文章中，我们将关注 Python 的内置集合，以支持像集合和键值对这样的数学概念。

我们开始吧！

# 使用集合

一个集合用于存储一系列唯一的*值。*

## 初始化

可以使用花括号`{}`来声明一个集合。

```
>>> numSet = {1, 2, 3, 4, 5}
>>> print(numSet)
{1, 2, 3, 4, 5}
```

如果在集合的初始化中放置重复的元素，则只会保留元素的一个实例。

```
>>> numSet = {1, 2, 2, 3, 3, 3, 4, 4, 4, 4, 5}
>>> print(numSet)
{1, 2, 3, 4, 5}
```

您也可以使用内置的`set`函数初始化一个空集。

```
>>> emptySet = set()
>>> print(emptySet)
set()
```

注意:集合中的元素必须是不可变的。向集合中添加可变对象会引发错误。

```
>>> tuple1 = (1, 2, 3)
>>> tuple2 = (4, 5, 6)
>>> tupleSet = {tuple1, tuple2} # no error as tuples are immutable
>>> print(tupleSet)
{(4, 5, 6), (1, 2, 3)}
>>> list1 = [1, 2, 3]
>>> list2 = [4, 5, 6]
>>> listSet = {list1, list2} #will raise error as lists are mutable
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'
```

## 添加元素

使用内置的`add`函数向集合中添加元素。

```
>>> numSet = {1, 2, 3, 4, 5}
>>> numSet.add(6)
>>> print(numSet)
{1, 2, 3, 4, 5, 6}
```

注意:如果试图将重复元素添加到集合中，集合将不会发生变化。在这种情况下，add 函数不会引发任何错误。

```
>>> numSet = {1, 2, 3, 4, 5}
>>> numSet.add(5)
>>> print(numSet)
{1, 2, 3, 4, 5}
```

## 删除元素

使用内置的`remove`功能从集合中删除元素。

```
>>> numSet = {1, 2, 3, 4, 5}
>>> numSet.remove(5)
>>> print(numSet)
{1, 2, 3, 4}
```

注意:试图删除一个不存在的元素会引发一个错误。

```
>>> numSet = {1, 2, 3, 4, 5}
>>> numSet.remove(99)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 99
```

## 长度

使用内置的`len`功能计算一个集合的长度。

```
>>> numSet = {1, 2, 3, 4, 5}
>>> len(numSet)
5
```

## **存在**

使用`in`操作符检查集合中的元素是否存在。

```
>>> numSet = {1, 2, 3, 4, 5}
>>> 2 in numSet
True
>>> 99 in numSet
False
```

现在，让我们来看看执行*设定操作*的情况

## 设置交集

使用`&`运算符找到两个集合的交集。

```
>>> setA = {1, 2, 3, 4, 5}
>>> setB = {3, 4, 5, 6, 7}
>>> intersection = setA & setB
>>> print(intersection)
{3, 4, 5}
```

## 集合联合

使用`|`运算符找到两个集合的交集。

```
>>> setA = {1, 2, 3, 4, 5}
>>> setB = {3, 4, 5, 6, 7}
>>> union = setA | setB
>>> print(union)
{1, 2, 3, 4, 5, 6, 7}
```

## 集合差异

Set difference 返回第一个集合中存在而第二个集合中不存在的一组元素。

使用`-`运算符找出一个集合与其他集合的差异。

```
>>> setA = {1, 2, 3, 4, 5}
>>> setB = {3, 4, 5, 6, 7}
>>> difference = setA - setB
>>> print(difference)
{1, 2}
>>> reverseDifference = setB - setA
>>> print(reverseDifference)
{6, 7}
```

## 设置对称差

对称差分返回恰好出现在两个集合中的一个集合中的一组元素，而不是同时出现在两个集合中。

使用`^`运算符找出两个集合的对称差。

```
>>> setA = {1, 2, 3, 4, 5}
>>> setB = {3, 4, 5, 6, 7}
>>> symmDiff = setA ^ setB
>>> print(symmDiff)
{1, 2, 6, 7}
```

## 检查超集

如果集合`B`中的所有元素都出现在集合`A`中，那么集合`A`就是集合`B`的超集。

使用`>=`操作符检查左侧集合是否是右侧集合的超集。

```
>>> bigSet = {1, 2, 3, 4, 5}
>>> smallSet = {3, 4}
>>> isSuperSet = bigSet >= smallSet
>>> print(isSuperSet)
True
```

要检查右侧集合是否是左侧集合的超集，使用`<=`操作符。

```
>>> bigSet = {1, 2, 3, 4, 5}
>>> smallSet = {3, 4}
>>> isSuperSet = smallSet <= bigSet
>>> print(isSuperSet)
True
```

# 使用字典

在 Python 中，字典用于存储键值对。

## 初始化

字典也用花括号`{}`初始化，键值对用`key:value`语法声明。

```
>>> nameToNumber = {"John" : 1, "Harry" : 2, "Jacob" : 3}
>>> print(nameToNumber)
{'John': 1, 'Harry': 2, 'Jacob': 3}
```

也可以使用内置的`dict`函数初始化一个空字典。

```
>>> emptyDict = dict()
>>> print(emptyDict)
{}
```

空字典也可以通过简单地使用空花括号来初始化。

```
>>> emptyDict = {}
>>> print(emptyDict)
{}
```

注意:字典中的键必须是不可变的。试图创建带有可变键的 dict 将会引发错误。

```
>>> tupleA = (1, 2, 3) # tuples are immutable
>>> stringA = "I love Python!" # strings are immutable
>>> floatA = 3.14 # float values are immutable
>>> dictA = {tupleA : True, stringA : False, floatA : True} # no error as all keys are immutable
>>> print(dictA)
{(1, 2, 3): True, 'I love Python!': False, 3.14: True}
>>> listB = [1, 2, 3] #list is mutable
>>> dictB = {listB : True} # raises an error as lists are mutable
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'
```

## 获取数据

使用方括号(`[]`)从字典中获取一个值。

```
>>> nameToNumber = {"John" : 1, "Harry" : 2, "Jacob" : 3}
>>> JohnsNumber = nameToNumber["John"]
>>> print(JohnsNumber)
1
```

注意:试图获取一个不存在的键将会引发一个错误。

```
>>> nameToNumber = {"John" : 1, "Harry" : 2, "Jacob" : 3}
>>> nameToNumber["Sam"]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'Sam'
```

为了避免这种错误，使用内置的`get`功能。使用`get`函数获取一个不存在的键将返回`None`，但不会抛出任何错误。

```
>>> nameToNumber = {"John" : 1, "Harry" : 2, "Jacob" : 3}
>>> johnsNumber = nameToNumber.get("John")
>>> print(johnsNumber)
1
>>> samsNumber = nameToNumber.get("Sam")
>>> print(samsNumber)
None
```

如果字典中缺少一个键，我们可以使用`get`函数返回一个默认值。将您想要的默认值作为第二个参数传递给`get`函数。

```
>>> nameToNumber = {"John" : 1, "Harry" : 2, "Jacob" : 3}
>>> johnsNumber = nameToNumber.get("John", 99)
>>> print(johnsNumber)
1
>>> samsNumber = nameToNumber.get("Sam", 99)
>>> print(samsNumber)
99
```

## 修改数据

使用内置的`setdefault`功能将数据插入字典。

只有当字典中不存在键时，`setdefault`函数才会在字典中创建一个新的键-值对。如果密钥已经存在，它将*而不是*被覆盖。

```
>>> nameToNumber = {"John" : 1, "Harry" : 2, "Jacob" : 3}
>>> nameToNumber.setdefault("Sam", 4)
4
>>> print(nameToNumber)
{'John': 1, 'Harry': 2, 'Jacob': 3, 'Sam': 4}
>>> nameToNumber.setdefault("Sam", 99) # no changes as the key already exists
4
>>> print(nameToNumber)
{'John': 1, 'Harry': 2, 'Jacob': 3, 'Sam': 4}
```

要修改字典中的现有数据，请使用内置的`update`函数。

```
>>> nameToNumber = {"John" : 1, "Harry" : 2, "Jacob" : 3}
>>> nameToNumber.update({"Sam" : 4}) # creates new entry
>>> print(nameToNumber)
{'John': 1, 'Harry': 2, 'Jacob': 3, 'Sam': 4}
>>> nameToNumber.update({"Sam" : 99}) # updates existing entry
>>> print(nameToNumber)
{'John': 1, 'Harry': 2, 'Jacob': 3, 'Sam': 99}
```

还可以使用方括号语法修改现有数据。

```
>>> nameToNumber = {"John" : 1, "Harry" : 2, "Jacob" : 3}
>>> nameToNumber["Sam"] = 4 # creates new entry
>>> print(nameToNumber)
{'John': 1, 'Harry': 2, 'Jacob': 3, 'Sam': 4}
>>> nameToNumber["Sam"] = 99 # updates existing entry
>>> print(nameToNumber)
{'John': 1, 'Harry': 2, 'Jacob': 3, 'Sam': 99}
```

## 删除数据

使用`del`命令从字典中删除关键字。

```
>>> nameToNumber = {"John" : 1, "Harry" : 2, "Jacob" : 3}
>>> del nameToNumber["John"]
>>> print(nameToNumber)
{'Harry': 2, 'Jacob': 3}
```

注意:试图删除不存在的密钥将导致错误。

```
>>> nameToNumber = {"John" : 1, "Harry" : 2, "Jacob" : 3}
>>> del nameToNumber["Sam"]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'Sam'
```

## **迭代次数**

我们可以使用内置的`keys`函数迭代字典中的键。

```
>>> nameToNumber = {"John" : 1, "Harry" : 2, "Jacob" : 3}
>>> names = list(nameToNumber.keys()) # using list() to store in a list
>>> print(names)
['John', 'Harry', 'Jacob']
```

我们可以使用内置的`values`函数迭代字典中的值。

```
>>> nameToNumber = {"John" : 1, "Harry" : 2, "Jacob" : 3}
>>> values = list(nameToNumber.values())
>>> print(values)
[1, 2, 3]
```

在下一期的*从零到英雄 Python 系列*中，我们将着眼于用 Python 编写函数，以及使用 iterables 处理数据。如有任何问题或建议，请在评论中联系我。