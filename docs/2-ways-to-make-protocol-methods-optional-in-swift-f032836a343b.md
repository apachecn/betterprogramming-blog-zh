# 在 Swift 中使协议方法可选的 2 种方式

> 原文：<https://betterprogramming.pub/2-ways-to-make-protocol-methods-optional-in-swift-f032836a343b>

## 了解每种方法的优缺点

![](img/62528de864a93200bd6e8cbf8338ffbb.png)

图片由来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1031224) 的[自由照片](https://pixabay.com/photos/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1031224)提供

在本文中，我们将了解两种使协议方法可选的方法。我们将探讨每种方法的优缺点。

事不宜迟，我们开始吧。

# 默认实现

第一种方法是创建协议的扩展，并包含该协议的空方法:

这样，我们使`didDismiss()`方法成为可选的。让我们看看当我们遵守`PickerViewDelegate`协议时会发生什么:

编译器不要求我们实现`didDismiss()`方法。

## 优势

*   我们也可以使结构符合协议
*   当我们不在一致性类型中实现方法时，扩展中的默认实现被自动使用

## 不足之处

*   在可选方法返回非`Void`值的情况下，我们需要在默认实现中提出并返回一些值

# Objective-C '可选'关键字

我们可以用`@objc`关键字标记协议，用`@objc optional`标记可选方法:

这样，我们不需要创建一个扩展—我们所需要做的就是使一个类符合协议:

## 优势

*   不需要创建扩展

## 不足之处

*   只有`NSObject`子类可以继承`@objc`协议。这意味着我们*不能*使结构或枚举符合协议。
*   如果我们突然需要调用一个可选的方法，就必须在方法名后面加上`?`或者`!`符号(如果我们使用强制解包，方法没有实现，app 就会崩溃):

虽然第二种方法乍一看似乎很好，但还是推荐使用第一种，因为它有更大的灵活性。

# 包扎

对 Swift 更多鲜为人知的功能感兴趣？请随意查看我的其他相关文章:

*   [“Swift 中的~=运算符是什么？](https://medium.com/better-programming/what-is-the-operator-in-swift-7f6bc7623023)
*   [“Swift 的价值绑定模式是什么？](https://levelup.gitconnected.com/better-programming/what-is-the-value-binding-pattern-in-swift-a644be3e0597)
*   [“Swift 中的类和静态有什么区别？](https://levelup.gitconnected.com/better-programming/what-is-the-difference-between-class-and-static-in-swift-3493848ed831)
*   [“Swift 中的 vDSP 框架是什么？](https://levelup.gitconnected.com/better-programming/what-is-the-vdsp-framework-in-swift-fe2539693e9a)

感谢阅读！