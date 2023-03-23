# 集合和数据结构的 10 个小 Python 习惯用法

> 原文：<https://betterprogramming.pub/10-tiny-python-idioms-for-collections-and-data-structures-2f0d2923832>

## 以最 Pythonic 化的方式使用容器

![](img/d5d08fa4f4ce367d19778c461690bba2.png)

由[杰西卡·鲁斯切洛](https://unsplash.com/@jruscello?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/collection?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

每项任务都可以用几种不同的方法来解决。这是编程的基本假设之一。虽然有些解决方案可能会变得太复杂，但其他解决方案可能非常简单。

这种想法应该可以引导你学习 Python 语言。对我来说，它允许开发人员创建每行都有意义的最紧凑的程序。

在处理列表、字典、元组等容器时，这一点变得尤为明显。您需要一个命令，它同时应用于每个元素，并为整个集合提供修改。

你不应该为每一个元素而烦恼，而是一次为整个数据集而烦恼。这对数据科学家来说尤其重要。

所以，我来介绍一些 Python 的习惯用法，作为这些词的论据。

# 字典中的最大值

让我们测试几种不同的方法。

仅最大值:

```
max(some_dict.values())
```

最大值的关键:

```
max(some_dict, key **=** new_dict.get)
```

他们两个:

```
max(some_dict.items(), key **=** **lambda** x: x[1])
```

更通用的方法:

```
**import** operator
max(some_dict.items(), key **=** operator.itemgetter(1))
```

# 获取几个字典的元素

这将有助于从多个来源创建独特元素的列表:

# 从列表中获取唯一元素

我最喜欢的习语和最短的一个。

创建列表:

```
new_list **=** [random.randint(0,9) **for** el **in** range(1,1001)]
```

和类型:

```
**set**(new_list)Out[1]: {0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
```

# 列表中的重复次数

让我们采用与前一个命令相同的列表。纯蟒道:

```
{el : new_list.count(el) **for** el **in** set(new_list)}Out[1]: {0: 76, 1: 92, 2: 103, 3: 113, 4: 85, 5: 112, 6: 109, 7: 116, 8: 93, 9: 101}
```

更通用的方法(输出相同):

```
**from** itertools **import** groupby{key : len(list(group)) **for** key, group **in** groupby(sorted(new_list))}
```

最短的一个(输出也是相同的):

```
**from** collections **import** Counter
dict(Counter(new_list))
```

# 反转字符串(或列表)

这是所有 Python 程序中被过度使用的最著名的习语:

```
new_str **=** "Abcd efgh ijk lmn opq rstu v w xyz"
new_str[::**-**1]Out[1]: 'zyx w v utsr qpo nml kji hgfe dcbA'
```

# 序列最长的 Zip

最有用的 Python 内置函数之一(`zip`)接受几个列表(或其他可迭代对象)并通过成对匹配遍历它们。尽管当最短列表结束时该功能停止。

幸运的是，还有另一个函数可以迭代，直到最长的序列结束。所有缺失的值都用`None`填充。

# 清单的快速副本

当然，Python 有一个专门的模块，可以在几乎每个数据结构中与方法 copy 一起复制。虽然有一种制作列表、字符串或其他可迭代对象的副本的最有效的方法——切片:

```
list_a **=** [1,2,3,4,5]
list_a_copy **=** list_a**[:]**
```

# 对整个列表进行快速操作

我们可以找到一个非常有趣的替代方法来代替需要遍历每个元素的循环。Python 允许下一个技巧:

```
list(map(**lambda** x: x******2, list_a))
```

我们已经在一行代码中对每个列表元素应用了一个操作。甚至，我们可以对几个序列重复操作:

```
list(map(**lambda** a, b: a**+**b, list_a, list_b))
```

返回成对总和的列表。

# 空值过滤

假设我们有一个稀疏列表，里面有很多零。或者更像是一个数据科学的例子:有很多缺失值的列。我们应该如何获得真实的值？下一个习语就是为此而生的。

# 转置和列提取

一个简单的数学运算——转置矩阵——可能需要几行代码和一对循环。尽管 Python 几乎对所有事情都采用一句话的方法:

此外，如果我们需要矩阵的单个列:

```
col1, col2, _ **=** zip(*****table)
col2Out[1]: (2, 5, 8, 0)
```

# 结论

Python 充满了单短语习惯用法，在其他编程语言中，这些习惯用法可能采用几种代码结构。我喜欢紧凑整洁的代码，这就是我有一天开始学习 Python 的原因。

您可以在我的 GitHub 中找到整个工作笔记本:

[](https://github.com/Midvel/medium_jupyter_notes/blob/master/python_tiny_tips/containers-tips.ipynb) [## 中级/中等 _jupyter_notes

### permalink dissolve GitHub 是 4000 多万开发人员的家园，他们一起工作来托管和审查代码，管理…

github.com](https://github.com/Midvel/medium_jupyter_notes/blob/master/python_tiny_tips/containers-tips.ipynb) 

分享你最喜欢的 Python 习惯用法或库中的工具，它们可以最小化你的代码。