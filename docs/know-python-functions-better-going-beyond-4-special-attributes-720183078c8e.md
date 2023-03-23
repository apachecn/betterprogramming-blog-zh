# 更好地了解 Python 函数:超越 4 个特殊属性

> 原文：<https://betterprogramming.pub/know-python-functions-better-going-beyond-4-special-attributes-720183078c8e>

## 对 Python 函数有更深的理解

![](img/f82f82f454611543088efdc9536e63ba.png)

内森·詹宁斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

函数是任何应用程序的潜在驱动力。他们负责处理应用程序中的数据。因此，我们应该对 Python 函数有一个全面的了解。

作为一种面向对象的编程语言，Python 函数也是对象。这意味着它有自己的属性，就像一个普通的物体一样。一组特殊的属性是内置的，它们的名称格式为:`__the_attr__`，名称前有两个下划线，名称后有两个下划线。你可能知道有些函数有这种命名约定，比如`__init__`和`__str__`，这就是所谓的特殊方法。使用相同的逻辑，我们可以将这些属性称为特殊属性。

在本文中，我将介绍函数的 4 个特殊属性，除此之外，我们将讨论这些特殊属性背后的四个关键概念。事不宜迟，让我们开始吧。

# 1.注释:__ 注释 _ _

第一个特殊属性是`__annotations__`，它访问函数的注释。函数注释是将表达式与 Python 函数的各种组件相关联的一种方式。下面是一个简单的例子。

```
>>> def example(a: "something", b: 1) -> True:
...     pass
... 
>>> example.__annotations__
{'a': 'something', 'b': 1, 'return': True}
```

如您所见，`__annotations__`是一个`dict`对象，它包括参数名和作为键的`“return”`以及作为值的相关表达式。然而，上面的例子不是很有用。函数注释的一个重要用途是函数中的类型提示:

```
>>> def example(a: str, b: int) -> bool:
...     return True
... 
>>> example.__annotations__
{'a': <class 'str'>, 'b': <class 'int'>, 'return': <class 'bool'>}
```

第三方工具，如代码分析包，可以使用所有的类型信息来检查所使用的参数是否属于正确的类型。

# 2.文档字符串:__doc__

当我们定义一个将被公开使用的函数时，最好的做法是包含该函数的适当文档。该文档称为 docstrings，它提供了有关使用该函数的必要信息。下面是一个简单的例子。

函数文档字符串示例

在函数的 docstring 中，我们应该包括三个关键部分:1 .函数操作的摘要；2.参数(类型和解释)；第三。返回值(类型和解释)。如果函数引发异常，您也应该指定它。值得注意的是，函数的 docstring 可以通过`__doc__`属性来访问。因为 docstring 是多行字符串，为了以正确的格式显示它，我们可以使用内置的`print`函数:

```
>>> print(multiplier.__doc__)
Multiply two numbers to get their product.
    :param num1: float, first number for multiplication
    :param num2: float, second number for multiplication
    :return: float, the product of the two numbers
```

# 3.默认参数:__default__

当我们定义函数时，我们可以为一些参数设置默认值，这样当我们调用函数时，我们就不需要设置这些参数。以下示例显示了一个包含默认参数的函数，以及两个不同的函数调用。

```
>>> def example(a, b=1, c="Hello!"):
...     print(a, b, c)
... 
>>> example("Hi")
Hi 1 Hello!
>>> example("Hi", 2, "Python")
Hi 2 Python
```

如您所见，当函数使用默认参数时，可以省略这些参数。您可以通过访问`__default__`属性来检查函数的默认参数:

```
>>> example.__defaults__
(1, 'Hello!')
```

当我们设置默认参数时，如果参数是可变的，比如一个`list`对象，我们应该小心。这是因为默认参数是在函数定义过程中计算的。请考虑以下情况:

```
>>> def example(a, b=list()):
...     pass
... 
>>> example.__defaults__
([],)
```

如果你通过多次省略`b`参数来调用函数，你使用的是同一个`list`对象。如果您有兴趣了解关于默认可变参数的更多信息，可以参考我以前的文章:

[](/top-5-mistakes-you-make-when-declaring-functions-in-python-b7a0747711a4) [## 在 Python 中声明函数时最容易犯的 5 个错误

### 编写更好的函数

better 编程. pub](/top-5-mistakes-you-make-when-declaring-functions-in-python-b7a0747711a4) 

# 4.闭包:__closure__

一个重要的函数概念是闭包。闭包是由另一个函数(称为外部函数)创建并返回的内部函数，内部函数使用外部函数的变量，称为非局部变量绑定。下面展示了如何在 Python 中创建闭包:

乘数创建者

`double_multiplier`和`triple_multiplier`都是闭包，因为它们满足三个要求:

1.  它是在外部函数(`multiplier_creator`)中创建的内部函数(`multiplier`)。
2.  创建的内部函数`multiplier`是外部函数`multiplier_creator`的返回值。
3.  内部函数`multiplier`使用了一个非局部变量`n`。

对于非局部变量，我们需要知道 LEGB 规则:局部->封闭->全局->内置，它定义了变量的查找顺序。查找包括顺序检查不同的范围，封闭范围也称为非局部范围。关于作用域和闭包，可以参考我以前的文章:

[](/5-essential-aspects-of-python-closures-494a04e7b65e) [## Python 闭包的 5 个基本方面

### 揭开闭包的神秘面纱——这个概念让许多 Python 初学者感到困惑

better 编程. pub](/5-essential-aspects-of-python-closures-494a04e7b65e) 

对于这些闭包，它们有一个特殊的属性:`__closure__`。让我们看看:

```
>>> double_multiplier.__closure__
(<cell at 0x7ff6e01742e0: int object at 0x7ff6d0028110>,)
```

`__closure__`特殊属性是一个`tuple`对象，它包含非局部变量的代码单元。例如，如果您检查单元格的内容，您将看到闭包的绑定非局部变量是什么:

```
>>> double_multiplier.__closure__[0].cell_contents
2
>>> triple_multiplier.__closure__[0].cell_contents
3
```

# 结论

在本文中，我们介绍了 Python 函数的四个特殊属性。这些特殊属性都有相关的底层函数概念。如果你知道这些特殊属性，那就太好了。更重要的是，你要熟知这些概念。

感谢阅读这篇文章。

```
Not a Medium member yet? Support my writing by [using my membership link](https://medium.com/@yong.cui01/membership) (at no extra cost for you, but a portion of your membership dues is redistributed to me by Medium as an incentive).
```