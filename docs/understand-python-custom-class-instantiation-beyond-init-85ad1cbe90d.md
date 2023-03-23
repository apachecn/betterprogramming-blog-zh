# 了解 Python 自定义类实例化，超越 __init__()

> 原文：<https://betterprogramming.pub/understand-python-custom-class-instantiation-beyond-init-85ad1cbe90d>

## Python 如何实例化自定义类对象

![](img/f7e2ee71a3813be3b67417b323602679.png)

[安德烈·诺沃亚](https://unsplash.com/@andrenoboa?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/modernism?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 介绍

创建自定义类允许我们定义新类型的对象，这些对象具有特定于我们工作需要的特定属性和功能。在大多数情况下，我们在自定义类中覆盖`__init__()`方法，为我们正在创建的实例对象设置所需的初始属性。这里有一个简单的例子:

```
>>> class Person:
...     def __init__(self, name, age):
...         self.name = name
...         self.age = age...     def greet(self, other):
...         print(f'{self.name} says hello to {other}.')
...
```

在上面的代码片段中，`__init__()`方法作为构造函数方法，是为新创建的`Person`实例对象设置`name`和`age`。

下面是一个如何创建`Person`实例对象的例子——使用类名形式的构造函数，后跟括起初始化变量的括号:

```
>>> person = Person('John Smith', 25)
>>> print(person.__dict__)
{'name': 'John Smith', 'age': 25}
```

通过调用内置的`__dict__`属性，我们看到这个实例对象`person`确实按照预期设置了其`name`和`age`属性。

# 获取人员。__init__(自己，姓名，年龄)上班

## __init__()方法中的 self 是什么？

然而，如果我们仔细观察示例中的`__init__()`方法，这个方法将`self`作为它的第一个参数。self 参数指的是调用函数的实例对象(关于函数中的`self`参数的更多信息，参见我的[上一篇文章](https://medium.com/better-programming/unlock-the-4-mysteries-of-self-in-python-d1913fbb8e16))。

下面的代码显示了它的主要用法。具体来说，当我们使用实例对象`person`调用方法`greet()`时，我们只需设置`other`参数。通过将实例对象`person`和`‘Mary’`分别设置为`self`和`other`参数，使用调用函数的类`Person`来处理该函数调用。它们会产生您可能期望的输出:

```
>>> person.greet('Mary')
John Smith says hello to Mary.
>>> # The above function is essentially operates as below
>>> Person.greet(person, 'Mary')
John Smith says hello to Mary.
```

*这也适用于* `*__init__()*` *法吗？*

让我们做一个实验:

```
>>> another_person = Person.__init__(another_person, 'Jeff Green', 24)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'another_person' is not defined
```

对于任何有经验的程序员来说，写这行代码都是一种罪过。我们知道会发生什么，但我们还没有定义变量！此外，我们知道`__init__()`方法没有返回任何东西。如果我们在自定义类中查看该方法声明中的代码(从技术上讲，在这种情况下会返回一个隐式的`None`)。

这个问题困扰着我和其他一些 Python 学习者，尤其是那些知道这些方法中的`self`参数是什么的人。要解决上面的问题，我们可以问一个相关的问题。

*我们可以通过什么作为* `*self*` *论证到* `*__init__()*` *的方法呢？*

为了让`Person.__init__(self, name, age)`工作，我们首先需要找到一个实例对象作为`self`参数。换句话说，其他东西已经创建了实例对象，它可以在这个构造函数方法中使用。但是什么？

## __new__()方法

`__new__()`方法来拯救。该方法实际上创建了自定义类的实例对象。我们使用构造函数来声明一个新的实例:

```
>>> another_person = object.__new__(Person)
>>> another_person.__dict__
{}
>>> Person.__init__(another_person, 'Ruby Green', 5)
__init__ is called
>>> another_person.__dict__
{'name': 'Ruby Green', 'age': 5}
```

如上所示，我们通过传递类`Person`作为所需的参数来调用`__new__()`方法。初始创建后，实例对象没有按预期设置属性。

使用这个实例对象作为`self`参数，我们最终能够使用类`Person`调用`__init__()`方法来完全初始化实例，该实例具有`name`和`age`属性。

所以，上面问题的答案是这样的:我们应该使用`__new__()`方法创建一个空实例，可以进一步设置为`__init__()`方法中的`self`参数。

# 深入分析:__new__() & __init__()

我们现在知道，当构造一个实例时，`__new__()`方法负责创建实例，`__init__()`方法负责完成初始化过程。现在，让我们通过一些相关的例子来更仔细地看看每种方法都做了什么。

## 使用 __new__()方法创建实例

如上所述，我们使用`__new__()`方法来创建实例。换句话说，`__new__()`方法的返回值是实例。

如果我们不让`__new__()`方法返回任何东西，会发生什么？

```
>>> class Student:
...     def __new__(cls):
...         print('__new__ gets called.')
...     def __init__(self):
...         print('__init__ gets called.')
... 
>>> student = Student()
__new__ gets called.
>>> type(student)
<class 'NoneType'>
```

在上面的例子中，`__new__()`方法没有返回任何东西。当我们调用构造函数(即`Student()`)时，只有`__new__()`方法被调用，而不是`__init__()`方法。当我们检查创建的实例的类型时，它是`NoneType`。

顺便提一下，需要提到的一点是，`__new__()`方法采用了一个名为`cls`的参数，这是我们想要为其创建实例对象的类。这个参数被命名为`cls`，这只是 Python 中的一个约定，与实例方法中名为`self`的参数(即上面例子中的`greet()`方法)相同。

为了实例化一个`Student`对象，`__new__()`方法应该返回一个新创建的`Student`实例。

```
>>> class Student:
...     def __new__(cls):
...         print('__new__ gets called.')
...         student = object.__new__(cls)
...         return student
...     def __init__(self):
...         print('__init__ is called')
... 
>>> student = Student()
__new__ gets called.
__init__ is called
>>> type(student)
<class '__main__.Student'>
```

在上面的例子中，更新后的`__new__()`方法返回了`Student`类的一个新实例。通过这样做，我们注意到`__init__()`方法被调用。正如所料，创建的对象属于类型`Student`。

## __init__ 有其他参数怎么办？

在上面的例子中，`__init__()`方法只有`self`参数。但是在很多情况下，我们希望为新的实例对象设置其他初始化属性。例如，`Student`实例对象可以有他/她的名字和学号。

```
>>> class Student:
...     def __new__(cls, *args):
...         print('__new__ gets called.')
...         student = object.__new__(cls)
...         return student
...     def __init__(self, name, id_number):
...         self.name = name
...         self.id_number = id_number
...         print('__init__ is called')
... 
>>> student = Student('John Smith', 983044)
__new__ gets called.
__init__ is called
>>> type(student)
<class '__main__.Student'>
>>> print(student.__dict__)
{'name': 'John Smith', 'id_number': 983044}
```

除了默认的`cls`参数，我们还有`*args`，它表示可变数量的参数。通过设置，我们现在可以为构造函数设置额外的参数，如上所示。

如果我们不把`*args`包含在`__new__()`方法中呢？

```
>>> class Student:
...     def __new__(cls):
...         print('__new__ gets called.')
...         student = object.__new__(cls)
...         return student
...     def __init__(self, name, id_number):
...         self.name = name
...         self.id_number = id_number
...         print('__init__ is called')
... 
>>> student = Student('John Smith', 983044)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: __new__() takes 1 positional argument but 3 were given
```

显然，构造函数正在将`Student`、`‘John Smith’`和`983044`这些参数发送给`__new__()`方法，该方法被定义为只接受一个参数(即类)，导致出现错误。

# 外卖食品

在本文中，我们通过研究`__new__()`和`__init__()`方法，回顾了 Python 中实例化的工作原理。以下是一些关键要点:

*   实例化或构造涉及到`__new__()`和`__init__()`两种方法。
*   `__new__()`方法是创建一个“空的”实例对象作为其类的返回值。
*   `__init__()`方法是设置其他属性来完成初始化过程。此方法不返回任何内容(即`None`)。
*   如果初始化涉及到设置额外的属性，那么定制的`__new__()`方法需要支持获取这些参数。通常，这是通过将`*args`作为方法的一部分来完成的。