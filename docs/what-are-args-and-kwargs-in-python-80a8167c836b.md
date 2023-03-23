# Python 中的 Args 和 Kwargs 是什么？

> 原文：<https://betterprogramming.pub/what-are-args-and-kwargs-in-python-80a8167c836b>

## 通过使用*args 和**kwargs 使函数接受任意数量的参数

![](img/3e8dc06fa17368b89b4af4e3eef9ee61.png)

照片由[克里斯蒂娜@ wocintechchat.com](https://unsplash.com/@wocintechchat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄。

Python 中的`*args`和`**kwargs`都意味着函数接受任意数量的参数*。*

*   `*args`指常规论证(如`myFun(1,2,3)`)。
*   `**kwargs`指关键词论据(如`myFun(name="Charlie")`)。

在本指南中，您将学习如何在 Python 中实现接受任意数量参数的函数

# args-Python 中任意数量的参数

让我们创建一个示例函数`mySum()`，它接受任意数量的参数并将它们相加:

注意`*args`是如何像数字列表一样循环的。这是因为参数确实被转换成了一个数字列表。

您也可以将一个实际的列表作为参数传递给`mySum()`:

```
numbers = [1,2,3]mySum(*numbers) # returns 6
```

# kwargs—Python 中任意数量的关键字参数

您可以使用`**kwargs`向函数传递任意数量的关键字参数。

关键字参数是附加了标签的参数，例如`myFun(name="Jack").`

例如，让我们创建一个打印项目的函数:

```
**def** read(**kwargs):
    **for** key, value **in** kwargs.items():
        print("{}: {}".format(key, value))read(shoes="Adidas", shirt="H&M") # prints shoes: Adidas, shirt:H&M
read(socks="Nike")                # prints socks: Nike
```

现在`**kwargs`像字典一样循环通过。这是因为`**kwargs`被转换成了引擎盖下的字典。

您还可以将一个条目字典传递给`read()`函数:

```
clothes = {"shoes": "Adidas", "shirt": "H&M"}read(**clothes) # prints shoes: Adidas, shirt:H&M
```

现在你知道如何使用`*args`和`**kwargs`了。

# 在同一个函数中使用 Args 和 Kwargs

有时，您可能需要在项目中接受任意数量的参数和关键字参数。在这种情况下，您可以在同一功能中同时使用`*args`和`**kwargs`。

例如:

现在，您可以向函数传递任意数量的常规参数和任意数量的关键字参数。Python 对此没有问题，因为它可以推断出哪些是常规参数，哪些是关键字参数。

# Args 和 Kwargs 是一种命名约定

既然你已经理解了`*args`和`**kwargs`，现在是学习`args`和`kwargs`这两个词没有什么特别的时候了。它们不是 Python 中的保留关键字。

*   例如，`*args`可以被命名为`*numbers`。
*   例如,`**kwargs`可以被命名为`**items`。

唯一需要担心的是确保在处理常规参数时使用`*`，在处理关键字参数*时使用`**`。*

# 结论

在 Python 中，可以通过使用`*args`或`**kwargs`将任意数量的参数传递给函数。

*   使用带有常规参数的`*args`。定义`**def** myFun(*args)`可以调用`myFun(1,2)`或`myFun(10,1,8,7,5,4,2)`。
*   处理关键字参数时使用`**kwargs`。定义`**def** myFun(**kwargs)`可以调用`myFun(name="Jim")`或`myFun(socks="Nike", shirt="H&M")`。

`*args`和`**kwargs`不是 Python 中的保留关键字。你可以随意命名它们，只要你记得使用常规参数的`*`和关键字参数的`**`。

感谢阅读。编码快乐！

# 资源

 [## 文件

### 欢迎光临！这是 Python 3.9.2 的文档。

docs.python.org](https://docs.python.org/3/)