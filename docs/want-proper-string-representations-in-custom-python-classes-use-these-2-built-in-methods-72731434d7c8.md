# 想要在自定义 Python 类中使用正确的字符串表示吗？使用这两种内置方法

> 原文：<https://betterprogramming.pub/want-proper-string-representations-in-custom-python-classes-use-these-2-built-in-methods-72731434d7c8>

## 提高代码的可读性

![](img/45d01654f6b2ceda19c827f51872e5a2.png)

安德鲁·海姆尔(安德鲁夫)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

随着项目的增长，您将不可避免地定义定制类。在这些类中，您可以定义各种执行特定操作的方法来满足业务需求。

有时，您或其他用户需要通过这些自定义实例的字符串表示来检查它们。

# repr 和 str 函数概述

有两个与 Python 对象的字符串表示相关的基本内置函数:`repr`和`str`。这两个函数都提供了 Python 对象的可打印字符串表示。我们可以在一些内置数据类型中观察到这个特性，如下所示。

内置类的字符串表示

在这些例子中，这两个函数创建相同的字符串，有意义地表示被检查的对象。如果你仔细观察，你会注意到`str`数据类型的字符串表示是不同的，我们将在本文后面讨论这种差异。

显然，这些内置的数据结构支持这两种功能，但是这些打印输出不能告诉我们幕后发生了什么。接下来就来探索一下。

# 重写特殊方法

为了观察底层的实现机制，让我们创建一个简单的定制类。

```
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age
```

有了这个定制类的基本结构，我们可以看看`repr`和`str`函数揭示了这个简单类的什么。

```
>>> student = Student(“John”, 15)
>>> print(f”repr: {student!r}”)
repr: <__main__.Student object at 0x0000023B519238E0>
>>> print(f”str: {student!s}”)
str: <__main__.Student object at 0x0000023B519238E0>
```

与内置数据结构不同，在这个自定义类实例上调用`repr`和`str`不会产生任何有意义的东西，除了显示它的类和内存地址。

为了定义自定义类的正确字符串表示，我们需要覆盖两个特殊的方法:`__repr__`和`__str__`，如下所示。重要的是，这两种方法都应该返回字符串值，因为它们应该为自定义实例提供字符串表示。目前，这些字符串只是占位符，我们将在后面讨论这些方法的最佳实践实现。

覆盖 str 和 repr

特殊方法是在名字前后有双下划线的函数(例如，自定义类中的`__init__`),有些人把它们称为 magic 或 dunder 方法。值得注意的是，它们旨在由 Python 解释器调用，以在幕后实现特定的操作。换句话说，我们通常不直接调用特殊方法。例如，我们通过调用 ClassName()创建一个类实例对象，这个类实例对象调用 Python 的`__init__`方法。

使用更新后的类，我们可以观察到通过调用`__repr__`和`__str__`特殊方法来调用`repr`和`str`方法的效果。

```
>>> student = Student(“John”, 15)
>>> print(f”repr: {student!r}”)
repr: Student __repr__ string
>>> print(f”str: {student!s}”)
str: Student __str__ string
```

# 为 __repr__ 和 __str__ 返回正确的字符串

如上所示，当前的字符串表示不是这两个特殊方法所需的输出值。你可能想知道我们应该为它们返回什么字符串。

在自定义类中正确实现这两个方法需要理解它们的区别。一般来说，我们定义`__repr__`方法是为了开发(例如，调试)，而`__str__`方法是为了显示对象的有用信息。

记住这个区别，让我们在相关的讨论之前看看一个合适的实现。

repr 和 str 的正确覆盖

有了更新后的类，我们现在可以看到这两种方法的字符串表示。

```
>>> student = Student(“John”, 15)
>>> print(f”repr: {student!r}”)
repr: Student(‘John’, 15)
>>> print(f”str: {student!s}”)
str: Student Name: John; Age: 15
```

这两种方法之间的区别对于 Python 如何使用它们有着重要的意义。对于`__str__`方法，就其具体实现而言，您有更多的自由。经验法则是，它应该向最终用户显示关于对象数据的有用信息。与此相关，需要注意的一点是，每当我们使用 print 函数检查对象时，都会调用`__str__`方法。

```
>>> print(student)
Student Name: John; Age: 15
```

相比之下，当在交互式 Python 控制台中输入一个对象时，会自动调用`repr`方法。

```
>>> student
Student(‘John’, 15)
```

打印出的字符串的格式很重要，因为我们可以重新创建另一个实例，该实例与当前实例具有相同的属性值。这就是为什么我们说`repr`方法是为开发而设计的。我们可以观察到这种效应:

```
>>> another_student0 = Student(‘John’, 15)
>>> another_student0
Student(‘John’, 15)
>>>
>>> student_repr = repr(student)
>>> another_student = eval(student_repr) #B
>>> another_student
Student(‘John’, 15)
```

注意，在`__repr__`方法中，我们将 name 属性的转换格式指定为字符串的表示形式。这种转换格式是必要的，因为如果我们忽略它(即`f”Student({self.name}, {self.age})”)`)，`Student`实例将具有以下表示:`Student(John, 15)`，由于语法错误，它不能用于重新创建相同属性的新实例。

从字符串表示重新创建的相同原则也适用于内置数据结构。记住，`repr`和`str`函数为一个字符串实例返回稍微不同的字符串:前者产生一个带引号的字符串，而后者产生一个不带引号的字符串。

```
>>> name = “John”
>>> print(f”repr: {name!r}”)
repr: ‘John’
>>> print(f”str: {name!s}”)
str: John
```

引号的使用有很大的不同，因为带引号的字符串可以用来实例化一个字符串对象，而不带引号的则不能，因为它将被解释为变量。这再次反映了这样一个事实，即使用`repr`方法生成的字符串可以用来创建另一个具有相同值的实例。

```
>>> str(‘John’)
‘John’
>>> str(John)
Traceback (most recent call last):
    File “<input>”, line 1, in <module>
**NameError**: name ‘John’ is not defined
```

# 结论

在自定义类中，`__repr__`方法的实现应该模拟我们如何使用初始化方法从被检查的对象中重新创建实例对象。

基本目的是以明确的方式解释对象是什么。因为从`repr`方法生成的字符串应该表示一个我们可以重新构造类似对象的文本，所以由`repr`生成的任何字符串都应该有引号，以使它们成为有效的 Python 字符串文字。

在自定义类中，`__str__`方法的实现应该为最终用户提供有用的信息。主要目的是以可读的方式解释对象包含什么数据。

建议为自定义类实现这两种方法。但是，如果您希望只实现一个，请覆盖`__repr__`方法，因为当`__str__`方法没有实现时，Python 将使用`__repr__`方法。