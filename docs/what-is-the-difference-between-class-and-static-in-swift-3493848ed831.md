# Swift 中的类和静态有什么区别？

> 原文：<https://betterprogramming.pub/what-is-the-difference-between-class-and-static-in-swift-3493848ed831>

## 用清晰的例子

![](img/3916b6986d97e2aa038c13bacc179821.png)

[Aditya Joshi](https://unsplash.com/@adijoshi11?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

今天，我们将快速探索 Swift 中`class`和`static`关键字的区别。

在我们深入代码之前，当我们想要访问一个类的常量/变量而不实例化该类时，我们通常使用`class`关键字(就像我们使用`static`一样)，并且在子类中覆盖该属性(静态属性是不可覆盖的)。

下面的代码是在 Xcode 操场上编写和运行的。

# 我们开始吧

看看下面这个简单的`Car`类的实现:

我们可以看到，我们能够在不实例化`Car`对象的情况下访问`countryOfProduction`和`topSpeed`属性。

如果我们想创建从`Car`继承而来的`SuperFastCar`类呢？我们将编写以下内容:

然而，这会导致编译时错误——我们根本无法覆盖静态属性。我们能做什么？请改用`class`关键字(注意，我们不能使用`class`存储的属性):

现在，我们看到值按预期打印出来:

![](img/3735426a1be385c638f64cdcb9e02364.png)

# 包扎

对 Swift 的其他细微差别感兴趣吗？请随意查看我的其他相关作品:

*   [Swift 中的带标签语句是什么？](https://medium.com/better-programming/what-is-a-labeled-statement-in-swift-2d245ac7b4e7)
*   [Swift 中的等价协议是什么？](https://medium.com/better-programming/what-is-the-equatable-protocol-in-swift-f3238f6821d6)
*   [Swift 中的 OptionSet 是什么，您应该何时使用它？](https://medium.com/better-programming/what-is-optionset-in-swift-and-when-you-should-use-it-419777f3c39)
*   [Swift 中的 CustomStringConvertible 协议是什么？](https://medium.com/better-programming/what-is-the-customstringconvertible-protocol-in-swift-4b7ddbc5785b)
*   [Swift 中的 vDSP 框架是什么？](https://medium.com/better-programming/what-is-the-vdsp-framework-in-swift-fe2539693e9a)

感谢阅读！