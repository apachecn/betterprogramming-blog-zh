# 为什么在 Python 代码中需要装饰器

> 原文：<https://betterprogramming.pub/why-you-need-decorators-in-your-python-code-df12d43eac9c>

## 元编程是关键

![](img/ec5683f9df5f25672bf4dcc82977982c.png)

照片由 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1478831) 上的[免费照片](https://pixabay.com/users/freephotocc-2275370/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1478831)拍摄。

Python 因其清晰和语法上的甜蜜而受到称赞。在这篇文章中，我将教你使用 Python 中的 decorators 来使你的代码变得可读和干净。

# 什么是装修工？

要理解 decorators 是什么，首先需要熟悉 Python 处理函数的方式。从它的角度来看，函数和普通对象没什么区别。它们具有属性，可以重新分配:

此外，您可以将它们作为参数传递给其他函数:

现在，对于装修工来说。装饰器用于修改函数或类的行为。实现这一点的方法是定义一个返回另一个函数的函数(decorator)。这听起来很复杂，但是通过这个例子你会明白一切:

让我们一步一步来:

*   首先，我们在第 1 行定义了`logging_decorator`函数。它接受单个参数，这是我们试图修饰的函数。
*   在里面，我们定义了另一个函数:`logging_wrapper`。然后,`logging_wrapper`被返回并代替原来的修饰函数使用。
*   在第 7 行，您可以看到装饰器是如何应用于`sum`函数的。
*   在第 11 行，当我们调用`sum`时，它不会只调用`sum`。它将调用`logging_wrapper`，后者将在调用`sum`前后记录日志。

# 为什么需要装修工？

很简单:可读性。Python 因其清晰简洁的语法而备受赞誉，decorators 也不例外。如果有超过一个函数共有的行为，你可能需要做一个装饰器。以下是一些例子，说明它们何时会派上用场:

*   运行时检查参数类型
*   基准函数调用
*   缓存函数结果
*   计算函数调用
*   检查元数据(权限、角色等。)
*   元编程
*   还有更多…

现在我将列出一些代码示例。

# 具有返回值的装饰器

假设我们想知道每个函数调用需要多长时间。此外，函数大多数时候会返回一些东西，所以装饰器也必须处理这些:

你可以看到我们将返回值存储在第 5 行的`result`中。但是在返回之前，我们必须完成函数的计时。这是一个没有装饰者就不可能的行为的例子。

# 有争论的装饰者

有时，我们想要一个接受值的装饰器(比如 Flask 中的`@app.route('/login')`):

为了实现这一点，我们定义了一个额外的函数，它接受一个参数并返回一个装饰器。

# 用类装饰

使用类而不是函数来修饰是可能的。唯一的区别是语法，所以做你更喜欢的。下面是使用类重写的日志装饰器:

好处是您不必处理嵌套函数。您需要做的就是定义一个类并覆盖`__call__`方法。

# 装饰课

有时候你可能想要修饰一个类中的每一个方法。你可以这样写:

但是如果你有很多方法，这可能会失去控制。令人欣慰的是，有一种方法可以同时装饰整个班级:

现在，不要惊慌。这看起来很复杂，但这是相同的逻辑:

*   首先，我们让`logging_decorator`保持原样。它将应用于一个类的所有方法。
*   然后我们定义一个新的装饰器:`log_all_class_methods`。它就像一个常规的装饰器，但是返回一个类。
*   `NewCls`有一个习俗`__getattribute__`。对于所有对原始类的调用，它将用`logging_decorator`来修饰函数。

# 内置装饰器

你不仅可以定义自己的装饰器，而且标准库中也有一些。我将列出与我合作最多的三个:

*   `@property` —内置的装饰器，允许您为类属性定义 getters 和 setters。
*   `@lru_cache`—`functools`模块中的装饰器。它记忆函数参数和返回值，这对于纯函数来说很方便(比如`factorial`)。
*   `@abstractmethod` —来自`abc`模块的装饰器。指示该方法是抽象的，并且缺少实现细节。

# 结束语

谢谢你的阅读，我希望你喜欢我的文章。继续订阅更多 Python 内容！

# 资源

*   [PEP 318 —函数和方法的装饰器](https://www.python.org/dev/peps/pep-0318/)
*   [对可调用对象的高阶函数和操作](https://docs.python.org/3/library/functools.html)