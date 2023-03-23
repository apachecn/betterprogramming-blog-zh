# 编写优雅代码的 7 个鲜为人知的 Python 技巧

> 原文：<https://betterprogramming.pub/7-lesser-known-python-tips-to-write-elegant-code-fa06476e3959>

## 通过利用 Python 的内置函数和模块，发现用 Python 编写干净、优雅和可读的代码的有用技巧

![](img/895371bf6a8679d248758f1d0ad085bf.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

最近，我参加了一个 Python 编程训练营，组织者是[mathspp 博客](https://twitter.com/mathsppblog)的作者 Rodrigo giro serro。这个训练营使用 [Advent of Code](https://adventofcode.com/2021/about) 平台，这是一个很酷的编程活动，每年 12 月 1 日至 25 日举行。每天，网站都会发布两个新的谜题来解答。

总的来说，这是一次很棒的学习经历。我不仅从组织者那里学到了很多技巧，也从新兵训练营的同伴那里学到了很多。非常强调最大化使用 Python 的内置函数和模块，以及编写干净优雅的代码。

这篇文章是我记录我学到的这些新技巧的方式，也是我努力与你分享的方式。它们极大地提高了我的 Python 编程技能，我希望它们也能以同样的方式帮助你。你准备好了吗？让我们开始吧！

# 目录

1.  [被低估的](#664e) `[enumerate()](#664e)`
2.  `[int()](#10db)`[功能比你想象的](#10db)更有用
3.  [让我们断言](#8f12) `[assert](#8f12)`声明！
4.  [拆箱和装箱——操作员](#bb2a) `[*](#bb2a)` [的权力](#bb2a)
5.  [从字典中检索值？考虑用](#564c) `[.get()](#564c)` [代替](#564c)
6.  `[defaultdict](#fa3b)` [—用默认值创建字典](#fa3b)
7.  [用](#f39b) `[sorted](#f39b)()`任意排序

# 1.被低估的`enumerate()`

遍历列表、元组或字符串等可迭代对象是非常常见的。我们中的一些人可能会做这样的事情:

```
>>> lst = ["a", "b", "c", "d"]
>>> for idx in range(len(lst)):
...     print(f"Index: {idx} --> Element: {lst[idx]}")Index: 0 --> Element: a
Index: 1 --> Element: b
Index: 2 --> Element: c
Index: 3 --> Element: d
```

如果我们只是浏览列表，计算其长度范围是多余的。相反，我们可以使用`enumerate()`，这是一个内置函数，就是为了这个目的。`enumerate()`函数返回一个枚举对象，该对象包含一个 iterable 对象的索引和元素对。

```
>>> enumerate(lst)
<enumerate at 0x1dde1211e80>
```

如果你把它绕在`list()`上，你就能看到它们:

```
>>> list(enumerate(lst))
[(0, 'a'), (1, 'b'), (2, 'c'), (3, 'd')]
```

使用`enumerate()`，先前的代码可以重写为:

```
>>> lst = ["a", "b", "c", "d"]
>>> for idx, item in enumerate(lst):
...     print(f"Index: {idx} --> Element: {item}")Index: 0 --> Element: a
Index: 1 --> Element: b
Index: 2 --> Element: c
Index: 3 --> Element: d
```

假设您希望起始索引为 1，而不是 0。您可以使用可选的`start`参数来指定:

```
>>> lst = ["a", "b", "c", "d"]
>>> for idx, item in enumerate(lst, start=1):
...     print(f"Index: {idx} --> Element: {item}")Index: 1 --> Element: a
Index: 2 --> Element: b
Index: 3 --> Element: c
Index: 4 --> Element: d
```

# 2.int()函数比您想象的更有用

您可能已经使用过`int()`函数将字符串或浮点数转换成整数。

```
# Converting a string into an integer
>>> var = "5"
>>> print(int(var))
>>> print(type(int(var)))5
<class 'int'>
```

对于字符串，它必须是整数的有效字符串表示，否则将引发错误。

```
# Converting a string into an integer
>>> var = "5.4321"
>>> print(int(var))
>>> print(type(int(var)))**ValueError**: invalid literal for int() with base 10: '5.4321'
```

有趣的是，当将整数的字符串表示转换成整数时，`int()`函数允许出现空格。

```
>>> int("5\n") == int("   5  ") == int("\t\n 5 \n\t ") == 5True
```

对于浮点数，`int()`函数会截断小数。

```
# Converting a float into an integer
>>> var = 5.4321
>>> print(int(var))
>>> print(type(int(var)))5
<class 'int'>
```

除了字符串和浮点数，您知道`int()`还可以用来将二进制数解析为整数吗？我们只需要指定`base=2`参数。

```
# Converting binary numbers into an integer
>>> print(int("1000", base=2))
>>> print(int("111110100", base=2))8
500
```

# 3.让我们断言断言语句！

`assert`语句是**一个布尔表达式，用于检查条件是否为真**。条件在关键字`assert`之后。如果条件为真，则什么都不会发生，程序转到下一行代码。

```
>>> x = "Hello, World!"
>>> assert x == "Hello, World!"
>>> print(x)Hello, World!
```

但是，如果条件为假，代码将停止并触发一个错误。

```
>>> x = "Hello, World!"
>>> assert x == "Hi, World!"
>>> print(x)AssertionError: 
```

您也可以在条件后指定自定义错误消息:

```
>>> x = "Hello, World!"
>>> assert x == "Hi, World!", "Uh oh, the condition is not met!"
>>> print(x)AssertionError: Uh oh, the condition is not met!
```

所以，一般的语法是这样的:

```
assert <insert condition>, <insert optional error message>
```

因为只要条件不满足，程序就会停止，`assert`语句是一个很好的调试工具——用来查看代码的哪些部分失败了以及失败的原因。例如，您可以使用它来检查函数中特定输入的数据类型或值，或者检查给定某些固定输入的函数的输出值。

# 4.拆包和打包——运营商的力量

在 Python 中，解包和打包是有用且方便的特性。您可以在赋值运算符右侧对存储在元组、字符串和列表等可迭代对象中的值进行解包，然后将它们赋给赋值运算符左侧的变量。变量将根据它们在 iterable 中的相对位置被赋值。

```
# Unpacking a tuple
>>> a, b, c = (1, 2, 3)
>>> print(a)
>>> print(b)
>>> print(c)1
2
3# Unpacking a string
>>> a, b, c = "123"
>>> print(a)
>>> print(b)
>>> print(c)1
2
3# Unpacking list
>>> a, b, c = [1, 2, 3]
>>> print(a)
>>> print(b)
>>> print(c)1
2
3
```

另一方面，打包使用了`*`操作符，它允许您在一个变量中打包多个值。

```
# Packing with a tuple
>>> a, *b = (1, 2, 3)
>>> print(a)
>>> print(b)1
[2, 3]# Packing with a string
>>> *a, b = "123"
>>> print(a)
>>> print(b)['1', '2']
3# Packing with list
>>> a, b, *c = [1, 2, 3]
>>> print(a)
>>> print(b)
>>> print(c)1
2
[3]
```

除了元组、列表和字符串，打包和解包也适用于生成器对象、集合和字典。当你想在变量之间交换值或者同时进行多个赋值时，这是一个方便的工具。它使你的代码更具可读性。

```
# Swapping values between variables
>>> a = 1
>>> b = 2
>>> a, b = b, a
>>> print(a)
>>> print(b)2
1# Parallel assignment of multiple values using unpacking
>>> first_name, last_name, gender = ["Jane", "Doe", "Female"]
>>> print(first_name)
>>> print(last_name)
>>> print(gender)Jane
Doe
Female# Parallel assignment of multiple values using packing
>>> *full_name, gender = ["Jane", "Doe", "Female"]
>>> print(full_name)
>>> print(gender)['Jane', 'Doe']
Female
```

如果你有兴趣了解更多，这里有一篇关于打包和解包的解释很好的文章。

# 5.从字典中检索值？考虑使用。请改为 get()。

假设您有一个将键映射到其对应值的字典。在下面的例子中，为了在`num_to_words`字典中检索一个关键字的英文单词，您可能会尝试使用方括号。

```
>>> num_to_words = {1: 'one', 2: 'two', 3: 'three'}
>>> print(num_to_words[2])two
```

如果您想检索一个不在`num_to_words`字典中的关键字的英语单词，该怎么办？是的，你猜对了——它触发了一个错误！

```
>>> num_to_words = {1: 'one', 2: 'two', 3: 'three'}
>>> print(num_to_words[4])KeyError: 4
```

与方括号相比，`.get()`方法可能是一个更稳健、更实用的选择。`.get()`方法返回字典中存在的键值，这与使用方括号没有什么不同。

```
>>> num_to_words = {1: 'one', 2: 'two', 3: 'three'}
>>> print(num_to_words.get(2))two
```

现在，在字典中查询不存在的键值时，`.get()`的好处变得显而易见。默认情况下，它不会触发一个`KeyError`并中断您的代码，而是返回值`None`并保持您的代码运行。

```
>>> num_to_words = {1: 'one', 2: 'two', 3: 'three'}
>>> print(num_to_words.get(4))None
```

`.get()`的美妙之处在于，当找不到键时，您甚至可以指定一个自定义值来返回。

```
>>> num_to_words = {1: 'one', 2: 'two', 3: 'three'}
>>> print(num_to_words.get(4, "Uh oh... key is not found!"))Uh oh... key is not found!
```

`.get()`有用的一个用例是用字典中相应的值替换列表中的值。在下面的例子中，我们想使用`num_to_words`字典将`num_list`的每个元素替换为相应的英语单词。如果使用方括号，代码会中断，因为字典的键中没有`4`。

```
# Using square brackets
>>> num_to_words = {1: 'one', 2: 'two', 3: 'three'}
>>> num_list = [1, 2, 3, 4, 5, 6]
>>> word_list = [num_to_words[num] for num in num_list]
>>> print(word_list)KeyError: 4
```

然而，如果我们使用`.get()`方法，代码就能工作。

```
# Using .get() method with default value of None
>>> num_to_words = {1: 'one', 2: 'two', 3: 'three'}
>>> num_list = [1, 2, 3, 4, 5, 6]
>>> word_list = [num_to_words.get(num) for num in num_list]
>>> print(word_list)['one', 'two', 'three', None, None, None]
```

也许，对于不存在的键，`.get()`返回`None`还是不太理想。我们可以指定`.get()`方法的第二个参数，这样，如果在`num_to_words`字典中找不到它，它就返回键本身。

```
# Using .get() method with customised default value
>>> num_to_words = {1: 'one', 2: 'two', 3: 'three'}
>>> num_list = [1, 2, 3, 4, 5, 6]
>>> word_list = [num_to_words.get(num, num) for num in num_list]
>>> print(word_list)['one', 'two', 'three', 4, 5, 6]
```

话虽如此，`.get()`未必是所有情况下最理想的选择。例如，您有时可能希望您的代码故意触发一个错误，以便您可以通过异常显式地处理它。在这种情况下，`[]`可能是首选。

# 6.defaultdict 使用默认值创建词典

另一种避免查询字典中不存在的关键字而导致`KeyError`的方法是使用内置`collections`模块中的`defaultdict`。

使用`defaultdict`，您可以指定一个“默认值工厂”，一个为任何不存在的键返回我们想要的默认值的函数。这里，当初始化一个`defaultdict`对象时，我们使用一个`lambda`函数来做这件事。

```
>>> num_to_words_dd = defaultdict(lambda: "Uh oh... key is not found in `defaultdict`!")
>>> num_to_words_dd[1] = 'one'
>>> num_to_words_dd[2] = 'two'
>>> num_to_words_dd[3] = 'three'
>>> print(num_to_words_dd)defaultdict(<function <lambda> at 0x000001DDE3B589D0>, {1: 'one', 2: 'two', 3: 'three'})
```

当在`defaultdict`对象中查询存在的键时，它与普通的字典对象没有什么不同。为了说明`defaultdict`如何工作，下面的代码片段使用方括号，而不是`.get()`方法。

```
>>> num_to_words_dd[2]'two'
```

但是，如果查询一个不存在的键，将返回默认值。

```
>>> num_to_words_dd[5]'Uh oh... key is not found in `defaultdict`!'
```

您也可以使用`int`或`list`关键字初始化`defaultdict`。如果用`int`初始化，则创建一个默认值为 0 的`defaultdict`对象。当您查询一个不存在的键时，它返回 0。

```
>>> counter = defaultdict(int)
>>> lst = [0, 1, 2, 2, 3, 1, 1, 0]
>>> for num in lst:
...     counter[num] += 1
>>> print(counter[0]) # Key that exists
>>> print(counter[5]) # Key that does not exist2
0
```

类似地，如果你用`list`初始化它，那么默认情况下用一个空列表创建一个`defaultdict`对象，查询一个不存在的键返回`[]`。

```
>>> country_list = [('AU','Australia'), ('CN','China'), 
...                 ('FR','France'), ('SG', 'Singapore'), 
...                 ('US', 'United States'), ('PT', 'Portugal')]
>>> country_dict = defaultdict(list)
>>> for code, country in country_list:
...     country_dict[code].append(country)
>>> print(country_dict['AU']) # Key that exists
>>> print(country_dict['BX']) # Key that does not exist['Australia']
[]
```

总之，无论何时需要创建一个字典，并且每个元素的值都需要有一个特定的默认值时，`defaultdict`都是一个很好的选择。

# 7.用 s**sorted**()按你想要的方式排序

如果你想对一个 iterable 进行排序，比如一个列表、一个字符串或者一个元组，你可以使用`sorted()`函数。它以排序的方式返回一个包含原始元素的列表，而不改变原始序列。

对于字符串，它返回一个字符列表，首先按标点符号或空格排序，然后按字母顺序按大写字母和小写字母排序。

```
>>> sorted("Hello, World!")[' ', '!', ',', 'H', 'W', 'd', 'e', 'l', 'l', 'l', 'o', 'o', 'r']
```

对于数字列表，它返回一个按升序排序的序列。

```
>>> sorted([5, 2, 4, 1, 3])[1, 2, 3, 4, 5]
```

对于字符串列表，它返回一个根据前几个字符按字母顺序排序的序列:

```
>>> fruits = ["apple", "watermelon", "pear", 
...           "banana", "grapes", "rockmelon"]
>>> sorted(fruits)['apple', 'banana', 'grapes', 'honeydew', 'pear', 'watermelon']
```

您也可以通过在`sorted()`功能中将`reverse`指定为`True`来颠倒顺序。

```
>>> sorted(fruits, reverse=True)['watermelon', 'pear', 'honeydew', 'grapes', 'banana', 'apple']
```

你还知道你可以定制你想要的排序方式吗？您可以指定一个函数，并将其分配给`sorted`函数中的`key`参数。例如，如果您想按照每个单词的长度对`fruits`列表进行升序排序，您可以这样做:

```
>>> sorted(fruits, key=len)['pear', 'apple', 'banana', 'grapes', 'honeydew', 'watermelon']
```

或者，如果您想根据每个单词中字母“n”的数量对水果进行排序，您可以指定一个`lambda`函数，如下所示:

```
>>> sorted(fruits, key=lambda x: x.count('n'))['apple', 'pear', 'grapes', 'watermelon', 'honeydew', 'banana']
```

我们也可以通过引用另一个 iterable 来进行排序。在下面的例子中，我们根据`prices`字典对水果进行价格递增排序。

```
>>> prices = {"apple": 1.4, "watermelon": 4, "pear": 1.2, 
...           "banana": 2.3, "grapes": 3.5, "honeydew": 2.6}
>>> sorted(fruits, key=lambda x: prices.get(x))['pear', 'apple', 'banana', 'honeydew', 'grapes', 'watermelon']
```

好吧！暂时就这样了。谢谢你远道而来。我希望你已经学会了简单的技巧来编写优雅的代码，并最大限度地利用 Python 中鲜为人知的内置函数和模块。

对于其中的一些技巧，我只触及了皮毛；它们可以各自独立成篇。因此，我强烈建议您查看更多相关资源以了解更多信息。

> ***走之前……***
> 
> 如果这篇文章有价值，并且你希望支持我作为一个作家，请考虑注册一个中级会员。一个月 5 美元，费用直接支持我和其他你读其文章的作家。作为会员，你可以无限制地阅读媒体上发表的故事。如果你使用[这个链接](https://zeyalt.medium.com/membership)注册，我将获得一小笔佣金。如果你希望在我发布时得到通知，也可以随时加入我的[电子邮件列表](https://zeyalt.medium.com/subscribe)。

## **参考文献**

1.  Python 问题解决训练营 2021，Rodrigo giro serro
2.  列举我，Pydon，2021 年 4 月 6 日，Rodrigo giro serro
3.  Python 中的解包:超越并行赋值，2021 年 9 月 19 日，Leodanis Pozo Ramos

```
**Want to Connect?**Reach out to me via [LinkedIn](https://www.linkedin.com/in/zeyalt/) or [Twitter](https://twitter.com/zeyalt_).
```