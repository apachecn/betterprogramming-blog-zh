# 使用 Python 列表的 10 种有效方法

> 原文：<https://betterprogramming.pub/10-efficient-ways-to-use-python-lists-f6e7e666708>

## 按值复制、最常出现、展平列表等等

![](img/0185a1156740482fb80f41ad599473b0.png)

Glenn Carstens-Peters 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

当您处理许多相关值时，Python 列表是一种很好的数据结构。它们是可变的、有序的元素序列，可以容纳不同种类的元素。

开始使用列表真的很容易。它们被写在方括号中，如下所示:

```
brands = ['McD', 'KFC', 'Nike', 'Apple', 'Google']
```

让我们快速总结一下 Python 列表上的一些基本操作:

*   您可以通过指定索引(切片符号)来访问一个元素或一系列元素。
*   `in`关键字用于确定一个元素是否存在于列表中。
*   `remove()`方法移除指定的元素，而`pop()`方法返回并移除给定索引处的元素(如果没有指定索引，则移除最后一个元素)。
*   `*`操作符可用于乘法列表。它按照我们指定的数量复制列表。

Python 列表被广泛使用，所以高效地处理它们是至关重要的。在接下来的几节中，我们将探索 Python 列表的几个用例，并确保我们有效地使用它们。

# 按值复制列表

复制列表有很多方法，但使用赋值操作符不是其中之一。让我们确认一下:

```
>>> a = [1, 2, 3, 4, 5]
>>> b = a>>> id(a)
4345924656>>> id(b)
4345924656
```

这个赋值只是创建了一个对列表`a`的引用。这意味着两个列表现在指向同一个内存，一个列表中的任何变化都会影响另一个。

以下是创建 Python 列表的独立“浅层”副本的一些可能方法，按照速度从最高效率到最低效率排序:

*   `b = [*a]`
*   `b = a * 1`
*   `b = a[:]`
*   `b = a.copy()` (Python 3 —浅层复制)
*   `b = [x for x in a]`
*   `b = copy.copy(a)` (Python 2)

虽然速度上的差异是可比较的，但有时执行`deepcopy`(这显然是最慢且最需要内存的方法)是不可避免的。

与深层拷贝不同，浅层拷贝不会克隆嵌套对象。相反，它只是复制嵌套对象的引用。让我们看下面的例子来验证这一点:

```
>>> a = [[0,1],[2,3]]
>>> b = [*a]
>>> a[1][0] = 5**#Output of b: [[0, 1], [5, 3]]**
```

更新嵌套列表元素`a[1][0] = 5`也会改变列表`b`。在不使用 1D 列表的情况下，下面的方法最适合对所有列表元素进行深层复制:

*   `b = [x[:] for x in a]`
*   `b = copy.deepcopy(a)`(导入`copy`库)

# 列表中出现的次数

我们可以使用 list comprehension、`filter()`或`count()`找到一个元素出现的次数。虽然列表理解使得编写优雅的代码变得容易，但是在目前的情况下，它们是效率最低的。最高效的是内置方法`count()`:

使用计数器库，我们可以检索每个元素出现的次数。它是一个无序的集合容器，包含表示键的元素，它们的计数以值的形式设置。

```
>>> from collections import Counter
>>> a = ['apple', ‘orange’, ‘orange’, 'grape', 'apple']
>>> Counter(a)#Output: Counter({'apple': 2, 'orange': 2, 'grape': 1})
```

# 列表中最常见的元素

以下是查找列表中最常见元素的一种方法:

```
x = [1,2,2,3,2,1,1]
common_element = max(set(x), key=x.count)
```

虽然上面的实现看起来聪明、简洁、优雅，但是使用`count`方法会导致额外的循环开销和明显的 *O( n2 )* 时间。

通过使用我们刚刚看到的`Counter`库，找到最常见(或第二常见)的元素要快得多。以下代码片段展示了我们如何实现这一点:

# 将文件解析到列表

将文件解析为 Python 列表是一个非常常见的用例，虽然下面的代码看起来不错，但效率很低:

```
with open('filename.txt', 'r') as f:     
   lines = [line.split() for line in f.readlines()]
```

`readlines()`一次将整个文件放入内存，并解析成一个列表。现在，这可能不会对小文件造成太大的麻烦，但是随着文件大小的增加，程序需要一段时间才能启动。

幸运的是，您并不真的需要读取文件来解析它。默认情况下，文件是可迭代的，因此下面的代码片段将为您完成这项工作，以一种有效的方式将每一行放入嵌套列表中:

```
a = [i.split() for i in open(‘filename.txt’)]
```

