# Swift 中的 CustomStringConvertible 协议是什么？

> 原文：<https://betterprogramming.pub/what-is-the-customstringconvertible-protocol-in-swift-cbb766afac4d>

## 引擎盖下的细绳细节

![](img/5dd2de8dd0baea5ad082626bb3451730.png)

由[诺德伍德主题](https://unsplash.com/@nordwood?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

当您需要在控制台中打印一些东西时,`CustomStringConvertible`协议变得很重要。记住，`print()`函数将代表控制台中对象的文本形式。然而，该功能有其自身的局限性，并不总是如您所愿。

我举个例子澄清一下。如果你想定义一个`Car`类:

您可以看到`print`语句没有正常工作。在这种情况下，`CustomStringConvertible`可能非常有用，因为它有一个必需的计算属性(`description`)，返回实例的`String`表示。

事不宜迟，让我们在实践中探索`CustomStringConvertible` 。

# 我们开始吧

1.  通过添加冒号及其名称来采用协议。
2.  通过添加`description` computed 属性来符合协议。

让我们用一个新的协议来考虑前面的例子:

# 包扎

这就是你对`CustomStringConvertible`应该了解的全部。我希望你觉得这很有趣！

对其他相关协议感兴趣吗？欢迎访问我的其他相关文章:

*   [在 Swift 中创建您自己的协议](https://levelup.gitconnected.com/creating-your-own-protocols-in-swift-e266707892b7)
*   [Swift 中的可编码协议是什么？](https://levelup.gitconnected.com/what-is-codable-protocol-in-swift-50103c2d9d02)
*   [Swift 中的可比协议是什么？](https://levelup.gitconnected.com/what-is-comparable-protocol-in-swift-8cb854e30cf7)
*   [Swift 中的公平协议是什么？](https://medium.com/cleansoftware/what-is-the-equatable-protocol-in-swift-3cced3f28219)

如果你有任何批评、问题或建议，欢迎在下面的评论区发表！

感谢阅读。