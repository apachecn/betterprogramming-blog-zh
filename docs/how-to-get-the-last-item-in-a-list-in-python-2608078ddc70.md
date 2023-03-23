# 如何在 Python 中获取列表中的最后一项

> 原文：<https://betterprogramming.pub/how-to-get-the-last-item-in-a-list-in-python-2608078ddc70>

## 使用切片符号并学习保护您的应用程序

![](img/b1124e79e13510aec46b7dbda0382850.png)

杰西卡·刘易斯在 [Unsplash](https://unsplash.com/s/photos/list?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在 Python 中，列表是有序的、可变的值容器。如果这听起来太沉重，它类似于其他编程语言中的数组。

检索列表中的最后一项是一项相对简单的任务，但却是演示一些核心 [Python](https://www.python.org/) 概念的绝佳练习。在本教程中，我们将学习以下内容:

*   什么是可重复项和索引？
*   什么是切片符号？
*   什么是`IndexError`？

如果不想继续读下去，使用`-1`作为索引来获取数组中的最后一项。对于其他人，享受解释！

# 什么是可重复项和索引？

最简单地说，iterable 是一个可以迭代的值。追溯单词 *iterate* 的词根，iterable 可以为每个单独的元素执行相同的值。

列表是可迭代的，每一项都是独立的元素。字符串也是可重复的，每个字符都是独立的元素。

索引用于跟踪你在一个 iterable 中的位置。指数是从 0 开始的整数。下面是打印列表中第二个元素以及一个字符串的例子。

```
numbers = [100,200,300,400]
name = "Jonathan"print(numbers[1]) # 200
print(name[1]) # o
```

# 什么是切片符号？

如果您见过包含冒号的复杂索引的 Python iterables，您可能会看到切片符号。

`slice()`函数——实际上是一个返回 slice 对象的构造函数——用于从给定的 iterable 中创建一个子集。切片标记是执行切片的一种速记技术。符号写在 iterable 的方括号内，语法如下`[start,stop,step]`。

下面举几个简单的例子让你熟悉一下(第一个是文章题目的答案):

```
numbers = [100,200,300,400]# start at index 1, step forward one at a time
print(numbers[1:]) # [200, 300, 400]# start at index 0, step forward one at a time and stop at index 2
print(numbers[0:2]) # [100, 200]# step backwards every two items
print(numbers[::-2]) # [400, 200]# step backwards one at a time and end at index 1
print(numbers[:1:-1]) # [400, 300]# step backwards one at a time
print(numbers[::-1]) # [400, 300, 200, 100]
```

如您所见，这三个位置都是可选的，允许您灵活地遍历 iterable 的子部分。

# 什么是索引错误？

如果你确定你的 iterable 不会为空，那么这就不是问题。但是——这是一个很大的但是——如果你的 iterable 有可能是空的，那么你需要相应地为出现`IndexError`的可能性做好计划。

`IndexError`是当试图访问一个不存在的索引时抛出的异常。

```
scores = []print(scores[-1]) # IndexError: list index out of range
```

如果没有计划好，那么`IndexError`会暂停你的应用程序。

为了解决这个问题，使用一个`try/except`语句来优雅地处理这个异常。

```
scores = []try:
   print(scores[-1])
except IndexError:
   print("Index does not exist")
```

使用`try/except`语句，您可以添加多个异常并定制每个异常的处理方式。