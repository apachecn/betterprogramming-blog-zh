# Dataclass 如何美化您的 Python 代码

> 原文：<https://betterprogramming.pub/python-elegant-and-concise-models-with-the-dataclasses-3992b5f5a08d>

## 编程，Python

## 弄清楚如何轻松优雅地表示数据结构

![](img/b67c1194726532257578b124cd973e32.png)

Julien Moreau 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

数据类是 Python 3.7 引入的[新特性之一。它的目标是简化主要包含数据而没有功能的类的创建。这可以与常用的数据结构相比较。](https://docs.python.org/3/whatsnew/3.7.html)

当使用类作为数据结构时，我们要实现与`__init__()`和`__repr__()`相同的方法，比较繁琐。Dataclasses 为我们自动生成这些方法。这在语言中提供了更多的透明度。

# **沉浸在数据类中**

## 申报

数据类的声明是通过影响一个类的装饰器来完成的。在下面的示例中，我们将创建一个数据类来表示 GPS 坐标:

您可以直接打印`Position`对象，而无需定义`__repr__()`函数:

```
$ python dataclass_ex1.py
Position(lat=37.6216, lon=-122.3929)
```

我们可以用等号运算符比较`dataclass`对象，而不必实现额外的`__eq__`方法:

```
$ python dataclass_ex2.py
True
```

## 方法

你可以在一个`dataclass`里面添加方法。我们将实现一种方法来计算两个位置之间以千米为单位的哈弗线距离:

```
$ python dataclass_ex3.py
15479.614752629424
```

## 作为方法属性的数据类对象

`dataclass`对象被认为是任何其他的 Python 类型。我们实现另一个类来实例化`Town`对象。基本上，`Town`对象会有一个位置属性:

## 遗产

`dataclass`也支持继承。上一节介绍的`Town`类可以简化。我们将考虑一个城镇作为位置。`Town`对象将继承经纬度属性:

为了更进一步，我们添加了一个新的类来区分城镇中的首都:

## 菲尔茨

数据类可以使用`field()`说明符单独定制每个字段。该字段支持许多不同的[参数](https://docs.python.org/3/library/dataclasses.html#dataclasses.field)。我们将使用其中一些来定义经度和纬度的字段和元数据的默认值:

## 不变

`dataclass`提供不变性选项设置`frozen=True`。启用后，`dataclass`字段可能永远不会改变。小心嵌套的`dataclass`，它包含使用继承的不可变字段。

我们将添加一个新的`Country`类来收集一个国家的不同城镇(包括首都)。在这个类中，有一个函数`get_capital`从城镇列表中过滤出首都数据:

# 结论

我们已经看到，通过使用`dataclass`， **y** 你不需要在每次写类的时候都写重复的方法。这些方法已经实现并且完全透明。您可以隐式定义它们来覆盖默认行为。

此外，我们已经意识到`dataclass`实际上类似于一个经典类，并且支持继承。您也可以从现有的`dataclass`中定义类属性。最后，如果您认为不可变对象适合您的关注点，您可以定义不可变对象。

通过例子，我们可以断言`dataclass`是创建综合数据模型的一个优雅的解决方案。

自从我发现了这个特性，我就尽可能地使用它来编写优秀的可读代码！你呢？

# 资源

[](https://www.python.org/dev/peps/pep-0557/) [## PEP 557 —数据类别

### 这个 PEP 描述了一个叫做数据类的标准库的附加物。虽然他们使用非常不同的…

www.python.org](https://www.python.org/dev/peps/pep-0557/)  [## 数据类-数据类-Python 3 . 9 . 1 文档

### 这个模块提供了一个装饰器和函数，用于自动添加生成的特殊方法，比如和…

docs.python.org](https://docs.python.org/3/library/dataclasses.html)