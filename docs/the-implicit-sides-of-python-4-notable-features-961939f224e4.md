# Python 隐含的一面——4 个显著的特性

> 原文：<https://betterprogramming.pub/the-implicit-sides-of-python-4-notable-features-961939f224e4>

## 知道引擎盖下发生了什么

![](img/94b7dc8ea92b676d9313e35acc30597a.png)

[贝尔哈德·拉明](https://unsplash.com/@lamine__belhadj?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

一个众所周知的 Python 技巧是在你的 Python 控制台中展示 Python 的禅宗。在《Python 的禅》一书中，作者 Tim Peters 为编写 Python 程序创造了 19 条指导原则。第二个原则是“显式比隐式好”，这意味着当我们编写 Python 代码时，它应该足够显式，无需过多的口头解释就能说出它做了什么。

然而，许多 Python 特性的实际实现涉及到幕后的隐式操作。在本文中，我想回顾五个这样的特性。

# 1.实例方法的调用

在 Python 类中，我们声明实例对象可以调用的实例方法。您可能知道，这些实例方法都有一个额外的参数，通常称为`self`。这个`self`参数指的是将要调用该方法的实例对象。

自定义类别

如您所见，`speak`方法包含了`self`参数，使其成为一个实例方法。然而，当我们对一个实例对象使用这个方法时，我们不会显式地对这个参数做任何事情。相反，实例对象不带任何参数调用此函数，如下所示:

```
>>> person0 = Person()
>>> person0.speak()
Speak method is called by <__main__.Person object at 0x11a8b04c0>
```

在幕后(或隐式)，实例方法的调用涉及属性查找链，这导致通过包装器使用类的`speak`函数。实际上，代码`person0.speak()`被执行为`Person.speak(person0)`。如果您有兴趣更深入地理解底层机制，您可以看看在[官方文档](https://docs.python.org/3/howto/descriptor.html#invocation-from-an-instance)中说明的实例对象的调用链。

# 2.特殊方法

在 Python 中，我们使用两对下划线，一对在函数名之前，另一对在函数名之后，来表示一个特殊的方法。有些人把这些称为魔术方法或邓德尔斯。Python 中有许多特殊的方法，它们在适用的情况下被隐式或自动调用。

例如，当创建一个对象时，调用最著名的特殊方法`__init__`。值得注意的是，我们自己并不直接调用`__init__`。相反，我们使用类名来间接调用这个方法。

更一般地说，许多内置函数(例如`len`、`callable`)都有它们相关的特殊方法，允许你覆盖它们来定义你的类中的定制行为。让我们考虑下面这个微不足道的例子:

在重写特殊方法之前

现在，让我们尝试执行两个操作——这两个操作都不起作用。请注意，这对括号被称为调用操作符(即调用某些东西，如调用函数):

```
>>> person1 = Person1("John Smith", 38)
>>> len(person1)
Traceback (most recent call last):
  File "<input>", line 1, in <module>
TypeError: object of type 'Person1' has no len()
>>> person1()
Traceback (most recent call last):
  File "<input>", line 1, in <module>
TypeError: 'Person1' object is not callable
```

为了使它们工作，我们需要覆盖该类的底层特殊方法，如下所示:

重写特殊方法后

*   我们覆盖了`__len__`方法。通过这次更新，我们可以看到，调用内置的`len()`方法隐式地调用了这个特殊的方法。

```
>>> person1 = Person1("John Smith", 38)
>>> len(person1)
__len__ is invoked
38
```

*   以类似的方式，我们覆盖了`__call__`方法，我们可以看到调用操作符现在使用实例对象。更重要的是，底层的`__call__`特殊方法被调用。或者:

```
>>> person1()
__call__ is invoked
'John Smith'
```

*   在一般意义上，这些操作相当于使用底层的特殊方法。例如，`len(person1)`实际上就是`person1.__len__()`。Python 只是隐式地为我们调用底层的特殊方法。

# 3.装饰函数

装饰器是高阶函数，它向被装饰函数添加额外的行为，而不改变被装饰函数的实际实现。考虑下面的例子来理解装饰者做什么:

装饰示例

*   装饰器被命名为`alert`，它简单地告诉我们被装饰的函数被调用。
*   我们通过把装饰器放在被装饰的函数上面来使用它。当我们调用修饰函数时，我们会看到类似这样的内容:

```
>>> greeting("John")
Alert: greeting is being called.
Hello, John!
```

修饰所隐含的是，被修饰的函数实际上是由修饰函数创建的闭包。换句话说，函数修饰的过程实际上如下:

```
>>> def greeting1(person):
...     print(f"Hello, {person}!")
>>> # Create the closure to provide decoration
>>> greeting1 = alert(greeting1)
>>> greeting1("John")
Alert: greeting1 is being called.
Hello, John!
```

*   第一步是像我们通常做的那样定义函数。
*   第二步，将此函数发送给更高阶的函数(即`alert`)进行修饰。如您所见，返回值是一个内部函数，它定义在`alert`函数体内。

因此，当我们使用装饰特性时，我们依赖 Python 隐式地将即将被装饰的函数发送给装饰器，并将包装后的函数分配给原始函数名。

# 4.迭代次数

开发人员通常通过简单地插入一个`for`循环来使用迭代来避免重复代码。以下代码向您展示了一个简单的迭代示例:

```
# A simple iteration
numbers = [1, 2]
for a in numbers:
    print(a)
```

更一般地，迭代具有以下一般格式:

```
for item in iterable:
    pass
```

但是迭代期间到底发生了什么？为了更深入地了解这个特性，让我们考虑下面的自定义类:

自定义可迭代

*   在 Python 中，iterables 是可以迭代的对象，它们实现了特殊的`__iter__`方法。
*   然而，为了使 iterables 可以在一个`for`循环中使用，我们必须实现`__next__`方法，该方法使迭代器让 Python 知道如何检索下一项。

让我们看看如何在一个`for`循环中使用这个自定义类:

使用自定义迭代器进行迭代

*   执行这个`for`循环的第一个值得注意的事情是通过调用`__iter__`方法隐式创建一个迭代器，正如打印输出所证明的。
*   在迭代过程中，每次迭代都会隐式调用`__next__`方法来检索下一个可用项。
*   在迭代完所有可用的项目后，我们通过引发`StopIteration`异常来完成迭代。然而，我们在控制台中看不到这样的异常。这是因为 Python 已经在常规的`for`循环迭代中隐式地为我们处理了异常。

# 结论

在本文中，我们回顾了四个重要的 Python 特性，它们涉及一些隐含的操作。了解这些机制将让我们更好地掌握 Python 的特性。这里有一个快速回顾:

*   实例方法的调用包括创建一个中间包装器，该包装器使用类中定义的相应函数。此外，调用对象也被发送给函数进行评估。
*   许多函数(如`len`)都有相应的特殊方法负责这些行为。
*   函数修饰包括创建一个内部函数，它是在 decorator 函数中定义的。本质上，修饰函数是一个闭包。
*   迭代涉及迭代器的创建，两个特殊的方法(`__iter__`和`__next__`)在执行迭代时很重要。此外，Python 隐式地为我们处理了`StopIteration`异常。