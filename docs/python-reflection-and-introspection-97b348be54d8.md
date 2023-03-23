# Python 反射和自省

> 原文：<https://betterprogramming.pub/python-reflection-and-introspection-97b348be54d8>

## 在代码中，而不是在我们自己身上

![](img/d85b810d4d9865fcb3db59c8df2b0c3a.png)

照片由[雷·轩尼诗](https://unsplash.com/@rayhennessy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在编程中，*自省*是在运行时找出对象信息的能力。*反射*通过允许对象在运行时被*修改*而更进一步。

对我来说，这两个语言特性确实让 Python 变得有趣，并使它有别于不太动态的语言。Python 支持自省和反射，在本文中，我将解释它们是如何工作的。

# 反省

你可以在运行时自省的最基本的信息是一个对象的类型。这可以通过`type()`功能实现。

```
>>> type(1)
<class 'int'>>>> type(1.0)
<class 'float'>>>> type(int)
<class 'type'>
```

这里，解释器告诉我们整数`1`属于`int`，`1.0`属于`float`，而`int`属于`type`。

来自`type()`函数的返回值被称为*类型对象*。类型对象告诉我们参数是哪个类的实例。

我们可以用`isinstance()`巧妙地证实这一点:

```
>>> isinstance(1, int)
True>>> isinstance(int, type)
True
```

类型对象支持`is`操作符，所以我们可以写:

```
>>> type(1) is int
True>>> type(int) is type
True
```

然而，`type()`和`isinstance()`并不直接等价，因为`isinstance()`也考虑对象的基类:

因此，我们可能想一起使用`type()`和`isinstance()`，就像这样:

```
if isinstance(obj, A):
    # do something for all children of A

    if type(obj) is B:
        # do something specifically for instances of B
```

我们可以用 Python 中的其他技术来发现对象，包括:

*   `hasattr(obj,'a')` —如果`obj`具有属性`a`，则返回`True`。
*   `id(obj)` —返回对象的唯一 ID。
*   `dir(obj)` —返回列表中对象的所有属性和方法。
*   `vars(obj)` —返回字典中对象的所有实例变量。
*   `callable(obj)` —如果`obj`可调用，则返回`True`。

我们还可以使用创建时自动添加到对象中的属性来直接访问这些信息。例如:

*   `obj.__class__`为`obj`存储*类型对象*。
*   `obj.__class__.__name__`为`obj`存储*类名*。
*   对于函数对象，`obj.__code__`存储一个代码对象，包含函数中代码的信息。

有关代码对象的更多信息，请参见[Python 文档](https://docs.python.org/3.8/c-api/code.html)。

## 自省示例

将所有这些放在一起，我们可以创建一个简单的`introspect()`函数:

注意我们是如何在函数中使用内省来打印被调用函数的名称(`func.__name__`)和内省参数的名称(`introspect.__code__.co_varnames[0]`)！

输出如下所示:

```
type(obj):              <class '__main__.Test'>                                                                       
id(obj):                139779613404408                                                                               
dir(obj):               ['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__
', '__getattribute__', '__gt__', '__hash__', '__init__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__red
uce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'x', '
y']
vars(obj):              {'y': 2, 'x': 1}                                                                              
callable(obj):          False
```

对于两行代码来说还不错！

## 高级内省

上面概述的工具已经非常强大了。然而，如果你想更深入地研究内省，Python 的 [inspect 模块](https://docs.python.org/3/library/inspect.html)提供了内省活动对象的更多功能。

# 动态修改对象

到目前为止，我们已经讨论了如何在运行时找到关于一个对象的信息。我们现在要学习如何动态地*修改*或者甚至创建新的对象和类！

首先，我们应该知道属性可以在运行时添加到类或对象中。所以，我们可以写:

由于方法只是一种特殊类型的属性，这意味着我们也可以在运行时添加方法。让我们通过动态添加一个`__init__`方法来修改我们的类。

注意我们如何使用`[setattr](https://docs.python.org/3/library/functions.html#setattr)`函数将`A`的`__init__`方法设置为`init`。这使得我们正在设置的属性的名称也可以动态确定。

我们可以通过修改函数的`__code__`属性来进一步理解这个概念。这次通过简单的赋值:

例如，这可用于创建只执行一次的函数:

## 在运行时创建类

我们现在要重温前面提到的`type()`函数，并用它来动态地*创建*一个新类。为此，我们用三个参数调用`type()`:

```
type(name, bases, dict)
```

其中:

*   `name`是我们正在创建的类的名称。
*   `bases`是我们继承的基类元组。
*   `dict`是属性名、属性值对的字典。

因此，最简单的形式是，我们可以写:

```
>>> A = type('A', (), {'x': 1})
>>> a = A()
>>> a.x
1
```

然而，我们可以得到更多的进展。例如，要创建一个完全成熟的类:

```
>>> exec("def init(self):\n\tprint(self.__class__.__name__ + \" created!\")")
>>> A = type('A', (), {'__init__' : init })
>>> a = A()
"A created!"
```

注意，在这里，我们使用 Python 的内置`[exec](https://docs.python.org/3/library/functions.html#exec)`函数从字符串生成我们的类的`__init__`方法。方法定义本身使用`self.__class__.__name__`动态获取类名！

# 最后提醒一句

虽然本文中概述的语言能力是强大的，但是[强大的能力意味着巨大的责任](https://en.wikipedia.org/wiki/With_great_power_comes_great_responsibility)！

这些技术应该尽量少用。过度使用动态编程会使代码更难阅读。在某些情况下，它还会引入安全漏洞，尤其是在动态执行涉及用户输入的情况下。

在运行时动态修改代码有时被称为*猴子补丁*。关于潜在应用和陷阱的更多细节可以在维基百科上找到[。](https://en.wikipedia.org/wiki/Monkey_patch)

感谢阅读！