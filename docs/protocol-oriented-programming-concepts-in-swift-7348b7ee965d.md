# Swift 中面向协议的编程概念

> 原文：<https://betterprogramming.pub/protocol-oriented-programming-concepts-in-swift-7348b7ee965d>

## 利用协议增加代码重用

![](img/e8ca3241d5477ed4c9a42959011093eb.png)

照片由[米娅·贝克](https://unsplash.com/@miabaker?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/macbook-pro?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

面向协议的编程可能是 Swift 编程中更高级的主题之一。对某些人来说，这意味着定义协议应该伴随项目中任何类或结构的公共接口。对其他人来说，这是一个只被喜欢炫耀他们疯狂技能的开发人员使用的概念。在本文中，我们来看看如何利用协议在我们的程序中做有用的工作。

# 让我们直接进入代码吧！

我们将扩展一些标准库组件来添加统计分析的方法和属性。我们大多数人可能已经有了一些扩展的经验——通过构建我们经常使用的东西来展示有用的东西更容易。

让我们从计算一个数据集的总和开始。通过将所有测量值保存在一个数组中，我们可以迭代元素，将数字相加，并返回结果。很简单，对吧？

虽然它可能很容易，但我们希望我们的解决方案是可重用的。我们通过分析我们的需求并聚焦于数据和结构的公共属性和功能来实现可重用性。让我们浏览一下:

*   数据结构需要包含数字，以便我们计算总和。因此，我们强加了一个约束，即我们的方法应该只在元素为`Numeric`时才可用。
*   我们不关心我们是否将数据存储在数组、集合或你叔叔地下室的纸箱中。我们所关心的是迭代元素的能力，以及属于`Sequence`协议的功能。

在考虑这些要求时，请查看以下实现:

计算数值元素之和的序列协议的扩展。

如您所见，我们只需要一个单一的实现来涵盖所有感兴趣的数据结构。这个属性现在出现在每个符合`Sequence`协议并包含`Numeric`元素的结构上。更好的是，对于包含除数值项以外的内容的序列，该属性甚至不存在！

# 延伸更多

让我们继续沿着统计分析的道路前进，实现几个允许我们计算数字数据集的平均值、样本方差和样本标准差的属性。让我们看看我们的要求:

*   这些操作还要求元素是数字。因为我们感兴趣的值可能是实数，所以我们需要返回一个尽可能精确的浮点数。来回个`Double`。然而，`Double`不支持从`Numeric`类型初始化，所以我们需要为`BinaryInteger`和`BinaryFloatingPoint`都实现。
*   数据结构需要访问我们刚刚实现的`sum`属性，但是它也需要知道元素的总数。符合`Sequence`协议并且具有`count`属性的最小公分母是`Collection`。

收集协议的扩展，用于计算一些统计属性。

正如您在上面看到的，我们需要实现这些属性，以便它们对整数和浮点类型都可用。有或多或少的方法可以绕过这一点，但这是一种我们可能不得不不时处理的情况。我们暂时就这样吧。

# 计算模式

另一个有用的统计操作是计算数据集的众数。也就是说，确定从集合中取样时最有可能出现的元素。如果只有一个这样的元素，则数据集为*单峰*。如果多个项目的可能性相等，则数据集是*多模态的。*我们来看看需求:

*   这个数据集的元素需要有相等的可比性，否则我们就无法判断哪个更有可能出现。对于我们的特定算法，我们还需要项目是`Hashable`。幸运的是，`Hashable`协议也要求符合`Equatable`。
*   我们的数据结构只需要迭代元素的能力。正如我们在第一个例子中看到的，这个功能属于`Sequence`协议。

看一下这个实现:

决定数据集模式的序列协议的扩展。

这些方法使用一个`Dictionary`来计算序列中每个元素的出现次数，然后挑选出最常见的元素。这些方法受到声明式编程的启发，并使用一系列高阶函数来计算它们的回报。

# 更多灵感

Swift 的标准库使用了大量面向协议的编程。如果你需要一些灵感，我鼓励你去[看看](https://github.com/apple/swift)！

如果您有兴趣阅读更多关于 Swift 在数学和统计环境中的发展，我也鼓励您阅读我以前的文章， [***理解蒙特卡罗方法使用 Swift***](https://medium.com/better-programming/understanding-monte-carlo-methods-using-swift-82e0734b6e89) **。**

[](https://medium.com/better-programming/understanding-monte-carlo-methods-using-swift-82e0734b6e89) [## 使用 Swift 了解蒙特卡罗方法

### 我们如何使用蒙特卡罗模拟来评估风险和回报？

medium.com](https://medium.com/better-programming/understanding-monte-carlo-methods-using-swift-82e0734b6e89)