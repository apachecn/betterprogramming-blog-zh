# 对初学者有用的 3 个 Python 技巧

> 原文：<https://betterprogramming.pub/useful-tricks-in-python3-e953430c002c>

## 编写干净的 Pythonic 代码

![](img/a77750515f02a1a2dfd3604f9d07f21e.png)

[Boitumelo Phetla](https://unsplash.com/@writecodenow?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

当我开始探索编程时，我在 [Udacity](https://www.udacity.com/) 上了一些关于 Python 2 的数据科学课程。当我最终投身编程时，我主要使用 Ruby on Rails 和 JavaScript (React)进行 web 开发。现在，几年后，由于 Python 的多功能性和广泛的开发者支持，我又回到了学习 Python 的道路上。Python 已经发展了很多，现在最常用的 Python 版本是更强大的 Python 3！

虽然 Python 一直被誉为易于阅读的开发人员友好的语言，但我想分享一些我最喜欢的技巧，这些技巧让它变得更加有趣。

# 1.默认字典

有多长时间你想做一个字典(如果你不熟悉 Python，可以考虑 HashMap)来跟踪一些东西，并且你必须检查你的键是否存在来决定添加什么值？

让我们举一个计算字符串中所有字符的例子。一种常见的方法是创建一个字典，遍历字符串，并将字符数存储在字典中。当我们遍历字符串时，如果我们发现一个我们以前没有见过的字符，我们初始化这个字符并设置它的计数为`1`。如果我们发现一个我们以前见过的，我们增加计数`1`:

[在 repl.it](https://repl.it/@dtluther/countcharsinstringpy#main.py) 试试。

在 Python 中，这没有太多代码，这很好。但是我们必须检查该字符是否在字典中，因为如果我们试图访问字典中缺少的字符，我们将得到一个`KeyError`:

[在 repl.it](https://repl.it/@dtluther/brokencountcharspy#main.py) 试试看。

幸运的是，Python 有一个工具可以让这变得更高效:那就是`defaultdict`。`defaultdict`接受一个参数类型(称为`default_factory`)，并在访问缺少的字典键时用它来设置默认值。我们需要做的就是从 Python `collections`库中导入它:

演示 defaultdict(int)如何处理丢失的键

由于我们将`int`传递给了`defaultdict`，当我们试图访问一个丢失的键时，它将返回`0`。因此，通过使用`defaultdict(int)`，我们可以在我们的字符计数示例中去掉条件检入:

[在 repl.it](https://repl.it/@dtluther/cleanercountcharspy) 试试看。

我们可以使用其他数据结构作为`default_factory`，比如列表(比如数组)和集合。例如，如果我们想创建一个邻接表来表示一个图，传递一个`list`到`defaultdict`是一个很好的选择。

# 2.柜台

首先，是的，这是一个大写的“c”。我们需要确保它是大写的。不要问我有多少次因为打错而得了一个`ImportError`。还记得我们上面用过的`defaultdict(int)`吗？一个`Counter`有内置的功能，甚至更多，我们将很快介绍。我们可以用一个`Counter`来代替我们用来计数字符的`defaultdict(int)`:

[在 repl.it](https://repl.it/@dtluther/countcharswithcounterpy) 试试看。

`Counter`的顺序可能会有所不同，但不要担心。`Counter`和`defaultdict`都是标准`dictionary`的子类，所以顺序并不重要。事实上，即使打印的值表明了对象的具体类型，我们也可以将它们放在一起比较，它们将评估为`True`:

[在 repl.it](https://repl.it/@dtluther/comparingdefaultdictandcounterpy) 试试吧。

`Counters`居然有办法让代码*甚至*更短！我不需要迭代，只需将一个字符串(或任何可以迭代的 Python 对象)传递给一个`Counter`，然后*瞧*，它会处理它:

[在 repl.it](https://repl.it/@dtluther/cleanestcountcharspy) 试试吧。

也有一些很好的内置方法，使它们非常有用，包括一种按照最高频率顺序返回项目的方法。更多信息，请查看 [Python 文档](https://docs.python.org/3/library/collections.html?highlight=defaultdict#collections.Counter)。

# 3.获取字典中最大值的关键字

找到每个字符的频率后，常见的下一步是返回最常见/最不常见的字符。我们来找最常见的。我们可以通过在字典中查找最大值来做到这一点。然后，我们可以遍历键，检查键的值，当我们找到等于最大值的值时，我们知道我们找到了正确的值:

[在 repl.it](https://repl.it/@dtluther/findmostcommoncharpy) 试试。

在我们得到每个字母在字典对象中出现的频率后(`Counter`是`dictionary`的子类)，第 7-10 行演示了一种非常简单的方法来查找最常见的字母。对于 Python 中的其他常见事物，我们有一种更简短的写法:

[在 repl.it](https://repl.it/@dtluther/cleanerfindmostcommonpy) 试试。

哇哦。如果你像我一样，你可能会认为这看起来有点神奇，但我保证它不像看起来那么花哨。Python 的`max()`函数可以比较多个值:

```
max(1, 2, 3)
> 3
```

或者我们可以给它传递一个 iterable(可以排序的东西，比如我们的`Counter`)，一个字符串，一个列表，等等。)它将返回最大值:

```
max(Counter({'l': 3, 'o': 2, 'H': 1, 'e': 1, ' ': 1, 'W': 1, 'r': 1, 'd': 1}))
> 'w'
```

如果我们将计数器传递给`max()`，它将比较我们字典中的字母(键)并返回“max”字母，在本例中是`w`。然而，额外的`key`参数允许我们传递一个单参数函数，类似于我们在`sort()`中所做的。让我们再看一次我们的*奇特的*例子:

再试一次。为什么不呢？

我们将`ch_count.get`传递给了`key`参数。`ch_count.get(key)`是获取键值的另一种方式。`ch_count[key]`与`ch_count.get(key)`非常相似，但是`ch_count.get(key)`有一个额外的好处，那就是能够传入一个默认值。你可以在文档中读到更多关于这个[的内容。](https://docs.python.org/3/library/stdtypes.html#mapping-types-dict)

自从我们通过了`key=ch_count.get`，我们本质上是在告诉 Python，“嘿，去查字典，给我 max 字母。但是，我希望您对每个字母调用`ch_count.get`并使用这些值来确定最大值，而不是将字母相互比较来找到最大值。”起初这可能看起来有点古怪，但是我鼓励你尝试一下，让你的代码更加整洁。

# 结论

今天就到这里吧！非常感谢你阅读这篇文章。如果你觉得这很有用或有任何问题，请随时分享你的反馈，我非常感谢。

本周下面的资源列表有点短，但还是很有帮助的！

# 资源

*   [好的 ol' Python 文档](https://docs.python.org/3/)
*   在经常重复和优化编码模式的地方练习算法。