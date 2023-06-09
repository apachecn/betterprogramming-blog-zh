# 如何使用 Kotlin 密封类进行状态管理

> 原文：<https://betterprogramming.pub/how-to-use-kotlin-sealed-classes-for-state-management-c1cfb81abc6a>

## 使用密封类简化复杂的数据流

![](img/3515c50036af70a32766b2958baec15b.png)

[女同胞](https://unsplash.com/@cowomen?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 介绍

当产品增长时，状态管理是任何平台的关键方面之一。

我在 Google Play 商店的一个应用程序有一个带有多个支付网关的支付屏幕。用户可以通过任何网关进行支付。

付款后，会弹出一个确认窗口，说明购买状态。

# 问题

一开始，我们只有 Google Play 计费(针对应用内购买)。在那个时候，很容易维护像`success`、`failure`、`retry`、`acknowledgment`这样的状态。最近，我们决定增加 PayU 和 Stripe 作为额外的支付网关，这就是状态管理问题的开始。

我们可以肯定的一点是，我们将在未来添加更多的支付网关，因此为每个网关维护不同的流程将耗尽代码管理系统。因此，我们决定创建一个适用于所有网关的独特流程。

这时我们发现了帕特里克·考辛斯(Patrick Cousins)在 2018 年科特林大会上的一次精彩演讲(资源添加在文章末尾)，内容是关于密封类以及他如何在自己的应用程序登录期间使用它们来维护状态。

# 解决办法

所以我们确信我们需要处理密封类的状态管理。在讨论我们如何实现它之前，您应该了解一下密封类。

## 科特林的定义

> 当值可以具有有限集合中的一种类型，但不能具有任何其他类型时，密封类用于表示受限的类层次结构。在某种意义上，它们是枚举类的扩展:枚举类型的值集也是受限制的，但每个枚举常量只作为单个实例存在，而密封类的子类可以有多个包含状态的实例。

正如 Cousins 所说，这个定义本身在理解密封类在处理复杂流时如何有用方面非常清楚。

现在，让我们看一个如何使用密封类的简单例子。下面是发起网络请求时维护状态的密封类。这里有两种状态:`Loading`和`Error`。

`Loading`:如果出现真正的加载，这个状态返回一个布尔值——否则，它是隐藏的。
`Error`:该状态以字符串形式返回错误信息。当一个错误被触发时，加载被隐藏，一个带有错误信息的弹出窗口将出现。

现在，在另一边，我们必须使用 Kotlin 中的`when`语句来处理不同的状态，如下所示。

既然我们已经在如何使用密封类的问题上达成一致，那么让我们看看我们是如何使用它们来解决购买状态流的。

这里我们要在两个地方进行 API 调用:

1.  去购物。
2.  确认购买成功。

我们创建了一个密封类来处理请求的所有状态。以下是购买的密封类:

然后，如果购买成功，我们将确认购买，并在弹出窗口中向用户显示结果。对于确认流状态，我们创建了以下类:

这两个密封的类可以处理所有支付网关的购买和确认流。但是我们过去把所有的类放在一个类中，就像一个树形结构。看一看:

在这里，我们使用了嵌套的密封类概念，并在每个支付网关中使用了嵌套的确认，所以另一方面，很容易评估确认是从哪个网关完成的。看一看:

这在我们的情况下似乎更好，因为我们有需要触发的网关特定功能。

但是我们也可以在单个文件中以不同的方式维护密封的类，而不需要任何树结构，如上所示。

然后，您可以访问这些状态，如下所示:

当您没有特定于网关的事情要做时，这是使它工作的一个简单方法。务实一点，选择适合自己的。

# 有用的链接

 [## 科特林的密封课堂

### 了解如何在 Android 中使用密封类

medium.com](https://medium.com/@sgkantamani/sealed-classes-in-kotlin-e48e072daca8)