`split()`函数帮助我们去掉每个嵌套列表元素中烦人的换行符。

# 在字典列表中搜索

搜索包含在列表中的字典的较慢方法是使用`Filter + Lamda`:

```
next(filter(lambda obj: obj.get('name') == 'Anupam', dicts), None)#Time Elapsed: 0.001047
```

现在，利用列表理解不仅简洁，而且节省了函数调用开销，因此速度更快:

```
next((item for item in dicts if item["name"] == "Anupam"), None)#Time Elapsed: 0.0009327
```

# 列表的合并

列表的连接或合并不等同于追加。一个`append`函数将一个元素添加到列表中，从而使计数增加 1，如下所示:

```
x = [1, 2, 3]
x.append([4, 5])#Output: [1,2,3,[4,5]]
```

另一方面，`extend`通过迭代它的参数，将集合中的每个元素添加到列表中。值得注意的是，传递单个元素，如整数，在`extend`函数中不起作用。

```
x = [1, 2, 3]
y = [4,5]
x.extend(y)#Output of x: [1,2,3,4,5]x = [1,2,3]
x.extend(4)# TypeError: 'int' object is not iterable
```

使用`extend`函数或使用`+=`操作符可以正确地连接或合并列表。

## 扩展 vs. +=

就速度而言，`extend`和`+=`之间只有微小的差别，虽然它们看起来可能是一样的，但实际上并不是这样。例如，不能对非局部变量使用`+=`。此外，与`extend`不同，`+=`不支持链式函数调用(`getListA() += listB`会抛出语法错误)。

```
a = [1, 2]
b = [2, 3]
b.extend(a)#Time elapsed: 0.000189 #Second wayb += a
#Time elapased: 0.0002098
```

## Splat 运算符

splat 操作符有助于解包和连接列表，同时还允许我们在其中添加文字元素。

```
a = [1,2]
b = [2,3]
a = [*a, *b]#Output of a: [1, 2, 2, 3]
#Time elapsed : 0.00021719
```

# 并行迭代多个列表

为了并行遍历列表，我们将利用`zip`操作符。`zip`操作符通过将每个列表中相同索引处的元素组合在一起，返回元组的迭代器。

如果列表的长度不同，当最短的列表完成时，zip 操作符停止。要获得元组列表，请使用`list(zip(a, b))`。以下代码显示了一个并行无错迭代多个列表的示例:

# 展平列表列表

在展平列表列表的各种可能方法中，让我们看看三种流行的方法，并在`timeit`中比较它们在 500 次迭代中的效率。

## 方法 1:使用列表理解——慢

```
l = [[1, 2, None], [3, 4], [5, 6]][item for items in l for item in items]#Time is 0.0009621297940611839
```

## 方法 2:使用链式迭代——更快

```
import itertools
list(itertools.chain.from_iterable(l)) #Time is 0.0007339292205870152
```

## 方法 3:使用 functools —最快

使用`Functools (reduce-iconcat)`是使列表变平的最快方法(尽管不太快),尽管它需要导入一些库:

```
import functools
import operatorl = functools.reduce(operator.iconcat, l, [])# Output: [1, 2, None, 3, 4, 5, 6]
#Time is 0.0006934418343007565
```

# 列表中的排列和组合

Python 提供了`itertools`包中的内置方法来生成序列的排列和组合。让我们看看如何从 Python 列表中创建它们。

## 排列

它产生 *n！*输入序列长度的排列 *n* 。我们也可以显式传递一个自定义参数来生成不同长度的序列。

```
import itertools 
print(list(itertools.permutations([1,2,3])))#Output: [(1, 2, 3), (1, 3, 2), (2, 1, 3), (2, 3, 1), (3, 1, 2), (3, 2, 1)]
```

## 组合

就像我们轻松通过的七年级数学概念一样，这需要一个输入`r`并生成所有可能的元组组合。

```
import itertools
a = [1,2,3,4]
list(itertools.combinations(a, r=2)) #Output: [(1, 2), (1, 3), (1, 4), (2, 3), (2, 4), (3, 4)]
```

# 字符串到列表，列表到字符串

要从一个字符串创建一个列表，我们只需要使用列表理解来迭代它，如下面的代码所示:

```
a = [_ for _ in 'abcdefghi']
```

相反，我们将使用一个`join()`方法来获取 iterable 的每个元素，并将其连接到字符串中:

```
b = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i']
x = ', '.join(mylist)#Output x is:'a, b, c, d, e, f, g, h, i'
```

# 结论

这篇文章总结了 Python 列表的基本语法和一些常见用例，同时强调了不同方法的效率。

感谢阅读。