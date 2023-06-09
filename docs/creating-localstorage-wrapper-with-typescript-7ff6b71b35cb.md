# 使用 TypeScript 创建本地存储包装

> 原文：<https://betterprogramming.pub/creating-localstorage-wrapper-with-typescript-7ff6b71b35cb>

## 通过添加自定义包装器来改进浏览器 API 的使用

![](img/6dbf9b6e741eef8c6a4b2d2c71581c4a.png)

Christopher Gower 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

今天的文章将对前端开发人员有用，他们希望自己的代码是类型安全的，可读性更好。我将向您展示如何包装不同的浏览器 API 来更好地控制这种情况。例如，我将使用`localStorage`。

# 快速刷新

`LocalStorage`是用于跨浏览器会话存储数据的浏览器存储。和`localStorage`一起工作没什么大不了的。它有一个非常简单的界面:

好吧，你可能会问，“既然它有这么简单的接口，我们为什么还需要任何包装器呢？”说得好，但是包装器实际上帮助我们涵盖了开发的其他部分，而不是简单的可用性。

# 优势

*   第一个优点是实现测试会容易得多。您有一个包装器，它在生产中使用真正的 API，在测试时使用一个虚拟的 API。可以进行不同的模拟，并且可以发现各种弱点。
*   它将为您带来一个单独的地方，您可以在这里定义键的名称。这意味着您不会意外地将数据保存到不同的键中，或者试图从一个不存在的键中获取数据。
*   还有一个好处:如果你出于某种原因需要重命名你的密钥(例如从`token_v1`到`token_v2`，可以在你的包装器中完成。
*   最后但同样重要的是，在环境 API 上编写自己的包装器被认为是一种好的实践。

# 发展中的

因此，它应该是一个具有泛型的抽象类(稍后我会解释它的用途):

让我们添加一个包含实际存储的属性。在我们这样做之前，我们还需要描述可以存储在该属性中的接口:

如你所见，上面的代码完全重复了浏览器`localStorage`。这正是我们所需要的。

下面是具有属性的抽象类:

我们从哪里可以得到真正的存储？当然，从构造函数中借助`getStorage`函数。它默认返回浏览器`localStorage`:

所以在生产中，我们不需要做任何额外的工作。但是在测试的时候，我们只需要传递以`IStorage`为返回值的函数。

让我们开始添加方法。在我们开始之前，我必须说所有的方法都将受到保护，因为它们在类/实例之外不应该是可见的，并且只有继承者可以访问它们。

第一个是`get`:

这里是`T`，我们传递给抽象类的泛型。我们这样做是为了实现额外的类型安全。该方法返回一个值`null`。

在获取数据之前，我们应该传递它。下一个方法是`set`:

我认为一切都清楚了。

只剩下两种方法:`clearItem`和`clearItems`来移除单个键或它们的数组:

我们的最终准则:

好了，因为我们只有抽象类，我们必须创建一个实例。但在此之前，让我们用我们将要使用的所有键来描述一下`enum`。在我们的例子中，这个类将负责令牌:

如你所见，我们已经将`Locals`传递给了`Storage`泛型。这意味着只有来自这个`enum`的键可以在`get`、`set`、`clearItem`和`clearItems`方法中使用。

使用`singleton`是有意义的，因为有一个`Token`类的实例就足够了。

我们向构造函数添加了一个私有访问级别，以防止创建实例。此外，还添加了一个实例属性来缓存类实例。静态方法`getInstance`将返回缓存的实例或者创建一个新的实例。

现在我们准备创建处理数据的方法:

上面的这些方法将用于操作访问令牌。如你所见，我们使用了抽象类中的受保护方法。

让我们对刷新令牌做同样的事情:

此外，我们需要以某种方式删除我们的数据:

最终代码如下:

用法:

# 摘要

也许不是每个项目都需要这样的方法，但知道它还是好的。我希望你已经发现这篇文章有趣和有用。