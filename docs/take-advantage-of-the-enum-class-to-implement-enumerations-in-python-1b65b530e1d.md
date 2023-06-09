# 利用 Enum 类在 Python 中实现枚举

> 原文：<https://betterprogramming.pub/take-advantage-of-the-enum-class-to-implement-enumerations-in-python-1b65b530e1d>

## 轻松创建自己的枚举

![](img/fd586de3c1f7ae525d48d12e7a88d048.png)

[奥列格·拉普捷夫](https://unsplash.com/@snowshade?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

如果您有使用一些常见现代语言的编程经验，比如 Java、Swift 或 Kotlin，您应该熟悉枚举。简单地说，枚举就是你如何在同一个保护伞下组织一组密切相关的成员。

下面用 Kotlin 写的枚举向你展示了我们在`Direction`的概念下组织了四个方向，每个成员就是一个方向:

方向计数

除了方向之外，颜色、国籍、季节、工作日和许多其他具有离散成员的信息都可以作为枚举来实现。您可能知道，枚举给我们带来了更容易处理相关概念的好处。因此，当我们遇到这些数据时，我们应该考虑枚举。

在大多数编程语言中，枚举通常作为数据结构的一种基本形式来表达，这些主题在一个人学习的早期就出现了。但是，由于某些原因，当人们在学习 Python 时，这一点很少被讨论。具体原因我不知道，一个可能的原因是在 Python 中，枚举不是一个内置的数据结构(不像`bool`、`str`、`int`、`list`)。因此，有些人试图使用自定义类来模仿枚举的行为，如下所示:

使用常规类在 Python 中枚举

然而，用常规类实现枚举并不具备您可能想到的其他编程语言中枚举的所有特性。一个这样的问题如下所示。本质上，这种实现是通过利用 Python 类的属性进行“黑客攻击”。因此，如果您检查类型，您会发现它的行为并不理想。在这种情况下，类型是`int`，它揭示了属性的类型。换句话说，他们不被视为某个群体的成员。相反，它们只是类的独立属性:

使用常规类枚举的潜在问题

在本文中，我们将通过利用`[enum](https://docs.python.org/3/library/enum.html)`模块来学习定义真正的工作枚举类的正确方法，该模块是 Python 标准库的一部分。因此，一旦在计算机上安装了 Python，就可以使用`enum`模块来创建枚举了。

# 声明一个枚举类

在 Python 中声明一个枚举类实际上是非常容易的。为此，您只需通过从`enum`模块中子类化`Enum`类来创建自己的类，如下面的代码片段所示:

Python 中方向的枚举

语法非常简单。在类定义中，我们列出了成员及其相关值——就像将它们作为属性列出来一样。上面的代码还向您展示了您可以通过使用分号在一行中分隔成员来声明您的成员，尽管我建议使用前一种方式，即每行定义一个成员，因为这将增强代码的可读性。

虽然我们通常不太关心这些枚举成员的原始值，但有一点需要注意的是，您不必为它们的原始值使用整数。例如，如果您愿意，可以使用字符串。另外，当我们使用整数时，这些数不一定是递增的。这里有一些简单的代码向您展示这些特性:

原始值的灵活性

# 使用和检查单个成员

要使用单个成员，我们只需像访问枚举类的属性一样访问它们。然而，`Enum`班不同于普通班。这些属性被认为是枚举的成员，正如它们应该的那样。有了这个区别，我们就可以使用自省功能(例如`type`和`isinstance`)。一些相关的代码如下所示:

使用成员

如您所见，枚举类的这些“属性”属于该类的类型。当我们检查实例时，对象的类确实是枚举类— `Direction`。它们与我们对枚举类的预期完全一样。

对于每个成员，它都有附加属性。有用的包括`name`和`value`，它们表示枚举成员的名称及其相关值、整数或字符串(如果您这样定义的话)。

名称和值

通过理解成员的价值，我们可以用它做一些有趣的事情。例如，假设我们从一个 web HTTP 请求中得到一个 JSON 对象响应，它使用一个数值(例如 2)来表示方向。有了这个整数，我们可以创建一个`Direction`实例成员。一旦创建了该成员，就可以很容易地将它用于各种其他操作:

用原始值创建成员

如上所示，我们使用获取的值 2 创建了一个`Direction`成员。有了创建的实例，我们可以进行非常简单的比较，正如所料，当与东方向比较时，提取的方向被评估为`True`。值得注意的是，我们在这里通过使用`is`关键字来使用身份比较，因为您可以将成员视为枚举类的单一实例对象。然而，如果使用比较值的`==`进行等式比较，您仍然会得到有效的比较结果。

顺便提一下，如果您试图从一个无效的值(例如，我们示例中的> 4)创建成员，您会遇到一个异常，如下所示:

数值误差

# 迭代成员

我们经常需要迭代枚举类的所有成员。下面的代码显示了枚举类最基本的迭代形式:

For 循环

如您所见，我们可以简单地在 for 循环中使用类名，这表明枚举类本身是可迭代的(即可以迭代的 Python 对象)。因此，我们也可以从类名中创建一个成员列表，因为它是一个 iterable。

方向列表

与该迭代相关的其他高级知识是使用特殊的方法`__members__`，该方法将检索枚举类的映射，其名称及其成员存储为键-值对。请注意，映射与字典不是完全相同的概念，尽管它们听起来很相似。映射是一个比字典更通用的术语。

成员项目

# 定义方法

虽然基类`Enum`不同于其他常规类，因为它专门将其属性作为成员来处理，但是我们仍然可以像其他类一样为该类定义方法。这使我们有机会对枚举类进行更强大、更灵活的操作。

假设我们想找出地图上各个方向的角度。我们可以实现一些相关的功能，如下所示:

自定义方法

如上图所示，`angle()`函数被定义为一个实例方法，它将计算某个方向的度数，北方为 0 度。为了向您展示概念证明，我还声明了一个静态方法`angle_interval()`,它计算两个方向之间的角度差。上述代码片段中声明的类后面的示例显示了这些方法的用法。

# 功能 API

前面几节回顾了枚举的基于类的实现。为了使`Enum`更加灵活，Python 提供了用于创建枚举的功能 API。如果你不知道什么是函数式 API，这里有一个直观的解释。在前面的章节中，我们通过定义`Enum`类的子类创建了枚举成员。正如您可能知道的，它主要是一种面向对象的编程(OOP)风格。基本上，我们使用类和对象来处理成员。相比之下，函数式 API 采用函数式方法。我们简单地通过调用一个函数来创建枚举。

还是很困惑？先来看一些相关代码。为了使比较更加直观，我将使用函数式 API 方法实现相同的方向成员。如下所示，我们将只使用`Enum`函数——更准确地说，它应该被称为工厂函数。像`[namedtuple](https://medium.com/swlh/empower-a-lightweight-python-data-structure-from-tuples-to-namedtuples-ca4abddd8ef6)`函数一样，这些函数创建了一个新的类:

使用函数式 API 枚举

因为我们没有使用类声明接口来实现这个枚举类，那么有没有可能为这个枚举类(使用 functional API 创建的那个)定义函数？答案是肯定的，因为我们可以利用 Python 中一个叫做 [monkey patching](https://medium.com/better-programming/what-are-duck-typing-and-monkey-patching-in-python-2f8e3d6b864f) 的特性。简单地说，这是一种允许您在运行时创建和/或修改现有模块或类的行为的技术。让我们看下面一个简单的例子:

猴子补丁

如上所示，我们首先创建了一个函数，并将该函数分配给了`DirectionFunctional`类的`angle`属性。值得注意的是，这个属性被认为是一个实例方法，因为`angle`函数的第一个参数是类名。与类中声明的函数中的[自参数](https://medium.com/better-programming/unlock-the-4-mysteries-of-self-in-python-d1913fbb8e16)相同。

在猴子修补之后，我们能够得到向南方向的角度，如预期的那样是 180 度。

# 结论

在本文中，我们回顾了如何用 Python 创建枚举。这些功能主要是通过在`enum`模块中创建`Enum`类的子类来实现的。这里快速回顾一下要点。

*   与常规类不同，`Enum`类的属性将被视为该类的独立成员。
*   这些成员有名称和值，可以是整数(它们是默认的)、字符串或其他数据类型。
*   方法可以用枚举类来声明，这允许我们创建额外的操作来更容易地处理成员。
*   我们可以迭代这个类的所有成员，它本身就是一个可迭代的。我们还可以利用类是 iterable 来创建这些成员的列表。
*   作为一个灵活的特性，我们可以使用函数式 API 在运行时创建一个枚举类。此外，我们可以使用 monkey patching 向类中添加额外的方法。

感谢你阅读这篇文章。如果你想了解更多关于 Python 中枚举的内容，可以参考[官方文档](https://docs.python.org/3/library/enum.html)。