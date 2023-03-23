# 提高 Python 性能的 5 个优化技巧

> 原文：<https://betterprogramming.pub/5-optimization-tips-for-better-python-performance-f1f9b1d63c13>

## 将代码的效率提升到一个新的水平

![](img/c5e8589d894da389f6d6790454bd6353.png)

戴勒

Python 作为一种编程语言在最近十年变得很出名。这是一个优秀的工具，不仅有助于后端开发，也有助于数据科学和自动化。

这种简单的语言允许任何人开始学习。Python 有一个简单明了的语法，这对构建软件很有帮助。

然而，这里有很多陷阱，低效的代码会显著降低性能。

好消息是可以避免这些问题。Python 提供了一些关于如何提高代码速度的建议。那些是什么？我们来探索一下。

# 采用内置函数

Python 开发人员试图在最常见的操作上表现最佳。他们已经实现了覆盖这些操作的函数。例如，我们有`min()`、`max()`、`sum()`和许多更方便的功能。

它们是内置的，无需任何额外的导入即可使用。它们表现出良好的性能。

所以，每次需要对数组中的所有元素求和时，考虑使用内置函数`sum()`而不是实现自己的算法。

# 利用集合进行高效搜索

集合是另一种经常被遗忘的数据结构。软件工程师积极使用列表和字典，但很少使用集合。

然而，在寻找集合中的独特元素或快速检查包含性时，set 表现出了极高的性能。

```
collection = [x for x in range(100_000)]
exists = 1000 in collection
>> Duration: 0.000015020

collection = set(x for x in range(100_000))
exists = 1000 in collection
>> Duration: 0.000001907
```

在上面的例子中，我们看到检查一个元素在集合中的存在比在列表中快 7 倍。

# 使用枚举而不是范围来访问索引

如果需要遍历集合并建立索引，最好的方法是使用`enumerate`。

该选项比循环通过`range`更快。此外，我们不需要通过索引来访问条目，因为`enumerate`为我们做了这些。

```
collection = range(0, 1_000_000)
for i in range(len(collection)):
  print(collection[i])
>> Duration: 0.114128828

collection = range(0, 1_000_000)
for i, number in enumerate(collection):
  print(number)
>> Duration: 0.062491894
```

我们看到，在这个例子中，使用`enumerate`的情况比使用`range`的情况几乎快两倍。这是因为`enumerate`内部生成了一个特殊的枚举对象。它在一次运行中有效地返回项目和索引。

# 应用列表理解来创建列表

列表理解是 Python 中一个广泛使用的特性。遍历列表和转换元素只能在一行中完成。

但是额外的一点是它有不错的性能。我们来对比一下常规的`for`循环。

```
collection = range(0, 1_000_000)
newlist = []
for item in collection:
    newlist.append(item)
>> Duration: 0.065476894

collection = range(0, 1_000_000)
newlist = [item for item in collection]
>> Duration: 0.024003267
```

这个例子证明了列表理解速度快了三倍。

初学者通常会纠结于这个特性的语法，尤其是当有几个嵌套层的时候。但是一旦他们掌握了它，他们就会到处应用它。

# 使用 Join 代替“+”来连接字符串

当我们考虑字符串连接时，首先想到的是操作符`+`。因为直观。当第一弦是`'hello,'`第二弦是`'world'`时。要组合它们，我们需要编写`'hello,' + 'world'`。

字符串连接方式问题是性能。在每一次`+`操作中，都会创建一个新的字符串。以这种方式组合两个字符串没什么大不了的。但是当涉及到更大数量的字符串时，您会看到性能下降。

连接字符串的有效方法是使用`join()`函数。

```
strings = ["Python", "is", "amazing", "when", "you", "follow", "pro", "tips", "and", "advice"]
string_concat = ""
for s in strings:
    string_concat += s
>> Duration: 0.000010014

strings = ["Python", "is", "amazing", "when", "you", "follow", "pro", "tips", "and", "advice"]
string_concat = "".join(strings)
>> Duration: 0.000002861
```

这种性能已经在少数琴弦上显现出来。但是随着要连接的字符串数量的增加，这种差异会变得更大。

# 结论

可以提高 Python 代码性能的列表还会增加。有些建议可能只属于特定的场景。

我们在本文中介绍的技巧被认为是 Python 中的最佳实践。您可能不会注意到小数据集上的任何差异。但是，当您需要处理的数据量足够大时，您会看到显著的性能提升。

```
**Want to become a better software developer?**

Do you want to grow professionally as a software engineer?
Are you curious about how to achieve the next level in your career? 

My book "[Unlock the Code](https://www.amazon.com/dp/B098LNMFCJ/)" offers a comprehensive list of steps to boost 
your professional life.
```