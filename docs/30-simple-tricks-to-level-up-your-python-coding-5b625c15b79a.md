# 提升 Python 编码水平的 30 个简单技巧

> 原文：<https://betterprogramming.pub/30-simple-tricks-to-level-up-your-python-coding-5b625c15b79a>

## 更好的 Python

![](img/232f34ba11b473740282a898de22dfa4.png)

林赛·亨伍德在 [Unsplash](https://unsplash.com/s/photos/stairs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

作为一种通用编程语言，Python 已经渗透到几乎所有的工业和学术领域。根据我在生物医学领域对 Python 编程的观察，我意识到相当多的 Python 程序员，包括我自己，来自不同的编程背景，比如 Matlab、C、C++、Java、JavaScript 和 Swift，更不用说一些以前没有编程经验的人了。

由于 Python 是他们的“外国”语言，他们可能没有接受过 Python 编码的系统培训，也可能不知道 Python 开发的惯用方法。

但是不要误解我——只要代码能够满足预期的目的，他们仍然可以通过以不同的方式实现相同的功能来编写优秀的代码。因此，对我来说，编写非惯用的 Python 程序是可以接受的。

然而，就像我作为一个在美国的外国人一直在努力改善我的英语口音一样，我希望我的 Python 代码尽可能地地道。在这篇文章中，我分享了我在过去几年中积累的一些习惯用法，希望能帮助你提高 Python 编码的水平。

# 1.对序列进行切片

一些常见的序列类型有列表、元组和字符串。我们可以通过分割另一个序列来创建一个序列。以下功能使用列表作为示例，但是它们也可以应用于元组、字符串和其他序列类型(例如，字节)。

```
>>> a = [0, 2, 4, 6, 8, 10, 12, 14, 16, 18, 20]
>>> # Using a range, [start, end)
>>> a[1:3]
[2, 4]
>>> # Using a range with a step
>>> a[1:9:2]
[2, 6, 10, 14]
>>> # Leave out the start = an implicit start of 0
>>> a[:5]
[0, 2, 4, 6, 8]
>>> # Leave out the stop = an implicit end to the very last item
>>> a[9:]
[18, 20]
>>> # Entire list
>>> a[:]
[0, 2, 4, 6, 8, 10, 12, 14, 16, 18, 20]
```

# 2.颠倒顺序

有时候，我们想颠倒一个顺序。虽然我们可以使用 for 循环来实现它，但是有一种简单的方法。类似地，如上所述，当一个功能可用于序列时，通常意味着字符串、元组和列表都支持该功能。

```
>>> a = (1, 2, 3, 4, 5)
>>> a[::-1]
(5, 4, 3, 2, 1)
>>> b = 'start'
>>> b[::-1]
'trats'
```

# 3.使用反向索引访问序列中的元素

如果我们想访问序列末尾的一些元素，倒计数更容易。在 Python 序列中，最后一个元素的索引为-1，前面一个元素的索引为-2，依此类推。

```
>>> a = 'Hello World!'
>>> # instead of using a[len(a)-1]
>>> a[-1]
'!'
>>> # in combination with slicing
>>> a[-5:-1]
'orld'
```

# 4.多重任务

当我们想给几个变量赋值的时候，我们可以做多重赋值。我们可以应用相同的习语来交换列表中的两个变量或两个元素。在幕后，这个特性与本文后面介绍的元组解包密切相关。

```
>>> # instead of doing a = 8; b = 5
>>> a, b = 8, 5
>>> print(f'a is {a}; b is {b}')
a is 8; b is 5
>>> # Swap two variables
>>> a, b = b, a
>>> print(f'a is {a}; b is {b}')
a is 5; b is 8
>>> # Swap the first and last elements in a list
>>> numbers = [1, 2, 3, 4, 5]
>>> numbers[0], numbers[-1] = numbers[-1], numbers[0]
>>> numbers
[5, 2, 3, 4, 1]
```

# 5.检查序列是否为空

有些操作只有在序列(例如 list、tuple)不为空时才有意义，因此我们需要在应用适当的操作之前进行检查。为此，我们可以使用关键字`not`对序列求反(例如，`not []`)，如果序列为空，它将计算为`True`。此外，我们可以对另外两种常见的数据类型做同样的事情:`dict`和`set`。

```
>>> empty_list = [(), '', [], {}, set()]
>>> for item in empty_list:
...     if not item:
...         print(f'Do something with the {type(item)}')
... 
Do something with the <class 'tuple'>
Do something with the <class 'str'>
Do something with the <class 'list'>
Do something with the <class 'dict'>
Do something with the <class 'set'>
```

# 6.列出理解

Python 中一个方便的特性是列表理解，我们可以用它非常方便地构造一个列表。列表理解一般格式为`[some_expression for element in iterable if some_condition]`。

```
>>> a = [1, 2, 3, 4, 5]
>>> [x*2 for x in a]
[2, 4, 6, 8, 10]
>>> [x*3 for x in a if x%2 == 1]
[3, 9, 15]
```

# 7.集合理解

如上所述，集合理解的用法类似于列表理解。区别在于我们将使用花括号而不是方括号。此外，重复的元素将被`set`数据类型的定义移除。

```
>>> a = [1, -2, 2, -3, 3, 4, 4, 5, 5, 5]
>>> {x*x for x in a}
{1, 4, 9, 16, 25}
```

# 8.字典理解

除了列表和集合理解之外，理解特性也可用于字典数据类型的创建。一个`dict`由键-值对组成，因此 dict 理解涉及键和值的规范，它们由冒号分隔。

```
>>> a = [1, 2, 3, 4, 5]
>>> {x: x*x for x in a}
{1: 1, 2: 4, 3: 9, 4: 16, 5: 25}
```

# 9.生成器表达式

Python 中的生成器是创建迭代器的一种便捷方式。由于生成器是“懒惰的”(即，在请求时产生所需的项目)，它们非常节省内存。创建生成器的一种特殊方法称为生成器表达式，它在语法上类似于列表理解，除了使用圆括号而不是方括号。

在下面的例子中，当生成器直接用在可迭代的函数中时，括号是可选的。

```
>>> sum(x**2 for x in range(100))
328350
>>> max((x*x for x in range(100)))
9801
```

# 10.解包元组

元组是 Python 中非常常见的数据结构。它们只是相关值的组，元组的常见用法包括访问它们的元素。我们可以使用索引来访问这些元素，但是解包是一种更方便的方式。关于它的用法，我们可以使用下划线来表示我们不需要的元素，并使用星号来指定除命名元素之外的其余元素。

```
>>> items = (0, 'b', 'one', 10, 11, 'zero')
>>> a, b, c, d, e, f = items
>>> print(f)
zero
>>> a, *b, c = items
>>> print(b)
['b', 'one', 10, 11]
>>> *_, a, b = items
>>> print(a)
11
```

# 11.在 for 循环中使用 Enumerate()

`enumerate()`函数接受一个 iterable 来创建一个迭代器。此外，它还可以跟踪迭代次数。我们可以选择设置计数的开始。默认计数从 0 开始。

```
>>> students = ('John', 'Mary', 'Mike')
>>> for i, student in enumerate(students):
...     print(f'Iteration: {i}, Student: {student}')
... 
Iteration: 0, Student: John
Iteration: 1, Student: Mary
Iteration: 2, Student: Mike
>>> for i, student in enumerate(students, 35001):
...     print(f'Student Name: {student}, Student ID #: {i}')
... 
Student Name: John, Student ID #: 35001
Student Name: Mary, Student ID #: 35002
Student Name: Mike, Student ID #: 35003
```

# 12.在 for 循环中使用 Reversed()

`reversed()`函数经常在 for 循环中使用，作为一种以与原始 iterable 相反的顺序创建迭代器的方法。

```
>>> tasks = ['laundry', 'picking up kids', 'gardening', 'cooking']
>>> for task in reversed(tasks):
...     print(task)
... 
cooking
gardening
picking up kids
laundry
```

# 13.Zip()函数

`zip()`函数对于在一对一匹配的基础上连接多个可重复项很有用。如果某些可重复项超过了最短的一个，它们就会被截断。这个函数返回一个迭代器，因此经常在迭代中使用。我们也可以使用`zip()`函数来解压一个带有星号的迭代器，并将解压后的项赋给变量。

```
>>> students = ('John', 'Mary', 'Mike')
>>> ages = (15, 17, 16)
>>> scores = (90, 88, 82, 17, 14)
>>> for student, age, score in zip(students, ages, scores):
...     print(f'{student}, age: {age}, score: {score}')
... 
John, age: 15, score: 90
Mary, age: 17, score: 88
Mike, age: 16, score: 82
>>> zipped = zip(students, ages, scores)
>>> a, b, c = zip(*zipped)
>>> print(b)
(15, 17, 16)
```

# 14.用于排序的 Lambdas

Lambdas 是匿名函数，可以用一个单行表达式接受多个参数。它的一个常见用法是在`sorted()`函数中设置为`key`参数。除此之外，lambdas 经常用于一些函数中(例如，`max()`，`map()`)，在这些函数中，一行表达式可用于替换使用`def`关键字的常规函数。

```
>>> students = [{'name': 'John', 'score': 98}, {'name': 'Mike', 'score': 94}, {'name': 'Jennifer', 'score': 99}]
>>> sorted(students, key=lambda x: x['score'])
[{'name': 'Mike', 'score': 94}, {'name': 'John', 'score': 98}, {'name': 'Jennifer', 'score': 99}]
```

# 15.速记条件赋值

这个特性主要是一个语法糖。当你需要根据某个条件给一个变量赋值的时候，我们可以用一个简写的赋值，使用这个通用的形式:`y = x if condition_met else another_x`。

```
>>> some_condition = True
>>> # the expanded format
>>> if some_condition:
...     x = 5
... else:
...     x = 3
>>> print(f'x is {x}')
x is 5
>>> # the shorthand way
>>> x = 5 if some_condition else 3
>>> print(f'x is {x}')
x is 5
```

# 16.集合中的成员测试

有时，我们需要测试某个元素是否存在于一个集合中，然后才能将操作应用于集合或匹配的项目。惯用的方法是使用`in`关键字。

```
>>> a = ('one', 'two', 'three', 'four', 'five')
>>> if 'one' in a:
...     print('The tuple contains one.')
... 
The tuple contains one.
>>> b = {0: 'zero', 1: 'one', 2: 'two', 3: 'three'}
>>> if 2 in b.keys():
...     print('The dict has the key of 2.')
... 
The dict has the key of 2.
```

# 17.使用 Get()在字典中检索一个值

我们通常可以在方括号中指定键来检索键的值。然而，当字典中不存在这个键时，它会产生一个错误。当然，我们可以使用 try/except 来解决这个问题。相反，我们可以使用`get()`方法，该方法允许我们在键不在字典中时使用默认值。

```
>>> number_dict = {0: 'zero', 1: 'one', 2: 'two', 3: 'three'}
>>> number_dict[5]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 5
>>> number_dict.get(5, 'five')
'five'
```

# 18.获取字典中值最大的键

对于字典，我们有时需要找出值最大的键。我们可以首先在所有值的列表中找到最大值的索引，然后从另一个存储所有键的列表中找到相应的键。或者，更简单的方法是在`max()`函数中指定`key`参数。

为了简单起见，我们不考虑最大值可能有重复的情况。此外，使用`min()`函数，同样的方法可以应用于寻找具有最小值的键。

```
>>> model_scores = {'model_a': 100, 'model_z': 198, 'model_t': 150}
>>> # workaround
>>> keys, values = list(model_scores.keys()), list(model_scores.values())
>>> keys[values.index(max(values))]
'model_z'
>>> # one-line
>>> max(model_scores, key=model_scores.get)
'model_z'
```

# 19.使用 Print()函数进行调试

对于较小的项目，我们总是可以使用`print()`函数来帮助我们调试。我们在教学中也经常使用这个函数。对于`print()`函数，我们经常使用一些技巧。第一种是结束除默认换行符以外的字符串，第二种是使用 f-string，它允许我们创建一个包含一些表达式的字符串。

```
>>> for i in range(5):
...     print(i, end=', ' if i < 4 else '\n')
... 
0, 1, 2, 3, 4
>>> for i in range(5):
...     print(f'{i} & {i*i}', end=', ' if i < 4 else '\n')
... 
0 & 0, 1 & 1, 2 & 4, 3 & 9, 4 & 16
```

# 20.海象操作员

海象操作符(`:=`)是 Python 3.8+中的一个新特性。它只是赋值表达式的另一个名称，即在表达式中对变量的赋值。通常，当一个表达式使用一个变量时，该变量必须在前面声明。使用 walrus 运算符，变量赋值可以包含在表达式中，并且变量可以立即使用。

```
>>> a = ['j', 'a', 'k', 'd', 'c']
>>> if (n := len(a))%2 == 1:
...     print(f'The number of letters is {n}, which is odd.')
...
The number of letters is 5, which is odd.
```

# 21.拆开一根绳子

当我们处理字符串时，将字符串分成单词列表是一项常见的任务。在这种情况下，我们可以使用`split()`函数，它接受一个分隔符和可选的最大拆分。一个相关的函数是`rsplit()`函数，它有一个类似的功能，除了它从右边开始拆分，以满足设置时的最大拆分要求。

```
>>> sentence = 'this is, a python, tutorial, about, idioms.'
>>> sentence.split(', ')
['this is', 'a python', 'tutorial', 'about', 'idioms.']
>>> sentence.split(', ', 2)
['this is', 'a python', 'tutorial, about, idioms.']
>>> sentence.rsplit(', ')
['this is', 'a python', 'tutorial', 'about', 'idioms.']
>>> sentence.rsplit(', ', 2)
['this is, a python, tutorial', 'about', 'idioms.']
```

# 22.在 Iterable 中连接字符串

当处理字符串时，我们有时需要通过连接包含在 iterable(例如，list、tuple)中的一系列字符串来创建单个字符串。在这种情况下，我们可以使用由所需分隔符调用的`join()`函数。

```
>>> words = ('Hello', 'Python', 'Programmers')
>>> '!'.join(words)
'Hello!Python!Programmers'
>>> words_dict = {0: 'zero', 1: 'one', 2: 'two', 3: 'three'}
>>> '&'.join(words_dict.values())
'zero&one&two&three'
```

# 23.Map()函数

`map()`函数是高阶函数(即使用函数作为参数或返回函数作为输出的函数)。它有一个通用的格式`map(function, iterables)`，将函数应用于 iterable(s)并返回一个`map`对象，这是一个迭代器。iterables 的数量应该与函数所需的参数数量相匹配。

在下面的例子中，内置的`pow()`函数需要两个参数。当然，也可以使用自定义函数。顺便提一下，当我们使用`map()`函数创建一个列表时，我们可以使用列表理解来达到同样的效果。

```
>>> numbers = (1, 2, 4, 6)
>>> indices = (2, 1, 0.5, 2)
>>> # use map()
>>> list(map(pow, numbers, indices))
[1, 2, 2.0, 36]
>>> # list comprehensions
>>> [pow(x, y) for x, y in zip(numbers, indices)]
[1, 2, 2.0, 36]
```

# 24.Filter()函数

`filter()`功能是使用指定函数或 lambda 函数过滤序列。这个函数返回一个过滤器对象，它是一个迭代器。总的来说，它的用法与`map()`功能非常相似。

```
>>> def good_word(x: str):
...     has_vowels = not set('aeiou').isdisjoint(x.lower())
...     long_enough = len(x) > 7
...     good_start = x.lower().startswith('pre')
...     return has_vowels & long_enough & good_start
... 
>>> words = ['Good', 'Presentation', 'preschool', 'prefix']
>>> list(filter(good_word, words))
['Presentation', 'preschool']
```

# 25.找出列表中最常见的元素

当我们使用列表来记录可能有重复元素的东西时，比如说跟踪一系列游戏的获胜者，找出谁赢了最多次是一项相关的任务。这可以通过指定`key`参数使用`max()`函数来完成，它将通过集合中元素的计数找出最大值。

```
>>> winnings = ['John', 'Billy', 'Billy', 'Sam', 'Billy', 'John']
>>> max(set(winnings), key = winnings.count)
'Billy'
```

# 26.跟踪列表中元素的频率

根据上面的例子，我们还想知道非冠军选手在比赛中的表现，这样我们就可以找出第二名和第三名。要做到这一点，我们需要找出每个玩家有多少奖金。我们可以使用字典理解和带有 lambda 函数的`sorted()`函数。

```
>>> winnings = ['John', 'Billy', 'Billy', 'Sam', 'Billy', 'John']
>>> tracked = {item: winnings.count(item) for item in set(winnings)}
>>> sorted(tracked.items(), key=lambda x: x[1], reverse=True)
[('Billy', 3), ('John', 2), ('Sam', 1)]
```

# 27.检查对象的类型

检查对象的类型是 Python 中自省主题的一部分。有时候，我们需要知道一个对象是否属于某种类型，然后再应用相应的函数。为此，我们可以使用`type()`或`isinstance()`函数，后者是一种更灵活的方法，允许一对多检查。

```
>>> def check_type(number):
...     if type(number) == int:
...         print('do something with an int')
...     if isinstance(number, (int, float)):
...         print('do something with an int or float')
... 
>>> check_type(5)
do something with an int
do something with an int or float
>>> check_type(4.2)
do something with an int or float
```

# 28.Any()函数

假设我们有一个记录列表，记录了 John 到达工作地点的时间。一个用例是我们想知道他本周是否迟到，在这种情况下，`any()`函数非常方便。如果布尔列表中的任何元素为`True`，该函数将返回`True`。

```
>>> arrival_hours = {'Mon': 8.5, 'Tue': 8.75, 'Wed': 9, 'Thu': 8.5, 'Fri': 8.5}
>>> arrival_checks = [x>8.75 for x in arrival_hours.values()]
>>> any(arrival_checks)
True
```

# 29.All()函数

根据上面的同一个例子，我们还想知道他是否整个星期都在 9:30 之前到达工作地点。为了测试情况是否如此，我们可以使用`all()`函数，只有当布尔列表中的所有元素都是`True`时，该函数才返回`True`。

```
>>> arrival_checks_all = [x<9.5 for x in arrival_hours.values()]
>>> all(arrival_checks_all)
True
```

# 30.对文件使用 With 关键字

当我们处理一个文件时，我们需要打开它，处理内容，然后关闭它。如果在使用后不关闭文件，该文件可能在一段时间内不可用。在这种情况下，`with`关键字非常有用。如下图，文件用完后会自动关闭。

```
>>> with open('a_file.txt') as file:
...     pass
...
>>> file.closed
True
```

# 结束语

本文并不打算提供 Python 编程中惯用用法的详尽列表。相反，它试图向您展示一些常见的习惯用法，其中大部分可以应用到我们的日常 Python 编码中。

我一定错过了本文中 Python 编码的一些习惯用法。因此，如果您想到任何方便的东西与其他 Python 程序员分享，非常欢迎您留下您的回答。