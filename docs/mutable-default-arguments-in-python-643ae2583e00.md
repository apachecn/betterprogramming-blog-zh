# Python 中可变的默认参数

> 原文：<https://betterprogramming.pub/mutable-default-arguments-in-python-643ae2583e00>

## 给你的函数设置默认值

![](img/336af0022b7baa54aee7b9e5c224800a.png)

[米切尔罗](https://unsplash.com/@mitchel3uo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/python-programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片。

`int`、`float`、`bool`、`str`、`tuple`、`Unicode`等内置类型的对象是不可变的。像`list`、`set`和`dict`这样的内置类型的对象是可变的。可变对象可以改变其状态或内容，而不可变对象则不能。

这是 Python 中可变和不可变对象的一个非常简单的定义。现在到了有趣的部分，它是函数定义中可变的默认对象。

下面我写了一个非常简单的函数:

```
def foobar(element, data=[]):
    data.append(element)
    return data
```

在这个函数中，我将`data`设置为一个列表(可变对象)作为默认参数。现在让我们执行这个函数:

```
>>> print(foobar(12))
[12]
```

输出符合预期。现在执行多次:

```
>>> print(foobar(22))
[12, 22]
>>> print(foobar(32))
[12, 22, 32]
```

这是怎么回事？正如您所看到的，第一次，函数返回的正是我们所期望的。在第二次和所有后续传递中，每次调用都使用相同的列表。

# 为什么会这样？

Python 的默认参数在定义函数时计算一次，而不是在每次调用函数时计算。当 Python 遇到它时，它要做的第一件事就是编译它，以便为这个函数创建一个代码对象。当这个编译步骤完成时，Python 会计算默认参数，然后将其存储在函数对象本身中。

所以让我们做一些反省。为了消除任何混淆，我突出显示了几行代码。

## 执行前的功能

```
def foo(l=[]):
    l.append(10)
```

在函数执行这个定义之后，您可以使用`__defaults__`来检查这个函数的默认属性。

`__defaults__`是一个元组，包含那些具有默认值的参数的默认参数值(如果没有参数具有默认值，则为`None`)。

```
>>> foo.__defaults__
([],)
```

结果是一个空列表作为`__defaults__`中的唯一条目。

## 执行后的功能

现在让我们执行这个函数并检查默认值:

```
>>> foo()
>>> foo.__defaults__
([10],)
```

惊讶吗？对象内部的值会发生变化。对该函数的连续调用现在将简单地追加到嵌入的列表对象中。

让我们多次执行该函数:

```
>>> foo()
>>> foo()
>>> foo()>>> foo.__defaults__
([10, 10, 10, 10],)
```

这是因为默认参数值存储在函数对象中，而不是代码对象中(因为它们表示运行时计算的值)。

# 解决方案

现在，问题是如何编码`foobar`以获得我们期望的行为。

幸运的是，解决方案很简单。用作默认值的可变对象被替换为`None`，然后测试参数的`None`:

```
def foobar(element, data=None):
    if data is None:
        data = []
    data.append(element)
    return data>>> foobar(12)
[12]
```

所以用`None`替换可变的默认参数解决了我们的问题。

# 善用可变默认参数

然而，可变默认参数也有一些很好的用例:

1.  在回调中将局部变量绑定到外部变量的当前值。
2.  缓存/记忆化。

# 结论

我希望您喜欢 Python 中可变默认参数的解释。如果你有任何疑问或问题，请在下面的评论中告诉我。

# 参考

*   [https://docs . python . org/3/reference/data model . html # the-standard-type-hierarchy](https://docs.python.org/3/reference/datamodel.html#the-standard-type-hierarchy)