# 使用 __slots__ 将您的 Python 代码库优化近 3 倍

> 原文：<https://betterprogramming.pub/optimize-your-python-programs-for-free-with-slots-4ff4e1611d9d>

## 通过添加一行代码来提高执行速度和内存使用率

![](img/5d32c2be63867204636968ca2e851252.png)

照片由[吉利](https://unsplash.com/@gillyberlin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

尽管 Python 是最受欢迎的编程语言之一，但与其他语言相比，它的效率也很低。Python 对象是时间和内存性能的主要瓶颈。

由于它的动态类型系统和可变对象，解释器不能像编译器那样为更静态的语言优化程序执行。这是因为你没有给它任何关于你实际上要用你的程序做什么的提示。让我给你看一个例子:

无槽类人

在这里，您可以看到 Python 如何允许您为对象分配未声明的属性。但是，这种增加的功能也有不好的一面。解释器不能预先知道对象有多大，因此它必须考虑最终的操作，就像上面显示的那样。

## Python 的默认行为

默认情况下，Python 将对象的属性存储在可变字典中，可以通过`__dict__`属性访问该字典。

```
# Before setting the "country" attribute
>>> print(person.__dict__){'name': 'Hans', 'age': 30}# After setting the "country" attribute
>>> print(person.__dict__){'name': 'Hans', 'age': 30, 'country': 'Germany'}
```

如您所见，对象`__dict__`中添加了一个新条目。然后将通过字典查找访问它们，如这里的[所述](https://github.com/zpoint/CPython-Internals/blob/master/Interpreter/slot/slot.md#lookup-procedure-in-mro--1)。还要注意，Python 字典占据了相当大的空间。下面是空元组、列表和字典的内存需求的快速比较:

控制台输出(所有大小都以字节为单位):

```
Size of tuple:       56
Size of list:        72
Size of dictionary: 248
```

正如您所看到的，Python 字典是一个非常庞大的数据结构。现在，让我们看看另一种方法。

## Python 的 __slots__ 是什么

Python 提供了定义类属性`__slots__`的可能性，但是这如何影响创建的对象呢？

开槽类人

在这里，我将`Person` class' `__slots__`属性定义为一组字符串，每个字符串代表`Person`对象的一个属性。请注意，您也可以使用列表来声明属性，但是[元组更有效](/optimize-your-python-programs-with-tuples-704f176c956d)。“开槽”类不允许新属性被添加到它们的实例中。

```
>>> person.country = 'Germany'AttributeError: 'Person' object has no attribute 'country'
```

定义`__slots__`禁止了`__dict__`属性的创建，您可以从下面的错误中看到:

```
# Slotted classes don't have __dict__
>>> print(person.__dict__)AttributeError: 'Person' object has no attribute '__dict__'# Slotted classes have __slots__ instead
>>> print(person.__slots__)('name', 'age')
```

我不会深入讨论使用`__slots__`的属性查找是如何工作的实现细节，但是如果你有兴趣，可以看一看[这个指南](https://github.com/zpoint/CPython-Internals/blob/master/Interpreter/slot/slot.md#slot)。简而言之，Python 几乎可以直接访问对象属性，而不必执行耗时的查找。

## __ 网 __ 对 _ _ 槽 _ _

您可能会惊讶地发现，除了`__slots__`声明之外，开槽类和非开槽类都编译成完全相同的 Python 字节码。下面是一个使用[编译器资源管理器](https://godbolt.org/)的具体例子，这是一个非常棒的工具，可以让你详细了解源代码是如何编译的:

非时隙类

开槽类

事实上，性能差异不在于字节码，而在于 Python 如何处理它。特别是，我们对`LOAD_ATTR`和`STORE_ATTR`操作感兴趣，它们分别调用 C 函数`PyObject_GetAttr()`和`PyObject_SetAttr()`，从`[ceval.c](https://github.com/python/cpython/blob/be578e0c063dad1dbb273f86d5bc77e4e6f14583/Python/ceval.c)` [源文件](https://github.com/python/cpython/blob/be578e0c063dad1dbb273f86d5bc77e4e6f14583/Python/ceval.c)中可以看到。

原来，Python 虚拟机(PVM)执行的这些操作依赖于用于描述内置类型的`[PyTypeObject](https://docs.python.org/3/c-api/type.html#c.PyTypeObject)` C struct 的两个属性，即`[tp_getattro](https://docs.python.org/3/c-api/typeobj.html#c.PyTypeObject.tp_getattro)`和`[tp_setattro](https://docs.python.org/3/c-api/typeobj.html#c.PyTypeObject.tp_setattro)`。这些字段存储检索或设置给定名称的对象属性的函数。在下面的 Python 源代码示例中，您可以看到它们是如何用于获取对象属性的。别担心，我已经添加了一些评论，这样你可以更好地理解。

Python 源代码中的 PyObject_GetAttr()函数。

现在，有趣的部分来了:存储在`tp_getattro`和`tp_setattro`字段中的默认 getter 和 setter 函数分别是`PyObject_GenericAttr()`和`PyObject_GenericSetAttr()`。从 Python [文档](https://docs.python.org/3/c-api/object.html#c.PyObject_GenericSetAttr)来看，这些函数首先寻找一个[数据描述符](https://realpython.com/python-descriptors/)通过内存地址偏移量直接访问属性，速度非常快。如果没有找到这样的描述符，Python 会继续在实例属性字典`__dict__`中查找，这样会慢一些。我不打算把重点放在查找实现上，因为它相当复杂，但是如果您感兴趣，可以查看 Python 源代码中的`[object.c](https://github.com/python/cpython/blob/main/Objects/object.c)` [文件](https://github.com/python/cpython/blob/main/Objects/object.c)。然而，下面是该过程的伪代码概述:

## 基准

既然我们已经讨论了槽式类和非槽式类之间的区别，是时候做一些实际的基准测试来突出使用`__slots__`的优势了。对于速度基准，我将使用 Python 的内置模块`[timeit](https://docs.python.org/3/library/timeit.html)`。

下面是一个代码片段，它测量了创建这两个类的实例所需的时间。

这是输出结果:

```
SlottedClass:    0.19703685840140678
NonSlottedClass: 0.2519917337005609
```

正如您所看到的，时隙类只占用了非时隙类测试运行时间的 78%。速度提高了 22%!

至于调用`add`函数，下面是基准测试结果:

```
SlottedClass:    0.10773063939923304
NonSlottedClass: 0.12706781459928607
```

非时隙类占用时隙类所需时间的 118%。像以前一样，我们只需在类定义中添加一行就可以明显提高性能。

分槽类不仅在代码执行速度方面更好，而且在内存使用方面也更好。事实上，在本例中，添加`__slots__`将实例大小减少了 1/3。注意，使用`pympler.asizeof`模块中的`asizeof()`函数比内置的`__sizeof__()`方法更能让我们了解一个对象占用了多少内存。

```
print(f'Slotted: {asizeof(SlottedInstance)}')
print(f'Non-Slotted: {asizeof(NonSlottedInstance)}')Slotted:     112
Non-Slotted: 328
```

我们通过在类的开头添加`__slots__`实现了三倍的内存使用优化，太棒了！让我们试试更重的课程:

```
Slotted: 440
Non-Slotted: 904
```

即使在这个测试中，时隙类也比非时隙类表现出色 2 倍。

# 结论

总而言之，很明显`__slots__`极大地提高了 Python 程序的性能，无论是在速度还是内存使用方面。每当你不需要添加或删除实例属性时，你应该总是使用槽类，我不明白你为什么要这样做。

即使优化并不总是至关重要的，特别是对于测试或概念验证，自由时间和空间优化肯定不会有什么坏处。然而，使用分槽类的真正好处在生产代码中是显而易见的，在生产代码中，只需在 Python 类中添加一行代码，就可以获得明显更好的性能。

为什么这不是 Python 中的默认行为？我个人不知道，但这背后一定有原因。也许他们只是想让这种语言开箱即用，尽可能灵活。

我希望你喜欢这篇文章。感谢阅读！

如果您对 Python 的进一步性能优化感兴趣，我强烈建议您看看下面的故事:

[](/speed-up-your-python-codebases-with-c-extensions-94859875eb70) [## 用 C 扩展加速你的 Python 代码库

### 给你的 Python 程序带来 C 语言的速度

better 编程. pub](/speed-up-your-python-codebases-with-c-extensions-94859875eb70)