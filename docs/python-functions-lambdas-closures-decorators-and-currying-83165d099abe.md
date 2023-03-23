# Python 函数:Lambdas、闭包、装饰器和 Currying

> 原文：<https://betterprogramming.pub/python-functions-lambdas-closures-decorators-and-currying-83165d099abe>

## 理解 Python 中的四个高级函数概念

![](img/1260c8c88b93aae790f591c71ac1e987.png)

照片由[蒂姆·斯万](https://unsplash.com/@timswaanphotography?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

Python 函数是一个代码块，当它们被请求运行时执行特定的操作，我们称之为调用函数。大多数情况下，我们使用以下语法来声明和调用函数:

在上面的代码中，我们使用关键字`def`声明了一个名为`add_up`的函数。这个函数将两个数字(`num1`和`num2`)作为输入参数，按照定义的操作计算它们的和，并将这个值作为函数的输出返回。很简单，对吧？

除了这些常规函数，Python 中还有其他种类的函数可以让我们的开发受益。让我们在本文中回顾一下它们。

# λ函数

Lambda 函数是小型匿名单行函数。有时，我们可以简单地称它们为兰姆达斯。Lambdas 具有以下语法:

```
lambda arguments: expression
```

我们使用`lambda`关键字声明一个 lambda 函数。在这个关键字之后，我们指定参数列表，参数的数量可以从零到多个不等。然后，我们指定想要执行的操作，这就是 lambda 函数的表达式。

正如您所看到的，lambdas 具有非常简洁的语法，因此它们最适合于需要简单的一次性函数的场景。让我们考虑下面的例子。`sorted()`函数根据`key`参数指定的键函数对 iterable 进行排序:

```
>>> # define a list of tuples
>>> records = [(1, 'John'), (2, 'Aaron'), (5, 'Ian')]
>>> # sort with a lambda
>>> sorted(records, key=lambda x: len(x[1]))
[(5, 'Ian'), (1, 'John'), (2, 'Aaron')]
```

在上面的代码中，我们首先定义了一个列表来存储一些学生的记录以及他们的学号和姓名。然后我们用 lambda 函数对它们进行排序，该函数根据学生姓名的长度对列表进行排序。

关于使用 lambda 函数排序的信息，可以参考我以前的文章。知道兰姆达斯可能被误用也很重要。为了您的方便，提供以下文章:

[](https://medium.com/better-programming/advanced-sorting-in-python-using-lambdas-and-custom-functions-410b5780fb07) [## Python 中使用 Lambdas 和自定义函数的高级排序

### 对非基本数据类型(如 int、str)的元素列表进行排序

medium.com](https://medium.com/better-programming/advanced-sorting-in-python-using-lambdas-and-custom-functions-410b5780fb07) [](https://medium.com/better-programming/the-top-4-misuses-of-lambdas-in-python-e419f426b74f) [## Python 中 Lambdas 的 4 大误用

### 使用 Python lambdas 的最佳实践

medium.com](https://medium.com/better-programming/the-top-4-misuses-of-lambdas-in-python-e419f426b74f) 

# 关闭

闭包是捕获外部函数的非局部变量的嵌套函数。我认为从概念上理解闭包并不容易。[彻底理解](https://medium.com/swlh/anatomize-pythons-closures-dbf0fa217d38)需要深入了解 Python 中作为一级对象的[作用域](https://medium.com/better-programming/6-things-to-know-to-demystify-namespaces-and-scopes-in-python-f5353c7ff56f)和[函数。出于本文的目的，让我们通过一个具体的例子对闭包有一个总体的了解:](https://medium.com/swlh/everything-is-an-object-in-python-learn-to-use-functions-as-objects-ace7f30e283e)

在上面的代码中，我们定义了一个名为`make_multiplier`的函数。因为它包含了另一个函数`multiplier`，我们可以称`make_multiplier`为外部函数，称`multiplier`为嵌套函数。外部函数返回嵌套函数作为其返回值。重要的是，嵌套函数使用并修改了外部函数范围内定义的非局部变量(即`product`)。总而言之，在 Python 中创建闭包有三个关键要素:

1.  在外部函数的范围内声明一个嵌套函数。
2.  嵌套函数范围外的非局部变量的绑定。
3.  返回嵌套函数以输出闭包函数。

我们如何使用闭包？让我们看看下面这个简单的例子:

然后我们声明一个名为`multiplier3`的闭包。每次我们调用这个闭包，乘积就乘以 3。换句话说，封盖“记住”了产品最后一次使用后的状态。相关概念包括变量绑定和值捕获。我们可以通过调用`__code__.co_freevars`和`__closure__[1].cell_contents`来查看相关信息。

关于闭包的更多细节，可以参考我以前的文章:

[](https://medium.com/swlh/anatomize-pythons-closures-dbf0fa217d38) [## 剖析 Python 的闭包

### Python 中闭包的介绍性剖析，帮助您更好地理解闭包。

medium.com](https://medium.com/swlh/anatomize-pythons-closures-dbf0fa217d38) 

# 装修工

装饰器是扩展其他函数的行为而不显式修改它们的函数。装饰器本质上是一种高阶函数，定义为要么以其他函数为输入，要么返回其他函数为输出的函数。让我们通过下面的例子对装饰者有一个真实的感受:

在上面的代码中，我们定义了两个函数(`clap`和`triple_repeat_wrapper`)，后者是一个高阶函数，它在嵌套的`wrapper`函数中调用传递的`func`三次，并返回`wrapper`函数作为高阶函数的输出。

我们如何使用这些功能？如下面的代码所示，我们创建了一个名为`wrapped_clap`的函数，它将`clap`函数传递给高阶函数`triple_repeat_wrapper`。如您所见，调用`wrapped_clap`函数将导致嵌套的`wrapper`函数内的代码被调用。与打印输出一致，我们知道`wrapped_clap`函数引用了嵌套函数`wrapper`。

但是如果你回想一下，当你看过一些关于装饰者的示例代码时，你一定见过@符号的用法。这个符号是如何与我们上面定义的功能一起发挥作用的？让我们参考下面的代码来解决这个问题:

在上面的代码中，我们声明了一个名为`hooray`的函数。当我们调用这个函数时，输出与前面的`wrapped_clap`函数的输出具有相同的格式。类似地，`hooray`函数引用嵌套的`wrapper`函数。

为什么会这样？您可能已经注意到，在这个`hooray`函数声明的正上方，我们使用@符号作为`triple_repeat_wrapper`函数名的前缀。这只是装饰者的语法糖。本质上，我们告诉 Python 解释器，我们将要定义的函数将被装饰函数包装。

# Currying

currying 以数学家 Haskell Curry 的名字命名，指的是通过应用部分参数从现有函数创建新函数。因此，这个概念有时也被称为部分功能。

与上面的概念相比，这个概念更容易理解。让我们考虑下面的简化示例，使用我们在上面定义的相同函数`add_up`:

在上面的代码中，我们使用 lambda 函数将数字`7`设置为`add_up`函数的第一个参数。换句话说，创建的`add_seven`函数是原始`add_up`的部分函数，第一个参数一直被设置为`7`。除了使用 lambda 函数之外，使用常规方式定义带有`def`关键字的函数也是可以接受的。

在`functools`模块中有另一个创建部分功能的便捷工具。考虑下面的例子。我们使用`partial`函数来创建`add_ten`函数，它有一个默认参数`10`来调用`add_up`函数:

# 外卖食品

在本文中，我们回顾了 Python 函数基础之外的四个高级概念。以下是这些概念的简要回顾:

*   Lambdas 是匿名函数，在需要简单的一次性内联函数的情况下非常方便。
*   闭包是嵌套函数，它绑定了周围范围的非局部变量。
*   装饰器是修改其他函数行为的高阶函数。
*   Currying 是我们如何通过设置一些默认参数从现有函数创建部分函数。