# 如何在 Python 中使用神奇的@staticmethod、@classmethod 和@property 装饰器

> 原文：<https://betterprogramming.pub/how-to-use-the-magical-staticmethod-classmethod-and-property-decorators-in-python-e42dd74e51e7>

## 学习 Python 中的特殊装饰器

![](img/b2aa8711108fe692d3b4a7a468b9da1b.png)

[阿尔莫斯·贝托尔德](https://unsplash.com/@almosbech?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

装饰器是一个函数，它接受另一个函数作为输入，扩展它的行为，并返回一个新函数作为输出。这是可能的，因为在 Python 中，函数是[一级对象](https://en.wikipedia.org/wiki/First-class_citizen)，这意味着它们可以作为参数传递给函数，也可以从函数返回，就像 string、int 或 float 等其他类型的对象一样。装饰器可以用来装饰一个[函数](https://medium.com/codex/understand-and-master-the-decorator-in-python-481aa444933f)或者一个[类](https://medium.com/me/stats/post/edf826196fbf)。

在这篇文章中，将介绍三个特殊的装饰者，即`@staticmethod,` `@classmethod`和`@property`。这些“神奇的”装饰器可以让我们的开发工作变得非常方便，并使您的代码更加整洁。

# **@静态方法**

在 Python 和其他编程语言中，静态方法是一种不需要创建类实例的方法。对于 Python 来说，意味着静态方法的第一个参数不是`self`，而是常规的位置或关键字参数。此外，静态方法可以没有任何参数:

在这个例子中，`__init__`是 Python 的一个保留方法，作为类的构造函数。`get_number`是类的常规实例方法，需要创建一个实例。尤其是`get_emergency_number`是一个静态方法，因为它是用`@staticmethod`装饰器装饰的。此外，它没有将`self`作为第一个参数，这意味着它不需要创建`Cellphone`类的实例。实际上，`get_emergency_number`可以作为一个独立的函数工作。然而，把它放在`Cellphone`类中是有意义的，也是直观的，因为手机应该能够提供紧急号码。

这是一个超级简单的例子。实际上，在实践中，如果`Cellphone`类有一个`country`属性，`get_emergency_number`方法将成为一个实例方法，因为它需要访问`country`属性来提供正确的紧急号码。然而，这个例子应该清楚什么是静态方法。

# **@classmethod**

在 Python 中，一个类方法是用`@classmethod`装饰器创建的，并且需要类本身作为第一个参数，写为`cls`。类方法通常作为工厂方法工作，并使用提供的参数返回类的实例。然而，它并不总是必须作为工厂类工作并返回一个实例。您可以在类方法中创建一个实例，做您需要的任何事情，而不必返回它:

在这个例子中，`iphone`是一个类方法，因为它是用`@classmethod`装饰器装饰的，并且将`cls` 作为第一个参数。这里它是一个工厂方法，返回一个`Cellphone`类的实例，并将`brand` 预设为“Apple”。

类方法在第三方库中非常常用，例如在 *Scrapy* 网络抓取框架中，它被用来[定制一个爬虫](https://github.com/scrapy/scrapy/blob/06f3d12c1208c380f9f1a16cb36ba2dfa3c244c5/scrapy/spiders/crawl.py#L135-L139):

在*刺儿头*的情况下更复杂，但本质是一样的。在实际工作中，如果恰当地使用类方法，可以大大减少代码冗余，使代码更具可读性和专业性。要记住的关键点是，您可以基于类方法中的一些特定参数来创建类的实例。这样，您就不需要在代码的其他地方重复创建实例，从而使您的代码更加简洁。

# **@房产**

在上面的代码片段中，有一个名为`get_number`的函数，它返回一个`Cellphone`实例的编号。我们可以稍微优化一下这个方法，返回一个格式化的电话号码:

如我们所见，在这个例子中，当我们试图获取一个手机号码时，我们不直接返回它，而是在返回它之前做一些格式化。这是使用`@property`装饰器的完美例子。在 Python 中，使用`@property`装饰器，你可以使用 *getter* 和 *setter* 非常方便地管理你的类实例的属性。上面的代码可以用`@propery`重新写成这样:

但是，如果我们像上面这样运行代码，就会遇到一个`**AttributeError**`，无法设置属性。原因是，如果我们已经使用`@property`来获取属性，我们需要使用*设置器*来设置属性。让我们创建一个 *setter* ，语法乍一看可能很奇怪:

然而，这一次，我们有一个`**RecursionError**`错误。这是因为`number` 属性由于装饰不再是常规属性。注意，我们有两个同名的`number`方法。`self.number`现在是一个带有 *getter* 和 *setter* 的 [*property*](https://docs.python.org/3/library/functions.html#property) 对象，而不再是一个带有字符串值的实例属性。

为了解决这个问题，我们需要为 *getter* 和 *setter* 中的“实数”使用不同的属性名。让我们引入一个下划线前缀，使其成为私有属性，避免名称冲突。实际上，使用 *getter* 和 *setter* 的全部意义在于，不应该直接获取和更改类实例的属性，而是通过一些逻辑来完成。在这个例子中，在 *getter* 中，我们在返回数字之前对其进行格式化。在*设置器*中，我们在将数字设置为属性之前检查它是否是有效数字。让我们看看它是否适用于此更新:

万岁，成功了！让我们试着给手机设置一个无效号码，看看会发生什么:

```
cellphone.number = "123"
# ValueError: Invalid phone number.
```

太棒了，它像预期的那样工作。无效号码将被拒绝。

如果您想知道为什么 *getter* 和 *setter* 的语法如此怪异，您可以通过弄清楚 decorator 是如何工作的来理解它。[装饰器是一个特殊的函数，它返回一个装饰过的函数](https://medium.com/codex/understand-and-master-the-decorator-in-python-481aa444933f)。在这个例子中，使用了内置的 [*属性*](https://docs.python.org/3/library/functions.html#property) 类。属性是一个类，尽管它是小写的。很奇怪，不是吗。但这还不是最奇怪的部分。属性类的第一个参数是一个 getter。当您试图访问创建的*属性*对象时，将触发 *getter* 函数，例如(`cellphone.number`)。

因此，对于本例中的 *getter* 方法、*即第一个修饰过的`number`方法，我们将得到类似于:*

```
number = property(number)
```

*   圆括号中的`number`是被修饰的`number`方法( *getter* 函数)。
*   左边的`number`是由*属性*类返回的*属性*对象。

一个*属性*对象有一个 *setter* 方法，可以为*属性*对象设置 *setter* 方法。因此，对于 *setter* 函数，即第二个修饰过的`number`方法，你会得到类似于:

```
number = number.setter(number) 
```

*   右边的`number`是正在装饰的*设定器*方法`number`。
*   中间的`number`就是上面返回的`number`(一个`property`对象)，用`@propery`装饰的那个。
*   左边的`number`是最终的*属性*对象，同时分配了 *getter* 和 *setter* 。

它相当于以下形式:

```
number = property(number).setter(number)
```

看看你能否弄清楚每个`**number**`是什么意思。

`@property` 装饰器和*获取器*和*设置器*的技术细节相当复杂，并且与 Python 中的[描述符概念](https://docs.python.org/3/howto/descriptor.html)相关。不要灰心，在使用之前，你不需要了解`@property`装饰器的所有技术细节。你可以像在`Cellphone`类中演示的那样使用它。非常简单的语法。

本文用简单的例子介绍了 Python 中三个神奇的装饰器，即`@staticmethod`、`@classmethod`和`@propery`。您现在可以开始使用它来优化您的代码，使您的代码更加专业。

代码放在这里供您参考: