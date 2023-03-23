# 如何在 Python 中检查对象？需要了解的 9 项功能

> 原文：<https://betterprogramming.pub/how-to-inspect-objects-in-python-9-functions-to-know-b1b152fe6bde>

## 大大提高你的发展

![](img/47be2e14a70d9d53c1f40543995dcbc3.png)

由[哈德逊·辛慈](https://unsplash.com/@hudsonhintze?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Python 是一种典型的面向对象语言，其中一切都是围绕对象组织的——内置和自定义的类、实例、模块和函数。当我们处理对象时，经常需要检查它们是什么，以便我们可以相应地使用它们。

在本文中，我将介绍九个函数来从各个方面检查对象。重要的是，在适用的情况下，我还将讨论与每个功能相关的特性。

事不宜迟，让我们开始吧。

# **1。打印**

在开发阶段，我们可以使用`print`函数在控制台中检查一个对象或其特定属性。以下代码显示了内置数据类型的一些实例的打印输出消息:

```
numbers_tuple = (1, 2, 3)
numbers_dict = {1: "one", 2: "two", 3: "three"}>>> print(numbers_tuple)
(1, 2, 3)
>>> print(numbers_dict)
{1: 'one', 2: 'two', 3: 'three'}
```

如上所示，打印的消息类似于原始数据的样子。但是，当您打印自定义类的实例时，它可能看起来有所不同:

```
class Student:
    def __init__(self, name, student_id):
        self.name = name
        self.student_id = student_id>>> student = Student("John Smith", 19302)
>>> print(student)
<__main__.Student object at 0x7fce50a3fd30>
```

它不显示实例存储的数据。相反，它只告诉你它是位于指定内存地址的`Student`的实例对象。我们如何让`print`函数与一个自定义类一起工作？您需要在类中定义`__str__`方法:

```
class Student:
    def __init__(self, name, student_id):
        self.name = name
        self.student_id = student_id def __str__(self):
        return f"Student #{self.student_id}: {self.name}">>> student = Student("John Smith", 19302)
>>> print(student)
Student #19302: John Smith
```

对于`__str__` 方法，我们返回一个描述实例对象的字符串值。在这种情况下，关键信息是姓名和学生 ID 号。实现`__str__`后，在这个实例对象上调用 print 会打印出更有意义的数据。

# **2。类型**

内置的`type`函数返回实现该对象的类的名称。对于一个类的实例对象，你会得到相应的类。

```
>>> type(numbers_tuple)
<class 'tuple'>
>>> type(numbers_dict)
<class 'dict'>
```

当您将实例的类型与特定类型进行比较时，您应该使用`is`而不是`==`作为比较。下面是一些例子:

```
>>> type(numbers_tuple) is tuple
True
>>> type(numbers_dict) is dict
True
```

请注意，尽管使用`==`会得到相同的评估结果，但是类型被认为是单例对象，您应该使用 identity test ( `is`)。

您可以将实例的类型与特定的类型进行比较，如上所示。然而，这通常不是最好的主意。相反，当我们想要找出一个对象是否是一个类的实例时，我们更喜欢使用下一个函数— `isinstance`。

# 3.isinstance

`isinstance`函数返回一个布尔值，表明一个对象是一个类的实例。下面是一些例子:

```
>>> isinstance(numbers_tuple, tuple)
True
>>> isinstance(numbers_dict, dict)
True
```

值得注意的是，您不能只指定一个类—为了方便起见，如果需要，您可以指定多个类，如下所示:

```
>>> isinstance(numbers_tuple, (list, tuple, set))
True
```

请注意，如果对象是指定类中任何一个类的实例，那么上面代码的等价物如下所示，求值结果为`True`。

```
isinstance(numbers_tuple, list) or isinstance(numbers_tuple, tuple) or isinstance(numbers_tuple, set)
```

你更喜欢使用`isinstance`而不是`type`比较的一个重要原因是它可以开箱即用地处理子类化。也就是说，当您创建子类的实例对象时，`isinstance`也会评估`True`，如果您检查该对象是否是超类的实例，如下所示:

```
class CollegeStudent(Student):
    pass>>> college_student = CollegeStudent("John Smith", 19302)
>>> isinstance(college_student, Student)
True
```

相比之下，`type`函数不能直接处理涉及子类化的比较，如下所示，因此不够灵活。

```
>>> type(college_student) is Student
False
```

# 4.问题分类

既然我们刚刚谈到了子类化，你应该知道我们可以使用`issubclass`来确定一个类是否是超类的子类。用法应该非常简单明了；它使用以下内容:

```
>>> issubclass(CollegeStudent, Student)
True
```

这个函数唯一可能的棘手部分是哪个参数先出现——子类还是超类。我的建议是你简单地造一个句子:我想用`issubclass`来检查 a 是否是`b`的子类，用这个顺序，我们会有如下:

```
issubclass(a, b)
```

# 5.请求即付的

我们一直在谈论课程。您可能知道，类是一种可调用的，这意味着我们可以使用调用操作符——一对括号——来调用类。一般来说，如果一个对象可以被调用，我们说它是可调用的。为了检查一个对象是否可调用，我们可以使用`callable`函数。

```
>>> callable(tuple)
True
>>> callable(Student)
True
```

类是可调用的原因是我们可以调用类来构造实例。另一种重要的可调用类型是函数，如下所示:

```
def triple(number):
    return number * 3>>> callable(triple)
True
```

除了类和函数之外，您甚至可以通过覆盖`__call__`方法使自定义类的实例可调用，如下所示:

```
class Student:
    def __init__(self, name, student_id):
        self.name = name
        self.student_id = student_id def __call__(self):
        print(f"{self.name} completed the semester.")>>> student = Student("John Smith", 19302)
>>> student()
John Smith completed the semester.
```

# 6.易消化的

既然我们已经讨论了 callable，那么讨论另一个表也是合理的。Hashable 描述一个对象是否可以被散列。但是`hash`是什么意思呢？`Hash`意味着您使用一个预定义的函数(称为哈希函数)将一个对象转换成另一种表示，称为哈希值或简称为哈希。在 Python 中，哈希值是整数。您可以使用`hash`函数获得哈希值，如下所示:

```
>>> hash(numbers_tuple)
529344067295497451
```

请注意，这个数字在您的计算机上可能会有所不同。

值得注意的是，并不是所有的对象都是可哈希的。对于内置数据类型，可变对象，如`dict`，是不可改变的。

```
>>> hash(numbers_dict)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'dict'
```

原因是哈希值与其原始数据一一匹配。对于可变对象，如果它们是可散列的，假设你最初获得一个散列值。后来，您更新了对象，更新后的对象应该具有不同的哈希值，这违反了一对一的要求。因此，为了解决这个难题，Python 要求我们不能散列可变对象。

因此，要检查对象的可散列性，您可以简单地调用对象上的`hash`函数。如果它是可散列的，您将获得散列值——否则，您将遇到一个`TypeError`异常。

如果您想直接检查对象的哈希能力，而不依赖于可能的异常引发，您可以使用下面的替代方法:

```
from collections.abc import Hashable>>> isinstance(numbers_tuple, Hashable)
True
>>> isinstance(numbers_dict, Hashable)
False
```

`Hashable`代表一个抽象类，它为那些实例对象可以被散列的类定义了一个通用行为。

# 7.身份证明（identification）

当你想获得一个对象的标识时，你可以使用`id`函数，它返回该对象的内存地址。参见下面的一些例子:

```
>>> id(numbers_tuple)
140524092985536
>>> id(numbers_dict)
140522741441408
```

在任何给定的时间，每个内存地址应该唯一地标识一个对象。这就是为什么您可以使用内存地址进行身份检查。如果两个对象有相同的内存地址，它们就是同一个对象:

```
if id(a) == id(b):
    assert a is b
```

除了获取对象的身份之外，`id`函数对于了解浅拷贝和深拷贝之间的区别也很有用，这在处理可变对象(比如列表)时会很棘手。

对于浅层副本，您将获得最外层列表的副本，而不是内部列表的副本。考虑下面的例子:

```
from copy import copy, deepcopynumbers = [[1, 2, 3], [4, 5, 6]]
numbers_copy = copy(numbers)assert id(numbers) != id(numbers_copy)
assert numbers is not numbers_copy
```

在这里，我在`copy`模块中使用了`copy`函数，而不是 list 的`copy`方法，因为`copy`模块不仅可以复制列表，还可以复制对象，具有更好的泛化能力。对于浅层复制，不会复制内部列表。观察下面的效果:

```
assert id(numbers[0]) == id(numbers_copy[0])
assert id(numbers[1]) == id(numbers_copy[1])
```

相比之下，深层副本将为除最外层列表之外的内部列表创建不同的副本:

```
numbers_deepcopy = deepcopy(numbers)assert id(numbers) != id(numbers_deepcopy)
assert id(numbers[0]) != id(numbers_deepcopy[0])
assert id(numbers[1]) != id(numbers_deepcopy[1])
```

# 8.哈萨特

如果想检查一个对象是否有特定的属性，可以使用`hasattr`函数。下面是一些例子:

与这个功能相关的是编码风格的选择:LBYL(三思而后行)vs. EAFP(请求原谅比请求许可容易)。当你使用`hasattr`来检查一个对象的特定属性时，这是 LBYL 风格——我在这个对象上做这样的操作安全吗，像下面这样？

```
if hasattr(obj, "specific_attr"):
    # do something with the obj
else:
    # can't do such with the object
```

相比之下，当您使用 EAFP 时，您只需直接运行操作，并在无法对对象执行此类操作时处理可能的异常，如下所示:

```
try:
    obj.specific_attr() # suppose it's a method call
except AttributeError:
    # can't do such with the object
```

如上所示，我们使用了`try…except…`语句，其中的`try`子句试图运行可能不工作的代码。如果出现任何异常，在本例中是一个`AttributeError`，我们在`except`子句中处理它。

一般来说，我们更喜欢使用 EAFP 风格，因为它通常比 LBYL 更快，后者需要在执行操作之前进行条件检查。相比之下，EAFP 只是运行它，而不需要检查条件。

# **9。方向**

如果您对了解一个对象的所有方法和属性感兴趣，您可以使用`dir`函数。当你处理一个新类型的对象时，比如一个第三方库中的对象，并且你不知道什么方法和属性可用于这样的类型，你可以使用这个。

比如我们经常用熊猫做数据处理，关键数据结构是`DataFrame`，用一个`DataFrame`对象就能发现我们能做什么。请注意，我只是提供了一个属性的缩写列表，因为它太长了！

```
>>> import pandas as pd
>>> df = pd.DataFrame()
>>> dir(df)
['T', '_AXIS_LEN', '_AXIS_ORDERS', '_AXIS_REVERSED', '_AXIS_TO_AXIS_NUMBER', '_HANDLED_TYPES', '__abs__', '__add__', '__and__', '__annotations__', ..., 'value_counts', 'values', 'var', 'where', 'xs']
```

当列表过长时，我们如何利用它？假设我想对数据进行排序，但是您可以简单地过滤包含 sort 的属性，如下所示:

```
>>> [x for x in dir(df) if x.find("sort") >= 0]
['sort_index', 'sort_values']
```

对于一个更短的列表，你只需要查找这两个方法的信息来学习使用`DataFrame`对象排序。

# 结论

对象是任何应用程序的核心。如果您掌握了这些功能，您会发现检查对象和相应地应用特定操作没有问题。

我希望你喜欢这篇文章。