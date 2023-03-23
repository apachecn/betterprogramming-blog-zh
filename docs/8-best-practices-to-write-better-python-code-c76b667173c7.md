# 编写更好的 Python 代码的 8 个最佳实践

> 原文：<https://betterprogramming.pub/8-best-practices-to-write-better-python-code-c76b667173c7>

## 编写更好、更高效的 Python 代码

![](img/5f60d769bd66c6fbfad5456fac48cc33.png)

[Jukan Tateisi](https://unsplash.com/@tateisimikito?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

就语法而言，Python 是一种非常容易和简单的编程语言。与其他编程语言相比，用 Python 编写代码非常简单。但是有一些最佳实践可以让你写出更好更高效的 Python 代码。

当谈到 Python 脚本和开发时，编写高效的 Python 代码来节省时间和计算机内存就变得很有必要。

这些最佳实践将帮助您在 Python 开发之旅中实现目标。我们开始吧！

# 1.列表理解的使用

List comprehension 允许您在一行代码中基于现有列表的元素创建一个新列表。

当创建一个新的列表时，列表理解通常比普通的循环和函数更加紧凑和快速。

语法:`newlist = [ expression for *item* in *iterable* (if conditional) ]`

```
Output:Even numbers are: [0, 2, 4, 6, 8]
Squared numbers are: [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

# 2.用 f 字符串格式化字符串

在 Python 3.6 中引入的`f-strings`是一种格式化字符串的新方法。与其他格式化字符串的方法相比，`f-strings`可读性更强，速度更快，并且不容易出错。

使用`f-strings`来格式化一个字符串是非常容易的。你必须在引号前以`f`或`F`开始一个字符串。在字符串内部，您必须在`{}`之间编写一个 Python 表达式，该表达式可以引用初始化的变量。

```
Output:Hello World! My name is John and my age is 18
```

是的，它们使用起来非常简单。

# 3.使用集合。计数器()

`Counter()`用于统计 iterable 中某项出现的频率。`Counter()`函数将列表、元组或字符串等可迭代对象作为输入，然后返回该可迭代对象中每一项的计数作为输出。

这个`Counter()`函数是 dictionary 类的子类。在这个函数的帮助下，我们可以在不实现字典代码的情况下，以更快的方式计算迭代器中元素的频率。

语法:`Counter(iterable)`

```
Output:Counter({'B': 5, 'A': 3, 'C': 2})
Counter({'o': 4, ' ': 4, 'l': 3, 'H': 2, 'e': 2, 'w': 2, 'r': 2, 'd': 1, '!': 1, 'a': 1, 'y': 1, 'u': 1, '?': 1})
```

# 4.用 Sorted()对复杂的可重复项进行排序

如果您需要对一些 iterable 进行排序，比如列表、元组或字典，您不需要自己实现任何排序算法。您可以使用内置函数`sorted()`以任何顺序(升序或降序)对任何 iterable 进行排序。

它返回一个排序列表作为输出。

语法:`sorted(iterable, key=None, reverse=False)`

```
Output:Sorted list: [2, 3, 6, 11, 15, 33, 64, 99]
Sorted dictionary list: [{'name': 'Lisa', 'Age': 17}, {'name': 'Joy', 'Age': 19}, {'name': 'Jack', 'Age': 21}]
```

# 5.在字典中使用 get()方法

在 Python 字典中，`get()`方法用于检索具有指定键的项的值。

`get()`方法的优点是，如果带有指定键的值不存在，它不会抛出任何错误。相反，它返回默认值(`None`)或任何其他指定的默认值。

语法:`Dict.get(key, default=None)`

```
Output:Brand: Ford
Type: Not Found
```

# 6.使用解包操作符合并字典

我们可以使用`(**)`操作符在一行中合并多个字典。我们需要用`(**)`操作符在`{}`中传递字典。

语法:`{**dict1, **dict2}`

```
Output:Merged dictionary: {'name': 'Joy', 'age': 25, 'city': 'New York'}
```

我们可以这样传多个字典:`{**dict1, **dict2, **dict3}`。

# 7.使用 enumerate()函数

当处理迭代器时，我们经常需要计算迭代次数。Python 为我们提供了内置的`enumerate()`函数来简化这项任务。

您可以使用`enumerate()`函数同时从 iterable 中获取计数器和值，而不是自己创建和增加变量的计数。

语法:`enumerate(iterable, start=0)`

```
Output:0 apple
1 orange
2 banana
[(1, 'one'), (2, 'two'), (3, 'three'), (4, 'four')]
```

# 8.用集合存储唯一值

当您需要在迭代器中存储唯一元素时，可用的最佳选项是`Sets`。

`Sets`用于存储唯一值。集合相对于其他迭代器的优势在于，集合平均可以在 *O(1)* 中查找/插入/删除，因为它们是作为哈希表实现的。

语法:`set(iterator)`

```
list1 = [1,2,2,3,3,3,4,4,5,5,5,6]
my_set = set(list1)
print(my_set)Output:
{1, 2, 3, 4, 5, 6}
```

# 结论

本文到此为止。我们已经讨论了八个最佳实践，您可以使用它们来节省时间，并使您的代码在 Python 中看起来更整洁。

如果您将 Python 用于竞争性编程和开发目的，那么这些技巧对于更快地运行代码非常有用。

感谢阅读！