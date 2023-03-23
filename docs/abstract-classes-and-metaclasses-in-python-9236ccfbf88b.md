# Python 中的抽象类和元类

> 原文：<https://betterprogramming.pub/abstract-classes-and-metaclasses-in-python-9236ccfbf88b>

## 使用 Python 来扩展 Python

![](img/6cfd2628e593291dd4b438c1009a29cd.png)

由 [PIRO4D](https://pixabay.com/users/piro4d-2707530/) 在 [Pixabay](https://pixabay.com/) 上拍摄的照片。

抽象类(或接口)是面向对象设计的重要组成部分。虽然 Python 不是纯粹的 OOP 语言，但它在抽象和元类方面提供了非常健壮的解决方案。

# 抽象类

简而言之，抽象类是不能实例化的类。这是因为抽象类没有指定特性的实现。相反，您只需指定方法签名和属性类型(有时)，并且每个子类都必须提供自己的实现。请注意，仍然有可能为*提供一些*实现。例如，抽象类可以实现默认的构造函数。

在设计复杂系统以限制重复和加强一致性时，抽象非常有用。例如，你可能有一个`Renderer`抽象类，它实现了`3DRenderer`、`2DRenderer`和`HapticRenderer`。然后，负责生成渲染数据的代码不需要知道特定的渲染器。它只是把他们都称为`Renderer`。

# ABC 模块

在 Python 中，抽象是通过内置库中的`abc`模块实现的。这是如何使用它的最简单的例子:

```
from abc import ABC class AbstractRenderer(ABC): 
    pass
```

`abc`模块公开了`ABC`类，代表`A`抽象`B`类`C`类。因此，任何直接继承`ABC`类的类都是抽象的。但是如果没有抽象方法，它就没有多大用处:

```
from abc import ABC, abstractmethod class AbstractRenderer(ABC): 
    @abstractmethod def render(self, data): 
        raise NotImplementedError()
```

在这个例子中，我们定义了`render`抽象方法，因此有了`@abstractmethod`装饰器。在它的身体里，我们升起`NotImplementedError`作为安全预防措施。从理论上讲，应该连调用这个方法都不可能。如果您尝试实例化`AbstractRenderer`，您将得到一个错误:

```
>>> renderer = AbstractRenderer() 
>>> Traceback (most recent call last): 
File "<stdin>", line 1, in <module> TypeError: 
Can't instantiate abstract class AbstractRenderer with abstract methods render
```

除非在一个抽象类中有每个`abstractmethod`的具体实现，否则你将无法实例化它。下面是如何实现它:

```
class 3DRenderer(AbstractRenderer): 
    def render(self, data): 
        some_library.draw_3d_stuff(data) renderer = 3DRenderer() 
renderer.render(some_data)
```

除了方法，还可以指定抽象属性(注意`property`不是`abc`的一部分):

在这个例子中，我们定义了`engine`抽象属性。第一个函数(用`@property`装饰)是 getter，用`@engine.setter`装饰的是 setter。稍后，您必须同时实现这两个功能:

仔细看看这里实现的 getter 和 setter。请注意，setter 将引擎字符串改为大写。这是一个使用`property`覆盖默认 Python 行为以及使用`abc`的例子。

# 元类

正如您刚才看到的，Python 的抽象类实现与您在其他流行语言中看到的不同。这是因为抽象不是语言语法的一部分。相反，它被实现为一个库。接下来的逻辑问题是“你能自己做类似的事情吗，纯粹用 Python？”答案是肯定的。

在转到元类之前，您需要先了解一些关于类的东西。你可能认为类是对象创建的蓝图，你可能是对的。但是在 Python 中，类本身就是*对象*。例如，当运行以下代码时:

```
class YouExpectedMeToBeAClass: 
    pass
```

Python 将*实例化*一个`YouExpectedMeToBeAClass`类，并将这个“对象”存储在内存中。稍后，当您在创建对象时引用该类时，Python 将使用该“对象”但是 Python 如何实例化一个类呢？当然是通过使用元类。

元类是类的类。元类为类的创建提供了蓝图。默认情况下，每个类都有一个元类(称为`type`)。要创建自定义元类，您必须继承`type`:

```
class CustomMeta(type): 
    pass class SomeClass(metaclass=CustomMeta): 
    pass
```

*注:* `*type*` *的父类是* `*type*` *本身。这是 Python 内部的一个 hack，用纯 Python 不可能实现替代的* `*type*` *。*

`CustomMeta`本身什么也不做。让我们添加更多的特性来展示元类的威力。让我们让`CustomMeta`检查是否每个子类都有一个`render`属性(就像`AbstractRenderer`):

如果您试图运行这段代码(甚至没有实例化任何东西！)，它会抛出一个错误。我先解释一下`__new__`是什么。这是类的构造函数，就像对象的`__init__`。它在定义`SomeClass`的时候被调用，从这个函数返回的任何东西都成为这个类。这些参数是:元类(`cls`)、新类的名称(`clsname`)、父类(`bases`)和属性(`attrs`)。在函数体中，我们枚举属性并检查`render`是否是其中之一。如果不是，则引发一个异常。要运行这段代码，将这段代码添加到`SomeClass`:

```
def render(self): pass
```

这是一个非常简单的例子，说明了`abc`模块在幕后做了什么。如果你有兴趣了解更多，可以在 GitHub 上的[获得](https://github.com/python/cpython/blob/master/Lib/abc.py)[源代码](https://github.com/python/cpython/blob/master/Lib/abc.py)。

# 结束语

感谢您的阅读。我希望你喜欢我的文章。如果你在评论中有元类的用例，请告诉我！

# 资源

*   [abc 文档](https://docs.python.org/3/library/abc.html)
*   [Python 数据模型](https://docs.python.org/3/reference/datamodel.html)