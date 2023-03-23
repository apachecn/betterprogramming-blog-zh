# Django 模型装饰者——实现的技巧和诀窍

> 原文：<https://betterprogramming.pub/django-model-decorators-tips-and-tricks-with-implementation-c4c027d24a7f>

## 学习一次性编写功能，并在模型或应用程序之间共享

![](img/415a76a2214846bb51a74be7e9813bc7.png)

照片由[斯蒂芬·汉密尔顿](https://unsplash.com/@stephen2520?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 动机和路线图

我们打算将功能应用于 Django 模型，该模型可以被复制并容易地应用于多个模型。我们将讨论一些示例，并通过执行以下操作来解决这些问题:

1.  向模型中添加字段
2.  向模型添加属性
3.  添加一个工厂装饰器，附加一个默认的 serialiser 供 REST 框架使用

在我们开始解决上述问题之前，让我们快速刷新一下关于 decorators 和 Django 模型的几个要点。如果您对这些完全满意，请随意跳过。

![](img/2bb5664175582e0aa99a882487c8b86f.png)

照片由[真诚媒体](https://unsplash.com/@sincerelymedia?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 装修工:复习

装饰器是应用于函数(或类)的函数，导致丰富的功能。本质上，它们是将函数应用于函数的语法糖，将一个函数“包装”到另一个函数中。

有很多关于装饰者的教程解释了他们是如何工作的，所以要深入理解请参考这些。

为了这篇文章，我们将强调理解一个非常重要的部分:装饰者作用于函数/类，在它们周围放置一个层。

看看这个装饰器，它在被包装的函数执行之前和之后起作用。然后查看如何使用它的两个等价选项。代码如下:

运行任何一个都会产生相同的输出，如下所示:

```
In [2]: func1("Hello World!") # same for func2(...)
I cam do something before the function call!
Hello World!
I can do something after the function call!
```

![](img/a073428306c6bd94b8414858397edc6f.png)

# Django 模型

Django 模型被定义为用于定义数据库结构以及提供底层数据库的抽象接口的类。当这些被使用时，例如在进行迁移时，后台会发生相当多的“奇迹”。我们将了解其中一些是如何工作的。

请看这个例子:

```
from django.db import modelsclass DummyModel(models.Model):
    number = models.IntegerField()
```

这将在您的数据库中生成一个带有整数列和 ID(.pk)当然。

# 1.向模型中添加字段

假设我们想在模型上放置一个字段，如何在装饰器中实现呢？对于一个简单的类，这将是:

如果你尝试用 Django 模型来做这件事，那它就不起作用了！没有抛出错误，但是也没有发生任何事情。例如，使用以下代码:

等同于上述，文本字段`1`不会被创建，也不会被添加到数据库中。但这是为什么呢？

模型的创建不仅仅是向 Python 类添加成员:神奇的事情发生在使用 field 对象的`contribute_to_class`方法时。让我们看看它是如何工作的，以及我们应该对上面的代码片段做些什么。

这就是我们想要它做的:在模型上创建一个 UUID 字段，它也将出现在迁移中。

这是使用抽象模型类从派生的替代方法，是为程序员提供的一种选择。让我们看看我们还能为这些装饰者做些什么。

# 2.向模型添加属性和额外功能

假设有一些功能，比如您想要添加到多个模型中的公共派生参数。让我们看看如何用属性来扩展上述内容。

例如，如果您想要跟踪模型的创建和更改时间，并知道模型在数据库中的年龄，那么您可以执行以下操作:

这将对模型应用创建日期和修改时间字段。创建日期是在创建时记录的，以后不能更改，而修改时间是在每次模型发生变化时更新的。

更新时间的一个注意事项是，它仅适用于 Python 代码更改模型，不适用于在数据库上评估直接 SQL 的情况[1]。这是由于 Django 处理变化和触发信号的方式[2]。

模型的年龄是一个派生参数，取决于当前时间，因此它作为模型对象的一个属性来实现。

为了清楚起见，这相当于将这些字段和属性直接写入类中(也参见[1]):

*n.b.* 这个设计也是一个选择，也有其他方法可以达到同样的结果，例如创建一个抽象模型类来派生(可能使用多个继承)。这种选择可能会使代码更加易读和优雅。

# 3.为模型创建一个通用且简单的序列化程序

在使用 REST 框架的情况下，可能需要许多序列化器，其中一些非常简单和相似。这里有一个技巧来添加一个默认的简单串行器到模型中，可以和它们一起导入。这或多或少有点像序列化工厂

下面是一个简单的序列化器，可以为模型反复创建:

大约两年后，应该创建一个工厂函数，为任何给定的模型创建序列化程序。我们也可以用模型本身的装饰器来做这件事:

这可以以多种方式扩展，例如提供通用的 APIViews，或者在被许多模型使用的情况下向它们添加更多的功能。

# 结论

我们已经看到了如何将 decorators 应用到 Django 模型中，释放各种各样的 Pythonic 功能。在大多数情况下，这些是人们可以做出的选择，或者决定以不同的方式获得类似的结果。

非常感谢您阅读这篇文章。希望你学到了或者看到了新的东西！

![](img/07e24c5637bd821e5219877e9caf25a5.png)

照片由[米歇尔丁](https://unsplash.com/@michelleding?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 参考资料:

*   [1][https://stack overflow . com/questions/3429878/automatic-creation-date-for-django-model-form-objects](https://stackoverflow.com/questions/3429878/automatic-creation-date-for-django-model-form-objects)
*   [2][https://docs.djangoproject.com/en/3.2/ref/signals/#pre-save](https://docs.djangoproject.com/en/3.2/ref/signals/#pre-save)