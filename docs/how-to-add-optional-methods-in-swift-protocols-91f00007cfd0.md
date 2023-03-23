# 如何在 Swift 协议中添加可选方法？

> 原文：<https://betterprogramming.pub/how-to-add-optional-methods-in-swift-protocols-91f00007cfd0>

## 为什么我建议在协议中避免可选方法

![](img/8a4f7e330015342644f4413587b4d083.png)

由[让-菲利普·德尔伯格](https://unsplash.com/@jipy32?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

您可能遇到过想要在 Swift 协议中添加可选方法的情况。当使用协议将方法强制到类、结构和枚举中时，这是一个非常常见的情况。在这篇文章中，我们将看到如何实现可选的方法行为，以及为什么我反对它。

# 问题是

`Elephant`类必须符合`walk()`和`run()`，即使我们知道大象不会跑。让我们解决这个问题。

# 解决方案 1: ObjC 方法

一种方法是用`@objc`标记协议和功能，如下图。你会注意到我们已经从`Elephant`类中移除了`run()`方法，操场没有抱怨。

下面我们来理解一下我们做了什么:

1.  我们在`run()`方法前面添加了可选的`@objc`。
2.  我们在协议声明前面加了`@objc`。
3.  我们让`Elephant`类继承了`NSObject`类。
4.  我们从`Elephant`类中移除了`run()`方法。

我知道这是一种 Objective-C 方法，你必须让我们的类继承自`NSObject` ——没有人喜欢这样。但这是我们的选择之一。

# 解决方案 2:快速方法

让我们回到 Swift 和 dump `@objC`。下面是我们所做的:

1.  我们移除了`NSObject`和`@objC`痕迹。
2.  我们用一个空的`run()`方法体在`Animal`协议上添加了一个扩展。

就是这样。对于所有符合`Animal`协议的类、结构和枚举来说，`run()`方法现在是可选的。

# 为什么这样不好？

当您将任何协议方法设为可选时，您就做出了一个糟糕的设计决策，因为协议不应该这样使用。

> 一个*协议*定义了适合特定任务或功能的方法、属性和其他需求的蓝图— [Swift 的官方文档](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html)

我们应该做的是将`Animal`协议分解成两个不同的协议:一个是`WalkableAnimal`，另一个是`RunnableAnimal`。

通过这种方式，我们将协议分解成许多更小的协议，并使我们的代码更易于阅读。我相信这是使用协议并最终走向面向协议编程的更好方式。

或者，我们可以使用`Protocol`继承。像 [mmalc Crawford](https://medium.com/u/3c86fa45b598?source=post_page-----91f00007cfd0--------------------------------) 在这里建议的:

 [## 或者，你可以饲养动物，并使快速动物成为一个专业。

### 礼仪动物{

medium.com](https://medium.com/@mmalc/alternatively-you-could-keep-animal-and-make-a-fast-animal-a-specialisation-e86fe329668) 

# 结论

感谢您的阅读。我很乐意在评论区听到你的意见。