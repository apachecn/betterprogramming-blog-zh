# Python 默认参数的意外工作

> 原文：<https://betterprogramming.pub/the-unexpected-workings-of-pythons-default-arguments-fe1a4addb1dc>

## 可以帮助你创造新鲜的物体

![](img/7343dce0602d5d72ddfb5f0419f45260.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com/s/photos/fixed?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

默认参数在 Python 代码中很常见。它们是 Python 提供的众多灵活性之一，可以让我们的代码更干净、更灵活。然而，如果您不熟悉默认参数，它们也会带来奇怪的副作用。

让我们来看看下面的函数:

看起来很无辜，对吧？

但是如果我们真的运行这个，这是我们的输出:

尽管这只是一个玩具示例，但我们可以很容易地看到批处理过程中的一段潜在代码，如:

人们期望根据我们提供的输入`user_batch` 得到一个`core_users`列表。

然而，你会发现你的`core_users`列表并不是每批都重新设置，而是会一直延续下去。

这是怎么回事？

# 默认参数

与您的想法相反，默认参数并不是每次调用函数时都重新创建，而是在定义函数时创建一次。

这就是为什么我们可以看到这种奇怪的行为，因为我们的`core_users`列表实际上并不是每次调用函数时都创建一个新的列表。

那么，我们能做些什么呢？

好吧，一个解决方案，你们中的一些人可能一看到上面的函数就已经想到了，就是把我们的定义移到函数体中:

通过这个简单的更改，我们现在确实获得了每次调用都刷新列表的预期行为。如果我们确实需要提供种子列表的功能，我们可以将它作为可选输入来提供，如下所示:

# 类和实例变量

关于这种行为需要注意的重要一点是，它不仅仅是函数中的默认参数集，它也适用于类变量。

让我们来看一个跟踪他们的`email`和喜欢的帖子列表的基本用户类。

如果我们现在从这个类中创建两个对象并尝试操作这些字段，我们可以看到 email 字段在实例之间会有所不同，但是`liked_posts`将被共享。

这里的解决方案可能您已经知道了，那就是用实例属性替换类属性。

# 默认工厂

尽管当我们用额外的方法创建成熟的类时，使用实例变量是有意义的，但有时我们在使用`attrs`时可能会忘记这个原则。

并创建两个独立的实例:

尽管我们通常最终会将我们的值定义为类本身的初始化参数，但是如果我们不这样做，而是在以后修改它，我们仍然会得到一个共享变量，这肯定不是我们想要的。

这里的解决方案是使用默认工厂，它将创建输入变量的深层副本，从而为您创建一个全新的字段。

# Pydantic 和数据类

Pydantic 和 dataclasses 是数据对象的另一组流行选项，分别用于解析和验证逻辑以及基本数据对象。

你可能想知道，这些相同的效应在这里也适用吗？

在 Pydantic 的情况下，您不必担心。设置一个可变的默认参数仍然会为您定义的每个新的类实例创建一个新的参数。

另一方面，如果你没有使用工厂，`dataclasses`不会让你使用一个可变字段作为你的缺省值，也就是说，这将会失败。代码如下:

而是直接提示您使用默认工厂。

# 摘要

Python 中的默认参数可能会以意想不到的方式工作，因为它们只在定义对象时创建一次。由于这种行为，我们在使用可变默认参数时必须小心，无论是作为类变量还是在方法中。

避免这些问题的一个方法是，对于方法，在方法体中设置默认值，对于类，在类初始化方法中定义变量。

当使用`attrs`或`dataclasses`时，我们可以使用一个默认的工厂，这样每个实例都有一个为它创建的新对象，而不是重用同一个只创建过一次的可变对象。