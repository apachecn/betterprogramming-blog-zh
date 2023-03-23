# 关于 Python 中的数据类，您需要知道的一切

> 原文：<https://betterprogramming.pub/everything-you-need-to-know-about-data-classes-in-python-46a93a90f9d3>

## Python 中字典和命名元组的更好替代方案

![](img/c7d3eb5529e5e4e39151812540378a82.png)

丹尼尔·伊恩在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们经常使用简单的数据结构，比如 Python 中的 Tuple ( `tuple`)或 Dictionary ( `dict`)。在我们的生活中，程序员几乎在任何地方和每天都使用它们来存储数据。

例如，我们可以用下面的代码示例表示一个汽车对象:

然而，当我们必须处理更复杂的数据时，这些基本的数据结构就变得不那么理想了。这里，我们需要记住`car`在我们的应用程序中代表一个汽车字典或元组，而不是一些字符串或整数。

如果我们只有三个字段(`name`、`brand`和`price`)，那么在上面的例子中使用 Tuple 来表示我们的 car 对象就可以了。当我们添加更多的字段到我们的汽车对象中，比如`manufacturer`、`condition`等等。，我们需要记住属性的顺序。

对于使用字典的情况，我们不能使用[点符号](https://www.askpython.com/python/built-in-methods/dot-notation)(即`car.name`)来访问我们的属性。此外，深度嵌套的字典往往很难使用。

在本文中，我们将讨论常规字典或元组的更好替代方案。

## 涵盖的主题

*   命名元组
*   数据类，命名元组的更好替代
*   自定义数据类
*   何时使用数据类

开始吧！

# 命名元组来拯救

为了提高代码的可读性，一种更常见的方法是使用 Python 内置的`[collections](https://docs.python.org/3/library/collections.html)`库中的命名元组(`namedtuple`)。

以我们上面的汽车为例，命名元组应该是这样的:

好多了。那么，为什么不一直使用命名元组呢？

好吧，命名元组确实有它自己的限制。除了不能为我们的汽车属性分配默认值之外，命名元组本质上是不可变的。

下面是 PEP 对[为什么我们不应该只使用命名元组](https://www.python.org/dev/peps/pep-0557/#why-not-just-use-namedtuple)的解释。

而且，使用 Dictionary、Tuple，甚至命名 Tuple 都不允许我们拥有自定义的类方法，这就回避了问题:为什么不直接使用常规的 [Python 类](https://docs.python.org/3/tutorial/classes.html)？

# Python 类

在 Python 中，一切都是对象，大多数对象都有属性和方法。通常，我们会在 Python 中使用`class`来创建我们自己的定制对象，它们有自己的属性和方法。

使用我们之前的示例创建一个简单的汽车对象:

每次一个新的属性被添加到我们的汽车对象中，我们都需要将它们传递到`__init__`方法中。如果我们需要在我们的`__repr__`方法中添加一个更具描述性的汽车对象的表示会怎么样？如果我们需要比较同一个汽车对象的两个汽车实例，该怎么办？

老实说，当我们只处理单个汽车对象时，事情并没有那么糟糕。但是如果我们不得不增加更多的职业，比如`Manufacturer`、`CarDealer`等，那该怎么办呢？？

正如您已经知道的，代码重复的迹象无处不在，而且很难闻！说实话，除非我们真的需要自定义方法，否则使用命名元组可能会更好。

作为坏消息的传递者，现实生活中往往不是这样。

# 输入数据类别

在 Python 3.7 中引入的[数据类(](https://docs.python.org/3/library/dataclasses.html) `[dataclasses](https://docs.python.org/3/library/dataclasses.html)` [)](https://docs.python.org/3/library/dataclasses.html) 为我们提供了一种简单的方法，使我们的类对象不那么冗长。简而言之，数据类只是帮助我们抽象大量样板代码的常规类。

要用数据类重写我们前面的例子，我们只需用`@dataclass`修饰我们的基本类:

Data 类最好的部分是它自动生成类中的公共 [Dunder 方法](https://www.geeksforgeeks.org/dunder-magic-methods-python/)，如`__repr__`和`__eq__`，消除了所有重复的代码。

## 自定义数据类

1.  在某些情况下，我们可能需要定制我们的数据类字段:

2.为了在我们新创建的数据类中覆盖`__init__`之后发生的事情，我们可以声明一个`__post_init__`方法。例如，我们可以很容易地根据汽车的初始化条件覆盖汽车的价格:

3.为了使我们的数据类不可变，我们只需添加`@dataclass(frozen=True)`作为装饰器。

4.数据类的另一个好的用例是当我们需要处理嵌套字典的时候。下面是一个数据类可以做什么的简单例子:

5.最后，如果不明显的话，数据类也支持继承，因为它们的行为就像我们以前的普通类一样。

# 那么，什么时候使用数据类呢？

## 与命名元组

数据类的使用最常与命名元组的使用相比较。在很大程度上，数据类提供了相同的优势，如果不超过命名元组的话。

在需要[解包](https://www.w3schools.com/python/python_tuples_unpack.asp)变量的情况下，你可能会考虑使用命名元组。

## 对比字典

当我们的字典有一组固定的键，它们对应的值有固定的类型时，使用数据类几乎总是更好。

简而言之，经验法则相当简单，如果你创建一个字典或者一个主要由底层数据的属性组成的类，使用数据类。这可以节省你很多时间。

最后，数据类还保留了每个属性的类型信息，这是一个巨大的附加优势！

# 结束语

再说一遍，在 Python 中创建常规类没有任何问题。然而，这可能意味着仅仅为了设置我们的类实例就要编写大量重复的样板代码。

总结一下我们所经历的，数据类很棒，因为它:

*   节省时间并减少代码重复
*   提供更多的灵活性，它可以是可变的或不可变的
*   支持继承
*   允许自定义和默认值

不要误解我。并非 Python 中的每个类都需要是数据类。数据类不是灵丹妙药。

在大多数情况下，我们应该永远记住，如果没有必要，我们不应该把事情复杂化。只要我们不是在处理过于复杂的事情，一本好的旧字典可能就能胜任。

感谢您的阅读！

## [最初发表于 jerrynsh.com](https://jerrynsh.com/all-you-need-to-know-about-data-classes-in-python/)

[](/everything-you-need-to-know-about-context-managers-in-python-f83556fbdfb) [## 关于 Python 中的上下文管理器，您需要知道的一切

### 通过示例了解 Python 中“with”语句背后的魔力

better 编程. pub](/everything-you-need-to-know-about-context-managers-in-python-f83556fbdfb) [](/how-to-write-clean-code-in-python-5d67746133f2) [## 如何用 Python 写干净的代码

### 使用 Python 示例编写干净代码的 3 个技巧

better 编程. pub](/how-to-write-clean-code-in-python-5d67746133f2)