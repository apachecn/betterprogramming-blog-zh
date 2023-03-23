# Python 的“自我”论点到底是什么？

> 原文：<https://betterprogramming.pub/what-is-pythons-self-argument-anyway-21d1c3bc421f>

## 对这场众所周知的争论的幕后调查

![](img/cd1356246bb136c53a33ba3c5a5fb06e.png)

照片由 [Niloofar Kanani](https://unsplash.com/@niloofarkanani?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

每个 Python 开发人员都熟悉`self`参数，它出现在每个类的每个*方法声明中。我们都知道如何使用它，但是你真的*知道它是什么吗*知道它为什么在那里，以及它在引擎盖下是如何工作的吗？

# 我们已经知道的

让我们从我们已经知道的开始:`self` —方法中的第一个参数—引用类实例，如下所示:

此外，这个论点不一定要被称为`self`——它只是一个惯例。例如，您可以使用其他语言中常见的`this`(但不要这样做)。

上面的代码可能是自然而显而易见的，因为您一直在使用它，但是我们只给了`.do_stuff()`一个参数(`some_arg`)，然而这个方法声明了两个参数(`self`和`some_arg`)，这没有意义。代码片段中的箭头显示`self`被翻译成了`instance`，但是它到底是怎么翻译的呢？

Python 内部做的是从`instance.do_stuff("whatever")`到`MyClass.do_stuff(instance, "whatever")`的转换。我们可以在这里结束它，称之为“Python 魔术”，但是如果我们想了解幕后发生了什么，我们需要了解什么是 Python 方法以及它们如何与函数相关。

# 类属性/方法

在 Python 中，没有“方法”对象这样的东西——实际上，方法只是普通的函数。函数和方法的区别在于方法是在类的名称空间中定义的，这使得它们成为该类的一个属性。

这些属性存储在类字典`__dict__`中，我们可以直接访问或者使用`vars`内置函数。代码如下:

访问它们最常见的方式是“类方法”，如下所示:

这里我们使用一个 class 属性来访问函数，正如所料，它打印出`do_stuff`是`MyClass`的一个函数。但是，我们也可以使用实例属性来访问它。看起来是这样的:

在这种情况下，我们得到的是一个“绑定方法”而不是原始函数。Python 在这里为我们做的是将类属性绑定到实例，创建了所谓的“绑定方法”这个“绑定方法”是对底层函数的包装，该函数已经将`instance`作为第一个参数(`self`)插入。

因此，方法是普通的函数，在它们的其他参数前有一个类实例(`self`)。

我们需要查看描述符协议来理解这是如何发生的。

# 描述符协议

描述符是方法背后的机制(以及其他东西)。它们是定义 `__get__()`、`__set__()`或`__delete__()`方法的对象(类)。为了理解`self`如何工作，我们将只考虑`__get__()`，它有一个签名:

但是`__get__()`方法实际上做什么呢？它允许我们在类中定制属性查找——换句话说，当使用点符号访问类属性时会发生什么。考虑到方法只是类的属性，这是非常有用的。这意味着我们可以使用`__get__`方法来创建一个类的“绑定方法”。

让我们通过使用描述符实现一个“方法”来演示这一点，这样会更容易理解。首先，我们创建一个函数对象的纯 Python 实现:

上面的`Function`类实现了`__get__`，这使它成为一个描述符。这个 dunder 方法在`instance`参数中接收类实例。如果这个参数是`None`，我们知道`__get__`方法是从一个类中直接调用的(例如`MyClass.do_stuff`，所以我们只返回`self`。

然而，如果它是从类实例如`instance.do_stuff`中调用的，那么我们返回`types.MethodType`，这是一种手动创建“绑定方法”的方式。

另外，我们还提供了`__call__` dunder 方法。当调用一个类来初始化一个实例时`__init__`被调用(例如`instance = MyClass()`)，当调用实例时`__call__`被调用(例如`instance()`)。我们需要这个，因为`types.MethodType(self, instance)`中的`self`必须是可调用的。

现在我们有了函数实现，我们可以使用它将方法绑定到类，如下所示:

通过给`MyClass`一个类型为`Function`的属性`do_stuff`，我们大致模拟了当您在类的名称空间中定义一个方法时 Python 会做什么。

综上所述，在`instance.do_stuff`等属性访问时，在`instance`的属性字典(`__dict__`)中查找`do_stuff`。如果`do_stuff`定义了`__get__`方法，那么`do_stuff.__get__`被调用，最终调用这段代码:

正如我们现在所知，它将返回一个绑定方法——一个围绕原始函数的可调用包装器，它的参数前有`self`!

如果你想进一步探索，你可以类似地实现静态和类方法——如何做的例子可以在这里的[文档中找到。](https://docs.python.org/3.7/howto/descriptor.html#static-methods-and-class-methods)

# 为什么它会在那里？

我们知道它是如何工作的，但是一个更哲学的问题仍然存在:“为什么它必须出现在方法定义中？”

显式的`self`方法参数是一个有争议的设计选择，但这是一个有利于简化的选择。

Python 的`self`是“越差越好”设计哲学的体现——在这里[描述](https://dreamsongs.com/RiseOfWorseIsBetter.html)。这一设计理念的首要目标是“简单”,定义如下:

> 设计必须简单，无论是在实施和接口。实现简单比接口简单更重要…

这正是`self`的情况——一个简单的实现，以接口为代价，其中方法签名与其调用不匹配。

有更多的理由说明为什么我们有明确的`self`或者更确切地说，为什么它必须存在。吉多·范·罗苏姆在[的博客帖子](http://neopythonic.blogspot.com/2008/10/why-explicit-self-has-to-stay.html)中描述了其中一些，以回应要求将其删除的提议。

# 结束语

Python 抽象掉了很多复杂性，但是在我看来，深入底层细节和复杂性对于更好地理解这种语言的工作原理非常有价值。当出现问题时，这可以派上用场，高级别的故障排除/调试是不够的。

此外，理解描述符可能非常实用，因为它们有一些用例。虽然在大多数情况下，您只需要真正的`@property`描述符，但是在某些情况下自定义描述符是有意义的，比如 SLQAlchemy 中的[描述符，或者](https://docs.sqlalchemy.org/en/14/orm/mapped_attributes.html#using-descriptors-and-hybrids)[自定义验证器](https://docs.python.org/3/howto/descriptor.html#validator-class)。

[](/should-you-be-using-pythons-walrus-operator-yes-and-here-s-why-36297be16907) [## 你应该使用 Python 的 Walrus 操作符吗？(是的。原因如下)

### Python 有争议的赋值表达式——也称为 walrus 运算符——可以改进您的代码，现在是您…

better 编程. pub](/should-you-be-using-pythons-walrus-operator-yes-and-here-s-why-36297be16907) 

```
**Want to Connect?**This article was originally posted at [martinheinz.dev](https://martinheinz.dev/blog/81).
```