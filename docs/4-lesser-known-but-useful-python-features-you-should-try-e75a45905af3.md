# 您应该尝试的 4 个鲜为人知(但很有用)的 Python 特性

> 原文：<https://betterprogramming.pub/4-lesser-known-but-useful-python-features-you-should-try-e75a45905af3>

## 在 Python 中总是有新的发现

![](img/9eb815204d97c18394ddfbbe52f62459.png)

[Ben](https://unsplash.com/@benofthenorth?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

Python 有一个最活跃的开源社区。在撰写本文时，Python 包索引(又名 [PyPi](https://pypi.org/) )中现有超过 288，000 个包。它们已被广泛应用于许多领域，包括科学计算、web 开发和机器学习。

每当你看到一篇关于 Python 的文章，你可能会学到一些新东西。今天，我想分享一些有用的 Python 特性，如果特定的场景适用于您，您可以利用这些特性。

# 1.用 pprint 打印更好

`[pprint](https://docs.python.org/3/library/pprint.html)`模块是标准库的一部分。正如它的名字(pretty printer)所示，这个模块的目标是在解释器中以比默认内置`print`函数的输出更容易阅读的更好的格式打印数据。下面的代码片段向您展示了`print()`和`pprint()`函数输出之间的快速比较:

打印与 pprint

正如你所看到的，当我们使用`pprint`时，输出是一种有组织的格式，与`print`函数的标准输出相比，这更容易吸引眼球。

在大多数情况下，您只需要使用`pprint`的默认设置。但是，如果您想要自定义行为，还可以设置其他参数。例如，您可以更改输出的缩进，如下所示:

```
>>> pprint(http_responses, indent=4)
[   {'result': [1, 2, 3], 'status': 200},
    {'result': 'Endpoint not found', 'status': 404},
    {'result': 'API error', 'status': 400}]
```

在底层，`pprint`函数创建一个`PrettyPrinter`实例对象，处理打印作业的实际格式化。

# 2.作为轻量级数据容器的命名元组

如果希望有一个类来管理项目中的数据，可以考虑使用命名元组作为替代方法。不必为自定义类编写一些样板文件，创建一个命名的元组类非常简单:

命名元组示例

*   `namedtuple`是集合模块中可用的工厂功能。
*   作为一个工厂函数，调用这个函数将创建一个类。在我们的例子中，我们创建了名为`Coordinate`的类型。
*   在类型名之后，我们以字符串的形式传递属性列表，该字符串可以是由空格分隔的单个子字符串。或者，属性可以是列表的形式。
*   一旦定义了类，我们就可以为该类创建实例对象。就像其他类一样，我们可以使用点符号直接访问属性。与传统的自定义类不同，命名元组类还支持迭代和解包，因为它的核心是元组子类。相关演示见下文:

命名元组的其他功能

应该注意的是，Python 提供了特殊的功能，允许我们使命名元组更加有用。下面是一些例子:

# 3.在 for 循环中使用 else 子句

我们都知道在条件分支中使用`else`子句。下面是一个简单的例子:

```
if condition:
    # execute when the condition is true
else:
    # execute when the condition is false
```

你知道我们也可以在`for`循环中使用`else`子句吗？让我们先看看代码:

for 循环中的 else 子句

*   `else`子句在第一个`for`循环中执行。
*   第二个`for`循环中不执行`else`子句。
*   `for`循环中的`else`子句的规则是，如果`for`循环中的迭代正常完成，它将被执行。然而，如果`for`循环中的迭代由于`break`语句而中断，它将不会被执行。

你可能想知道在什么场景下我们可以使用这个特性。下面说明了一种可能的情况。虽然示例中没有显示，但是有一点需要注意，如果 iterable 为空，那么仍然会执行`else`子句，这仍然被认为是迭代的完成。

可能情况

# 4.用枚举创建枚举

在很多项目中，我们需要列举同一个概念的相关条目。在 Python 中，我们可以使用`enum`模块实现枚举。以下代码向您展示了一个简单的示例:

列举

*   `Enum`类在`enum`模块中可用。为了创建一个枚举，我们创建了一个`Enum`类的子类。
*   对于每一项，我们指定名称及其相关值。可以像这样检索名称和值:

```
>>> Direction.EAST.name
'EAST'
>>> Direction.EAST.value
1
```

关于枚举还有一些我们应该注意的特性:

*   如果你认为一行对你来说可读性更强，你可以只用一行。或者，您可以利用`Enum`类的功能 API。这两个功能如下所示:

```
class DirectionOneLine(Enum):
    EAST = 1; SOUTH = 2; WEST = 3; NORTH = 4;DirectionFunctional = Enum('Direction', 'EAST SOUTH WEST NORTH')
```

*   关联的值不必是整数。在下面的例子中，我们使用字母作为项目的值。要求是这些值需要是唯一的和恒定的。

*   枚举类是可迭代的。我们可以在一个`for`循环中使用它，如下所示。同样，我们可以检查成员。

*   我们可以使用适当的值来构造一个项目。但是，如果值不正确，则不能创建项目:

# 结论

在本文中，我们回顾了可以在自己的项目中尝试的四个 Python 特性。这里有一个快速回顾:

*   要查看 Python 对象的正确输出，可以使用`pprint`来生成格式更好的输出。
*   我们可以使用命名元组创建一个简单的类作为数据容器来管理项目中的数据模型。
*   除了`use if…else`语句，我们还可以在`for`循环中使用`else`子句。如果`for`循环中的迭代正常完成，则执行`else`子句。
*   通过子类化`Enum`类，我们可以方便地创建一个枚举类。