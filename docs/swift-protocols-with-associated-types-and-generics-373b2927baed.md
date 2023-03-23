# 具有相关类型和类属的 Swift 协议

> 原文：<https://betterprogramming.pub/swift-protocols-with-associated-types-and-generics-373b2927baed>

## 高级协议

![](img/f0e30814917730f86a0d7b0b2f75d31b.png)

Chris Ried 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

今天，我们来看看臭名昭著的`Protocol can only be used as a generic constraint because it has Self or associated type requirements`错误信息。

Swift 文档将协议描述为“适合特定任务或功能的方法、属性和其他要求的蓝图。”使用协议来定义我们代码的不同部分的责任，使得它更容易阅读，并且有助于维护一个干净的架构。

尽管 Swift 非常适合面向对象编程，但它是作为面向协议的语言而设计的。正如苹果公司的 Dave Abrahams 在 2015 年 WWDC 上所说，“我们 Swift 有一句谚语。不要从一门课开始。从一个协议开始。”

# 行动中的协议

让我们通过为一个简单的在线钱包应用程序创建一些基本逻辑来看看协议是如何工作的。首先，我们肯定需要从服务器获取一些数据。

获取数据后，我们将把它解析成某个对象。因为我们想保留我们的选项，所以使用关联类型是个好主意。

我们从协议开始。现在，让我们提供具体的实现。由于我们正在构建一个在线钱包应用程序，我们将需要一个`User`、`Wallet`，以及它们相应的提取器和解析器。

剩下唯一要实现的就是这个逻辑的消费者。

# 问题是

如您所见，`UserFetcher`和`WalletFetcher`的实现非常相似，因此重构代码并使用泛型是有意义的。

这样声明我们的`dataParser`会导致一个错误:`Protocol 'DataParsing' can only be used as a generic constraint because it has Self or associated type requirements`。

试图通过专门化`dataParser`来修复我们的错误会给我们带来另一个错误:`Cannot specialize non-generic type 'DataParsing'`。

出现这个问题是因为编译器需要在编译时知道具体的类型。大多数 Swift 开发人员在其职业生涯的某个阶段都会遇到这种情况，如果您仍在阅读这篇文章，那么您很有可能就是其中之一。

# 解决方案

解决这个问题的常见方法是使用类型擦除。顾名思义，我们通过提供具有具体实现的包装器来消除类型信息。

我们现在可以使用我们的`GenericDataFetcher`并从`UserFetcher`和`WalletFetcher`中删除重复的代码。

唯一需要更新的是`DataManager`代码。

# 最后的想法

我描述的解决方案在 Swift 标准库中的多个地方使用。然而，它依赖于分配在堆上的闭包。因此，我建议你谨慎使用。因为它也增加了代码的复杂性，所以我尽量避免它，并且更喜欢以一种不需要这种变通方法的方式来重构代码。我们用来演示这个问题的例子肯定可以进行不同的重构，但这是另一篇文章的主题。

感谢您的阅读，如果您有任何意见或问题，请告诉我！