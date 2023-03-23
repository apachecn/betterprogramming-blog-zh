# 存储正确包装的属性

> 原文：<https://betterprogramming.pub/default-stored-properties-1c27e5edc586>

## Swift 中的另一个用户默认属性包装

![](img/0836704b2fd77848ee13a569bee85680.png)

由 Arturo Esparza 在 [Unsplash](https://unsplash.com/s/photos/wrapper?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的基础照片

如果您从事 Swift 和 iOS 开发，您很可能会遇到 **UserDefaults** ，这是一种在键值存储上持久存储用户属性的简单方法。

> 更多关于 UserDefaults 的 Apple 文档:[https://developer . Apple . com/documentation/foundation/user defaults](https://developer.apple.com/documentation/foundation/userdefaults)

太好了！但是…为了使用它，您需要引用正确的 Defaults 实例，引用存储键，以便访问值…如果键还没有存储值，结果将是`nil`，而不是某个预定义的默认值。

那么，有没有一种方法可以在一个通用属性上使用 UserDefaults，它存储最后一个值，下次我试图访问它时，它会给我带来支持自定义默认值的存储值？？？

我的开发伙伴，这就是我们使用**属性包装器**的地方。

## 属性包装器？

是的。属性包装。

它们是一个很好的 Swift 功能，允许我们添加属性修饰符。基本上，我们想要做的是创建一个包装器，它将封装所有的`UserDefaults`逻辑，并给我们存储的值。

# 准备地形

由于`UserDefaults`是一个键值存储单元，我们将需要一些东西作为我们的存储键，所以让我们稍后制定一个协议来帮助我们:

这里，我们有一个很好的协议和一个扩展，使得使用带有`String`原始类型的枚举作为存储键变得更加容易。

为了使使用我们的存储键访问 UserDefault 更加容易，让我们使用默认值和我们的 StorageKey 协议创建一些助手方法。

# 构建包装器

我们希望在包装器上支持一些东西:

*   属性`Get`:获取存储值；
*   属性`Set`:更新存储值；
*   当键没有存储值时，支持默认值；
*   支持存储不同类型；

所以，让我们开始编写我们的包装器，它需要 2 个依赖项，一个代表存储值类型的`Value`，和我们的`StorageKey`。通过它们，我们可以实现`wrappedValue`属性来访问 UserDefaults。

在这里，每次我们访问包装的属性时，我们实际上是在 UserDefaults 上获取值，如果它不存在，我们将返回默认值。

当我们设置属性值时，它也会将它存储在用户默认值中。

## 使用它

不错！我们做了一个新的属性包装器…但是我们如何使用它呢？

我们只需要将它包装在我们想要的任何属性周围，现在，每次我们访问它时，我们将间接地访问用户默认值上的存储值。

您可以创建一个存储类来集中所有的`Stored`属性:

您也可以在需要的地方直接添加:

# 接下来呢？

属性包装器是封装行为和简化许多需要重复的地方的非常好的方法。

这是我实际使用过的代码的一部分，但我的想法更多的是展示如何在自己的项目中使用包装器来实现巧妙的解决方案。

今天到此为止！

感谢阅读，并继续包装您的财产！