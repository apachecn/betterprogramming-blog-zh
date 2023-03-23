# Python 闭包的 5 个基本方面

> 原文：<https://betterprogramming.pub/5-essential-aspects-of-python-closures-494a04e7b65e>

## 揭开闭包的神秘面纱——这个概念让许多 Python 初学者感到困惑

![](img/312582bb83cc54842a22f76f2aeae93b.png)

Kurt Cotoaga 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

闭包并不是 Python 独有的概念。许多其他编程语言都有相同的概念。虽然很多初学者可能听说过，但不知道到底是什么，怎么用。在本文中，我强调了关于 Python 闭包的最基本的知识，希望您能更好地理解这个概念。

# 1.内部和外部函数

如上所述，闭包存在于许多编程语言中，下面的定义是从[维基百科](https://en.wikipedia.org/wiki/Closure_(computer_programming))中检索到的:

> “在[编程语言](https://en.wikipedia.org/wiki/Programming_language)中，**闭包**，也称**词法闭包**或**函数闭包**，是一种在具有[一级函数](https://en.wikipedia.org/wiki/First-class_function)的语言中实现[词法范围](https://en.wikipedia.org/wiki/Lexically_scoped) [名称绑定](https://en.wikipedia.org/wiki/Name_binding)的技术。”——维基百科对闭包的定义

这个定义对许多人来说太专业了，而且不是 Python 特有的。为了帮助您理解这个概念，这里有一个简单的版本:闭包是在外部函数中创建的内部函数，并使用外部函数的变量。它由外部函数作为输出值返回。还是太技术化了？我也这么认为最好用一个真实的例子来理解。我们先来看下面的代码片段:

乘数创建者

在上面的代码中，`double_multiplier`和`triple_multiplier`是两个闭包，因为它们满足闭包的定义，如下所述。

1.  `multipler`是在`multiplier_creator`函数中创建的内部函数，由于它在`multiplier`函数之外，所以被称为外部函数。内部函数通常被称为嵌套函数，因为它嵌套在另一个函数中。
2.  创建的内部函数是外部函数的返回值。需要注意的是，`multiplier_creator`函数直接返回`multiplier`函数，而不是`multiplier`函数的输出值。
3.  内部函数`multipler`使用`n`变量，它是外部函数`multiplier_creator`的一个参数。内部函数对外部变量的访问也称为非局部变量绑定。

非局部变量的绑定可能是闭包最令人困惑的部分。让我们在下一节探讨它。

# 2.局部和非局部变量

当我们使用函数时，我们知道在函数中，我们可以自由地使用任何传递的参数，这些参数被称为局部变量。本质上，函数形成了一个局部作用域，限制了对其变量的访问。

根据上面的例子，`multiplier_creator`函数定义了一个局部范围，参数`n`是一个局部变量。在类似的函数中，内部函数`multiplier`定义了另一个局部范围，参数`number`是一个局部变量。但是值得注意的是，`multipler`函数也使用参数`n`。虽然`n`对于`multiplier_creator`的作用域来说是一个局部变量，但是对于`multipler`函数来说却不是局部变量。因此，在示例中，乘数函数使用非局部变量，也称为自由变量。

除了局部变量和非局部变量的区别，你们中的一些人可能还听说过全局变量，它是在模块级定义的变量。一些相关术语包括全局和内置作用域。有兴趣的读者可以参考我早前关于[这个话题的文章](https://medium.com/swlh/variables-and-scopes-in-python-global-nonlocal-and-local-923a71cb57d4)。

您可能已经注意到，从内部函数的角度来看，我们将访问外部函数的局部变量的过程称为非局部变量绑定。用绑定来描述这个特性是很有意义的。绑定到底是什么意思？让我们在下一节学习它。

# 3.非局部变量绑定

非局部变量的绑定在其他一些语言中也被称为非局部变量捕获来描述闭包的特性。你可以简单地把它概念化为内部函数“拥有”所使用的非局部变量。让我们观察下面的特征:

```
>>> del multiplier_creator
>>> double_multiplier(5)
10
>>> triple_multiplier(5)
15
```

在上面的代码中，我们删除了外部函数`multiplier_creator`，这样这个函数将变得不可访问。但是注意闭包(即`double_multiplier`和`triple_multiplier`)使用`multiplier_creator`的参数`n`。因此，有些人可能认为闭包会停止工作。然而，他们仍然产生了预期的结果。

潜在的原因是闭包已经建立了它所使用的非局部变量的绑定。换句话说，它有一个绑定变量的副本。为了观察这一特性，考虑一些特殊的检查功能，如下所示:

`__code__.co_freevars`允许我们检查闭包绑定的非局部变量的名称，`__closure__[0].cell_contents`允许我们检查绑定的非局部变量的值。你不必知道这些函数的细节，它们只是底层的实现。

重要的是要知道，因为闭包通过绑定拥有自己的非局部变量副本，所以即使外部函数被移除，它们仍然可以工作。

# 4.Nonlocal 关键字和 Unboundlocalerror 异常

当人们创建闭包时，有时会遇到`UnboundLocalError`异常。让我们在下面的代码片段中看到这一点:

闭包—unboundlocalrerror

乍一看，`running_total_multiplier_creator`函数似乎可以有效地生成闭包。然而，当我们使用生成的闭包时，会引发`UnboundLocalError`异常。这个例外是什么意思？如果您阅读追溯消息，您会发现有问题的代码是`running_total += product`。为什么会产生这样的错误呢？以下是解释:

1.  这一行代码本质上被解释为`running_total = running_total + product`。
2.  变量查找顺序称为 LEGB 规则，遵循局部->封闭->全局->内置的顺序。当你在内部函数中运行`running_total = xxx`时，你在内部函数的局部范围内注册了一个局部变量。当 Python 继续运行赋值语句右侧的代码时，又遇到了`running_total`变量，于是 Python 开始查找这个变量，并在局部范围内找到了它。但是，它注意到这个变量还没有被赋值，因为它的赋值语句还没有执行完，这就是为什么错误消息说:`local variable ‘running_total’ referenced before assignment`。当一个变量在赋值前被引用，这就是所谓的未绑定错误。

如果我们回过头来看一下`multiplier`函数，您可能会意识到我们想要使用在`running_total_multiplier_creator`函数的作用域内定义的`running_total`变量，从`multiplier`函数的角度来看，这就是所谓的封闭作用域。为了让内部函数明白`running_total`并不是一个局部变量，我们必须通过使用`nonlocal`关键字来显式化。以下是正确的版本:

闭包—unboundlocalrerror 修复

正如你所看到的，我们简单地声明`running_total`是一个非局部变量，这指示 Python 在查找`running_total`变量时绕过局部范围。

# 5.为什么要关闭？

到目前为止，我们已经回顾了什么是闭包，但是您可能想知道为什么我们要费心提供闭包功能？闭包最常见的应用之一是装饰函数的创建。尽管您可能不知道闭包，但我打赌您可能听说过 decorators。在 Python 中，装饰器是修改其他函数的行为而不影响被装饰函数的算法的函数。如果你想更深入地了解闭包，请参考我的关于装饰者的文章。

在这里，我将简单介绍一下我们刚刚学到的与闭包相关的装饰器。以下代码向您展示了一个示例:

闭包->装饰器

`simple_logger`是一个装饰器，使用它需要一个`@`符号作为名字的前缀，并放置在被装饰函数的上方。当你调用`hello_world`函数时，会发生以下情况:

```
>>> hello_world()
You're about to call <function hello_world at 0x101ce9790>
Hello, World!
You just called <function hello_world at 0x101ce9790>
```

这个特性背后的原因是修饰函数实际上是一个闭包。装饰过程有以下两个幕后步骤:

```
# Step 1
def hello_world():
    print("Hello, World!")# Step 2
hello_world = simple_logger(hello_world)
```

为了证明`hello_world`确实是一个闭包，我们可以像前面一样运行下面的检查。

```
>>> hello_world.__code__.co_freevars
('func',)
>>> hello_world.__closure__[0].cell_contents
<function hello_world at 0x101ce9790>
```

*   修饰函数`hello_world`有一个非局部变量`func`。
*   绑定值是一个函数。作为一个相关点，重要的是要知道函数只是 Python 中的常规对象，因此函数可以被视为其他数据模型变量(例如，列表和字典)。

# 结论

在本文中，我们回顾了闭包的五个最重要的方面。以下是对这些要点的简要回顾:

*   内部函数和外部函数是有区别的。闭包是在外部函数中创建的内部函数。
*   闭包涉及到非局部变量的绑定。
*   一个函数有它的局部作用域。当你使用在函数中创建的变量时，你使用的是局部变量。如果你使用在函数之外创建的变量，你就是在使用非局部变量。
*   关键字`nonlocal`表示一个变量应该被视为一个非局部变量。通常，LEGB 规则适用于变量查找。然而，使用`nonlocal`关键字，Python 将被指示在查找变量时绕过局部范围。
*   装饰是创建替换其原始函数声明的闭包的过程。每一个装饰功能都是一个封闭的引擎盖。