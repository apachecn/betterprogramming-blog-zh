# Python 列表理解比你想象的更强大

> 原文：<https://betterprogramming.pub/python-list-comprehensions-are-more-powerful-than-you-might-think-3363a90e5bb0>

## 借助这些未知的功能和技巧，写出更好的列表理解

![](img/b25f44badc710af4aac92369c222c17c.png)

[Aedrian](https://unsplash.com/@aedrian?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Python 的列表理解(和生成器)是一个非常棒的特性，可以极大地简化您的代码。然而，大多数情况下，我们只使用它们来编写一个单独的`for`循环，也许再加上一个`if`条件，仅此而已。如果你开始研究一下，你会发现 Python comprehensions 还有很多你不知道的特性，但是你可以从它身上学到很多…

# 多重条件句

我们知道我们可以使用`if`条件来过滤列表理解的结果，对于简单的理解，单个`if`通常就足够了。但是，如果您想要一个嵌套的条件呢？

可以使用“条件表达式”或通常所说的三元运算符来构建嵌套条件。这并不是一个很好的解决方案，所以你必须决定这几行节省下来的代码是否值得这个讨厌的一行程序。

除了使用复杂的条件句，也可以在一篇理解文章中叠加多个`if`。代码如下:

看上面展开的代码，这样写其实没多大意义，但是语法允许。

您可能希望使用它的一个原因是为了可读性，如下所示:

# 避免重复评估

假设你理解在条件函数和循环体中都调用了一个昂贵的函数。它可能如下所示:

这是低效的，因为它加倍了计算时间，但是我们能做什么呢？拯救的嵌套理解！

我想强调的是，以上并不是双循环。在这个例子中，我们在 list comprehension 内部构建了一个生成器，由外部循环使用。如果您觉得这很难理解，那么另一种方法是使用 walrus 操作符。代码如下:

这里`func`只被调用一次，创建一个局部变量`y`，它可以在表达式的其他部分使用。

# 处理异常

尽管列表理解通常用于简单的任务——比如对列表中的每个元素调用一个函数——但是也有可能在理解中抛出异常的情况。然而，在列表理解中没有处理异常的本地方法，所以我们能做些什么呢？

我们需要一个处理函数来捕捉理解中的异常。这里我们创建一个函数`catch`，它接受一个函数及其参数。如果在`catch`中抛出异常，那么返回异常。

考虑到我们需要一个助手函数，这不是一个理想的解决方案，但这是我们能做的最好的了，因为试图为此引入语法的提案( [PEP 463](https://peps.python.org/pep-0463/) )被拒绝了。

# 打破循环

列表理解的另一个限制是不能`break`循环。虽然在本机上不可能，但我们可以实现一个小技巧来解决这个问题:

上面的第一个例子使用了一个鲜为人知的`iter`函数的特性/行为。一旦`callable`函数值等于`sentinel`值，`iter(callable, sentinel)`返回一个“中断”迭代的迭代器。当内部`iter`返回 sentinel(示例中的`4`)时，循环自动停止。

这不是很可读，所以你可以利用伟大的`itertools`模块和`takewhile`函数，如第二个例子所示。

顺便提一下——如果你认为打破列表理解中的循环是可能的，那么你是正确的。在 Python 3.5 之前，你可以使用一个 helper 函数来提升列表理解中的`StopIteration`，然而，这在 [PEP 479](https://peps.python.org/pep-0479/#explanation-of-generators-iterators-and-stopiteration) 中有所改变。

# 技巧(和黑客)

在前面的章节中，我们已经看到了列表理解的一些晦涩的特性，这些特性在日常编码中可能非常有用，也可能不太有用。所以，现在让我们看看一些你可以马上使用的技巧(和小窍门)。

虽然简单、普通的列表理解非常强大，但是当与库如`itertools`(见上一节)或其扩展`more-itertools`一起使用时，它们会变得更好。

假设您需要查找连续的数字、日期、字母、布尔值或任何其他可排序的对象。你可以通过将`more-itertools`中的`consecutive_groups`与列表理解结合起来，很好地解决这个问题。代码如下:

这里我们有一个日期列表，其中一些是连续的。我们使用日期的顺序值将日期传递给`consecutive_groups`函数进行排序。然后，我们使用理解力将返回的组收集到一个列表中。

在 Python 中计算数字的累加和非常容易——你只需将一个列表传递给`itertools.accumulate`,就可以得到累加和。但是，如果我们想消除这种积累呢？

在`more_itertools.pairwise`的帮助下，这很简单！

如前所述，new-ish walrus 操作符可以与 list comprehensions 一起使用来创建一个局部变量。这在很多情况下都很有用。一种这样的情况是使用`any()`和`all()`功能:

Python 的`any()`和`all()`函数可以验证某个 iterable 中的任意或全部值是否满足一定的条件。但是，如果您还想捕获导致`any()`返回`True`的值(所谓的“见证”)或导致`all()`失败的值(所谓的“反例”)，该怎么办呢？

`any()`和`all()`都使用短路来计算给定的表达式。这意味着他们一找到第一个“见证”或“反例”就停止评估。所以，用这一招，walrus 操作符创建的变量总会给我们第一个“见证”/“反例”。

# 结束语

许多特性和技巧展示了列表理解的可能性和局限性。在我看来，学习这些复杂性是更好地理解特定语言特性的好方法，即使它在日常编码中并不真正有用。最重要的是，这很有趣。

也就是说，我希望你在这里学到一些东西，并意识到如果你决定在你的列表理解中使用复杂的条件句或循环中断，你的同事可能会讨厌你。

[](/should-you-be-using-pythons-walrus-operator-yes-and-here-s-why-36297be16907) [## 你应该使用 Python 的 Walrus 操作符吗？(是的。原因如下)

### Python 有争议的赋值表达式——也称为 walrus 运算符——可以改进您的代码，现在是您…

better 编程. pub](/should-you-be-using-pythons-walrus-operator-yes-and-here-s-why-36297be16907) [](https://python.plainenglish.io/its-time-to-say-goodbye-to-these-obsolete-python-libraries-7c02aa77d84a) [## 是时候向这些过时的 Python 库说再见了

### 忘掉 os.path、random、pytz、namedtuple 等等，开始使用最新最棒的 Python 库吧。

python .平原英语. io](https://python.plainenglish.io/its-time-to-say-goodbye-to-these-obsolete-python-libraries-7c02aa77d84a) 

```
**Want to Connect?**This article was originally posted at [martinheinz.dev](https://martinheinz.dev/blog/80).
```