# Python 的有什么区别。追加和。扩展列表方法？

> 原文：<https://betterprogramming.pub/whats-the-difference-between-python-s-append-and-extend-list-methods-65e9bca792f0>

## 他们之间的对比是戏剧性的

![](img/e8f711e0e1826c043c696a9d61f6ead6.png)

由 [David Clode](https://unsplash.com/@davidclode?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/monty-python?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

列表是 Python 中一种可变的有序数据类型，它包含多个元素。列表数据类型有两个方法`.append()`和`.extend()`，这两个方法都采用单个参数，并将数据添加到现有列表的末尾。那么有什么区别呢？

# 。append()方法

在`.append`方法中提供的参数被视为一个*元素*，它可以被认为是一个单独的项目。这意味着您可以预期列表的长度会比追加之前的长度长一。

```
l = [1,2,3]
l.append("Hello"))
# [1,2,3,"Hello"]l = [1,2,3]
l.append(4)
# [1,2,3,4]l = [1,2,3]
l.append([4,5,6])
# [1,2,3,[4,5,6]]l = [1,2,3]
l.append({"a":4,"b":5,"c":6})
# [1,2,3,{"a":4,"b":5,"c":6}]
```

在上面的每个例子中，初始列表有三个条目，追加后列表有四个条目。请注意，追加列表或字典会将该项嵌套在下一个可用的索引中。

# 。extend()方法

在`.extend`方法中提供的参数被视为一个 *iterable* ，它可以被视为一个项目集合。这意味着您可以预期列表的长度会比 *n* 项长，其中 *n* 是给定参数的迭代次数。

```
l = [1,2,3]
l.extend("Hello")
# [1, 2, 3, 'H', 'e', 'l', 'l', 'o']l = [1,2,3]
l.extend(4)
# TypeError: 'int' object is not iterablel = [1,2,3]
l.extend([4,5,6])
# [1, 2, 3, 4, 5, 6]l = [1,2,3]
l.extend({"a":4,"b":5,"c":6})
# [1, 2, 3, 'a', 'b', 'c']
```

我们在这里看到，结果与使用`.append`截然不同。扩展字符串时，每个字符都存储为自己的项。由于数值是不可迭代的，当试图用值`4`扩展时，我们会收到一个 TypeError。用另一个列表扩展有效地合并了两个列表。最后，当使用字典进行扩展时，请注意存储的是字典的键，而不是值。