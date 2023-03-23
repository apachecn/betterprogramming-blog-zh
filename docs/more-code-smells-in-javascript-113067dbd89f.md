# JavaScript 中的代码味道

> 原文：<https://betterprogramming.pub/more-code-smells-in-javascript-113067dbd89f>

## 我们看特征羡慕和亲密类

![](img/d51cf673288c779432df523ba3bc4131.png)

照片由[米米·蒂安](https://unsplash.com/@mimithian?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/coding-at-work?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在编程中，代码气味是一段代码的特征，表明可能有更深层次的问题。这是一种主观特征，用来通过观察代码来判断代码是否具有良好的质量。

在这篇文章中，我们看到了 JavaScript 代码中更多的代码味道，包括特性嫉妒和过于亲密的类。

# 特征羡慕

特性嫉妒意味着一个类使用了另一个类太多的特性。这意味着一个类中的大部分引用另一个类中的一些东西。

这尤其适用于在另一个类中过度使用一个类中的字段

例如，如果我们有以下内容:

`ShapeCalculator`类使用了来自`Rectangle`的大量字段。当我们不需要的时候，我们从`ShapeCalculator`类中的`Rectangle`获取所有字段。

这打破了`Rectangle`类中的封装，因为我们在不需要的时候暴露了字段`height`和`width`。

相反，我们应该避免使用来自`ShapeCalculator`类中`Rectangle`对象的`height`和`width`字段，并在`Rectangle`类中编写一个方法，然后在`ShapeCalculator`类中调用它，如下所示:

这要好得多，因为我们在来自`Rectangle`类的`ShapeCalculator`中引用的只是`getArea`方法。我们不再需要担心`height`和`width`字段的变化，因为它们被封装在`getArea`方法中。

只要`getArea`返回一个带有`Rectangle`区域的数字，我们就不关心`Rectangle`类中的字段或方法的实现。

![](img/db9b790159bf165668d17384bbea4907.png)

照片由 [Wynand Uys](https://unsplash.com/@wynand_uys?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# *不恰当的亲密关系*

依赖于另一个类的实现细节的类被认为是过于亲密的。

我们不想要依赖于其他类的实现的类，因为这意味着当第一个类改变时，我们必须改变两个类。

没有足够的字段封装或方法实现。

缺少封装可能是由于暴露了类的字段。让我们直接设置字段值在大多数情况下可能并不好。

编写遵循某个接口的代码是不可能的，因为很难改变一个类来遵循一个接口，因为如此多的其他类依赖于给定类的实现。

类之间过多的交互也会导致混乱，因为工作流很难跟踪。这是面向对象版本的意大利面条代码。

当我们改变一个类时，其他类过于依赖的任何类的改变也会产生问题，因为它们是如此紧密耦合，我们必须改变所有依赖它的类来改变它。这加剧了代码的可维护性问题，除了意大利面条式的代码。

例如，如果我们有以下类:

我们可以看到`ShapeCalculator`大量使用了`Box`类的字段和方法。

在`ShapeCalculator`类中，我们引用了`Box`的`length`、`width`、`height`属性和`getSurfaceArea`方法。

如果我们在`Box`类中改变了什么，我们也必须在`ShapeCalculator`中改变它。例如，如果我们重命名`Box`类中的所有内容，如下所示:

我们必须在`ShapeCalculator`计算器中进行同样的更改。这完全是一场噩梦，甚至没有那么复杂。想象一下，如果我们的类有更多的字段和方法，它很快变得不可维护。

交互也很混乱。有些字段在`ShapeCalculator`中用于计算，有些方法直接从`Box`类中使用。

我们应该做的不是在`ShapeCalculator`类中引用`Box`类中的任何字段，而是改变`Box`类来提供`ShapeCalculator`类需要的所有方法，如下所示:

那么`ShapeCalculator`可以改写为:

正如我们所见，`ShapeCalculator`类只引用了`Box`类的方法，而没有混合引用来自`Box`类的字段和方法。

现在我们不必担心字段名称是否在`Box`类中被改变，因为我们从未使用过它们。

当我们写类的时候，我们应该只暴露我们需要的。大多数字段可能是隐藏的，这样它们就不会被外部类使用。这减少了维护代码的工作量，因为它们不必在所有使用它们的类中进行更改。

它保持了一切的模块化和独立。同样，出于同样的原因，我们只公开我们需要使用的方法，而对公众隐藏其他方法，如 helper 方法。

类之间过多的耦合和引用是没有好处的！