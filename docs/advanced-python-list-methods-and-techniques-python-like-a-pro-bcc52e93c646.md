# 高级 Python 列表方法和技术

> 原文：<https://betterprogramming.pub/advanced-python-list-methods-and-techniques-python-like-a-pro-bcc52e93c646>

## Python 中最强大的数据结构之一是列表；以下是一些更高级的列表用法

![](img/29ef57d5f27a5f851775555c14227b79.png)

Python 中最强大的数据结构之一是列表。我并不真的相信这一点，直到我开始研究文档，并意识到在构建列表数据结构时投入了多少工作。

Python 列表本身支持用作队列、堆栈和数组。这就是为什么要像专业人士一样使用 Python，很好地理解列表是很重要的。

在本文中，我们将涵盖列表理解、`zip`方法和`sort`方法。

# 列出理解

理解是 Python 列表的一个高级特性，它有助于使代码更加清晰易读。

合成就是用一行代码在一个列表上执行一系列操作的一种方式。理解通常用括号内的`for`语句来表示。

以下是列表理解的模板:

```
newList = [returned_value  for item in list condition_logic ]
```

## 提取特定元素

列表理解可用于提取满足特定标准的某些元素。在下面的例子中，我们用一个理解从一个列表中取出所有的偶数。

```
# Create a list of numbers from 0 - 49
numRange = range(0,50)# Pull out all the numbers that are even
evenNums = [num for num in numRange if num % 2 == 0 ]
```

在上面的例子中，从左向右阅读，我们用从 for 循环返回的`num`创建一个新列表，其中`num`的余数(`%`模)除以 2 等于零。

这是一个常见的用例，所有偶数都需要从列表中取出。

## 对元素执行操作

列表理解可用于对列表中的元素执行操作。下面的例子展示了如何对列表中的所有元素求平方。

```
# Create a list of numbers from 0 - 49
numRange = range(0,50)# Pull out all the numbers that are even
evenNums = [num * num for num in numRange]
```

## 使用记忆限制函数调用

这是一段特别有用的代码，它可以让你避免不必要的昂贵的函数调用。来源是[这个关于栈溢出的帖子](https://stackoverflow.com/questions/15812779/python-list-comprehension-want-to-avoid-repeated-evaluation)。

记忆化是将值存储在内存中的过程，这样我们就不需要在以后重新计算结果。

情况是这样的:您有一个可能包含重复数据的列表，或者需要运行函数来检查输出并返回值。

这里，记忆化可以通过使用字典来跟踪具有相同输入参数的函数调用的结果。

当你运行上面的例子时，你会发现这个函数只运行了五次，尽管在列表理解中有两次对`f(x)`的调用，并且列表中有六个元素。

每个唯一的号码只能调用一次。否则，提供缓存的值。如果函数调用开销很大，可以通过记忆结果来大大提高代码速度。

这对于合理大小的列表非常有效，可以提高速度，但是对于非常大的列表可能会出现问题，因为当函数在作用域内时，所有的输入/输出都会被缓存，需要大量使用内存来存储值。

# 列出高级方法

除了理解之外，还有其他几种对列表有用的方法。这里有一些可能未被充分利用或不为人知。

## Zip(列表，列表 2，…)

zip 方法用于将 Python 中的多个列表组合成元组。如果两个列表的长度不同，那么两个列表中较长的列表将被截断为较短列表的长度。

```
first_names = ['John', 'Jeff', 'Chris']
last_names = ['Wick', 'Chen', 'Test', 'Truncated']names = zip(first_names, last_names)for name in names:
  print(name)# Outputs: 
('John', 'Wick')
('Jeff', 'Chen')
('Chris', 'Test')
```

## 列表。排序(key=func，reversed=T/F)

我知道。将排序方法放入这样的文章中似乎很奇怪。我把它放在这里，因为我觉得使用自定义排序功能的排序是超级利用不足。

这是我尝试做的一个小排序函数，它将返回给定一天的热门帖子。我确保在最后也使用了列表理解。

这样做的输出将是下面的列表，其中最新和排名最高的文章排在第一位，然后是其他文章和昨天的文章。

这并不能解释为什么昨天的文章表现得足够好，仍然有必要占据榜首，但我认为这一点是有道理的。

```
['Python Like a Pro - Lists and Their Many Uses', 
'Other today post', 
'Yesterdays news']
```