# 停止在 Python 中滥用*args 和**kwargs

> 原文：<https://betterprogramming.pub/stop-abusing-args-and-kwargs-in-python-560ce6645e14>

## Python 初学者

## 他们会回来缠着你…

![](img/f08623f8bfa28f8c6f2dea0d111c3274.png)

[马太·亨利](https://unsplash.com/@matthewhenry?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

网上有很多教程旨在教你在 Python 中定义函数时如何使用`*args`和`**kwargs`。也许你已经花了几个小时试图找出如何释放他们的潜力。也许，在所有的研究之后，你现在对他们有信心了。

*不要！*

强大的工具是危险的。你可能让你的一天变得轻松了，但是记住我的话，它以后会回来困扰你。

*但是为什么呢？*

# 一些基础知识

Python 函数中的参数可以接受两种类型的参数:

*   按位置传递的位置参数。
*   由关键字提供的带关键字的参数。

```
def foo(start, end):
    print(start, end)
```

例如，`foo('Hi', end='Bye!')`向函数`foo`提供一个位置参数`'Hi'`和一个带有关键字`end`的关键字参数`'Bye!'`。函数的参数是预定义的；函数中接受的参数数量是固定的。然而，情况并非总是如此。

允许你传递任意数量的位置参数给你的函数。星号`*`是一个解包参数。它们被打包成函数中的一个可迭代元组。

另一方面，`**kwargs`允许您向函数传递不同数量的关键字参数。因为每个带关键字的参数都有一个关键字和值，所以它被分组为函数中的一个可迭代字典。

![](img/f8fc48cfc9fdcd907017ab6d8f543854.png)

苏珊·霍尔特·辛普森在 Unsplash[上的照片](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 问题是

在大多数情况下，你并不真的需要`*args`和`**kwargs`。您是否经常不知道预定义函数应该接收多少个参数？

如果你滥用它们，代码就很难调试，因为你让任意数量的参数传递给函数，函数可能会有不可预知的行为。

> 显性比隐性好。Python 的禅

# 什么时候用？

简而言之:在你真正需要的时候使用它们。例如，一个函数有许多可选字段，其中一些字段只在某些情况下使用。比方说，一个函数绘制了一个图形，你可以传递各种可选参数来修改它的颜色、样式、大小等等。

每次你使用`*args`和/或`**kwargs`时，确保你做了非常清楚的文档以避免混淆。

在一种情况下，它们的使用可能是不可避免的。如果您正在为一个带有未知参数的函数创建一个包装器，那么您必须接受任意数量的位置和关键字参数，然后将它们传递给该函数。

例如，Python 中的 decorators 作为包装器工作，它改变代码的行为，而不改变函数代码本身，因此增加了额外的功能。

![](img/0f234c485d212dce80cdcb9c15089073.png)

Alexander Schimmeck 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在下面的例子中，我们构建了`trace`,它打印出执行函数的名称作为健全性检查。在函数的顶部使用`@trace`将装饰器应用于函数，如下所示。因为我们想将这个装饰器应用于任何具有任意数量参数的函数，所以我们需要使用`*args`和`**kwargs`。

# 外卖食品

***可能的话避开他们*** *。*

注意`args`和`kwargs`只是约定俗成的命名。你可以给它们起任何你喜欢的名字。星号`*`和`**`让它们变得强大。

感谢阅读！你可以[注册我的时事通讯](http://edenau.mailchimpsites.com/)来接收我的新文章的更新。如果您对进一步提高 Python 技能感兴趣，以下文章可能会有所帮助:

[](https://towardsdatascience.com/5-python-features-i-wish-i-had-known-earlier-bc16e4a13bf4) [## 我希望我能早点知道的 5 个 Python 特性

### 超越 lambda、map 和 filter 的 Python 技巧

towardsdatascience.com](https://towardsdatascience.com/5-python-features-i-wish-i-had-known-earlier-bc16e4a13bf4) [](https://towardsdatascience.com/4-common-mistakes-python-beginners-should-avoid-89bcebd2c628) [## Python 初学者应该避免的 4 个常见错误

### 我很艰难地学会了，但你不需要

towardsdatascience.com](https://towardsdatascience.com/4-common-mistakes-python-beginners-should-avoid-89bcebd2c628) [](https://towardsdatascience.com/4-hidden-python-features-that-beginners-should-know-ec9de65ff9f8) [## 初学者应该知道的 4 个隐藏的 Python 特性

### 如何轻松增强您的 Python 代码

towardsdatascience.com](https://towardsdatascience.com/4-hidden-python-features-that-beginners-should-know-ec9de65ff9f8)