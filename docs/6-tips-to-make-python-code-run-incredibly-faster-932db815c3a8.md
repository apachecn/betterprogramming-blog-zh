# 让 Python 代码运行得更快的 6 个技巧

> 原文：<https://betterprogramming.pub/6-tips-to-make-python-code-run-incredibly-faster-932db815c3a8>

## 加速您的 Python 代码

![](img/9c044a212127564c51b50fbf9fdd8f3c.png)

[布鲁诺·纳西门托](https://unsplash.com/@bruno_nascimento?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

Python 是开发人员中最流行的编程语言之一。无论是在 web 开发还是机器学习中，它的应用无处不在。

它受欢迎的原因有很多，比如它的社区支持，它令人惊叹的库，它在机器学习和大数据中的广泛使用，以及它简单的语法。

尽管有这么多的优点，Python 有一个缺点，就是它的速度慢。作为一种解释型语言，Python 比其他编程语言要慢。不过，我们可以使用一些技巧来克服这个问题。

在这篇文章中，我将分享一些 Python 技巧，使用它们我们可以使我们的 Python 代码比平时运行得更快。我们开始吧！

# 合适的算法和数据结构

每个数据结构对运行时都有显著的影响。Python 中有很多内置的数据结构，比如列表、元组、集合和字典。大多数人在所有情况下都使用列表数据结构。

在 Python 中，集合和字典的查找性能为 O(1 ),因为它们使用哈希表。在下列情况下，您可以使用集合和字典来代替列表:

*   集合中没有重复的项目。
*   您需要在集合中重复搜索项目。
*   该集合包含大量项目。

您可以在这里看到 Python 中不同数据结构的时间复杂度:

 [## 时间复杂性- Python Wiki

### 本页记录了当前 CPython 中各种操作的时间复杂度(也称为“Big O”或“Big Oh”)。其他…

wiki.python.org](https://wiki.python.org/moin/TimeComplexity) 

# 使用内置函数和库

Python 的内置函数是加速代码的最佳方式之一。需要时，您必须使用内置的 Python 函数。这些内置功能都经过了很好的测试和优化。

这些内置函数之所以快，是因为 Python 的内置函数，比如`min`、`max`、`all`、`map`等。是用 C 语言实现的。

您应该使用这些内置函数，而不是编写手动函数，因为这将帮助您更快地执行代码。

示例:

```
newlist = []for word in wordlist:
    newlist.append(word.upper())
```

编写这段代码的更好方法是:

```
newlist = list(map(str.upper, wordlist))
```

这里我们使用的是内置的`map`函数，它是用 c 写的，因此，它比使用循环要快得多。

# 使用多重分配

如果您想要分配多个变量的值，那么不要逐行分配它们。Python 有一种优雅且更好的方法来分配多个变量。

示例:

```
firstName = "John"
lastName = "Henry"
city = "Manchester"
```

分配这些变量的更好方法是:

```
firstName, lastName, city = "John", "Henry", "Manchester"
```

这个变量的赋值比上面的要干净优雅得多。

# 比起循环，更喜欢列表理解

List comprehension 是一种优雅且更好的方式，可以在一行代码中基于现有列表的元素创建一个新列表。

列表理解被认为是一种比定义一个空列表并向该空列表添加元素更为 Pythonic 化的创建新列表的方式。

列表理解的另一个优点是，它比使用`append`方法向 Python 列表添加元素要快。

示例:

使用列表`append`方法:

```
newlist = []
for i in range(1, 100):
    if i % 2 == 0:
        newlist.append(i**2)
```

使用列表理解的更好方法:

```
newlist = [i**2 for i in range(1, 100)  if i%2==0]
```

使用列表理解时，代码看起来更整洁。

# 适当进口

除非需要，否则应该避免导入不必要的模块和库。您可以指定模块名称，而不是导入完整的库。

导入不必要的库会降低代码性能。

示例:

假设你需要求一个数的平方根。而不是这个:

```
import math
value = math.sqrt(50)
```

用这个:

```
from math import sqrt
value = sqrt(50)
```

# 串并置

在 Python 中，我们使用`+`操作符连接字符串。但是 Python 中连接字符串的另一种方法是使用`join` 方法。

`Join`方法是一种更 Pythonic 化的连接字符串的方式，它也比用`+`操作符连接字符串更快。

`join()`方法更快的原因是`+` 操作符创建一个新的字符串，然后在每一步复制旧的字符串，而`join()`方法不是这样工作的。

示例:

```
output = "Programming" + "is" + "fun 
```

使用`join`方法:

```
output = " ".join(["Programming" , "is", "fun"])
```

这两种方法的输出是相同的。唯一的区别是`join()`方法比`+`操作符快。

# 结论

这就是这篇文章的全部内容。在本文中，我们讨论了一些可以让代码运行得更快的技巧。这些技巧尤其适用于竞争性编程，在这种编程中，时间限制就是一切。

我希望你喜欢这篇文章。感谢阅读！

> *在你走之前……*

如果你喜欢这篇文章，并想继续关注更多**精彩的**文章，请考虑使用我的推荐链接[https://pralabhsaxena.medium.com/membership](https://pralabhsaxena.medium.com/membership)成为一名中级会员。

另外，你可以在这里免费订阅我的时事通讯: [Pralabh 的时事通讯](https://pralabhsaxena.medium.com/subscribe)。