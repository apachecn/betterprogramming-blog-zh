# Swift 的 10 大@特性

> 原文：<https://betterprogramming.pub/10-powerful-attributes-in-swift-d4e4153a0001>

## 了解如何正确有效地使用属性

![](img/7533aa64601890cf58b750e5276319b1.png)

[内森·杜姆劳](https://unsplash.com/@nate_dumlao?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

# @逃避

它通常用作作为函数参数传递的`escaping`闭包。当函数结束并被执行时，它存在于内存中。

当将可选闭包定义为属性时，默认情况下它已经在转义了。

逸出的封闭物可以储存起来以备后用:

也可以在函数结束后执行:

# @未知

`@unknown`是用`@frozen`和`@nonfrozen`枚举引入的。未冻结的枚举可能在将来获得新的枚举实例。例如，`UILayoutConstraintAxis`目前包含两个轴:`AxisHorizontal`和`AxisVertical`:

稍后可能会获得另一个案例`axisZ`:

为了安全地避免编译错误:

```
error: switch must be exhaustive
```

我们用`@unknown`属性实现开关:

# @属性包装器

当将属性包装应用于类、结构或枚举的属性时，它通过包装类型的实例包装对属性的访问。

包装器必须定义一个`wrappedValue`，它是公开属性的 getter 和 setter。下面的示例从传入的字符串值中修剪空白和换行符:

要使用它:

属性包装也可以接受参数作为默认值:

为了直接访问包装的属性，我们使用`_`作为前缀名:

# @可用

它表示该声明仅在特定的 Swift 语言版本或操作系统版本中可用。

例如，使用仅在 iOS 13+中可用的复合`UICollectionView`:

# @discardableResult

它表示可以忽略函数的结果，而不会出现编译器警告。

在我们将`@discardableResult`应用到函数之前，当调用函数而不使用其结果时，编译器会报错`Result of call to '' is unused`:

为了消除警告，我们使用`@discardableResult`:

# @dynamicCallable

`@dynamicCallable`比`@dynamicMemberLookup`更进一步。这是语法上的好处，而不是任何形式的编译器魔术。它适用于类、结构、枚举或协议，将该类型的实例视为可调用函数。

要使用它，我们需要添加`@dynamicCallable`属性并实现下面的一个或两个方法:

# @ dynamicMemberLookup

它是在 [Swift 演进提案 SE-0195](https://github.com/apple/swift-evolution/blob/master/proposals/0195-dynamic-member-lookup.md) 中介绍的。它指示 Swift 在访问属性时调用一个`subscript`方法。我们需要在使用时添加`subscript`方法:

对于下面的实例，我们用成员`firstname`和`lastname`实现了`User`结构。这些属性不是作为属性存在的，而是在运行时查找的。`dynamicMember`在字典中查找属性成员`firstname`和`lastname`并返回其值。

# @main

Swift 5.3 中的`@Argument`属性变得更加实用，尤其是在创建 Swift CLI 工具时。它应用于类、结构或枚举声明，以指示它包含顶级入口点。必须提供`main()`功能。

# @UIApplicationMain

这可能是最广为人知的属性，因为它位于`AppDelegate.swift`文件中，指示 iOS 应用程序的主入口点。

# @NSApplicationMain

当我们创建一个 OS X 项目时，Xcode 会生成一个`main.swift`文件。此属性应用于 app 委托类，以便为 app 生成隐式 main。它和 iOS 应用的`@UIApplicationMain`作用一样。

# 结论

Swift 在不同版本中不断增加@属性。正确高效地理解和使用它们，可以给我们带来干净的代码和更好的 app 性能。

感谢您的阅读。在评论中留下你可能有的任何问题。

上面提到的所有代码都可以在这个 [GitHub repo](https://gist.github.com/ericleiyang/14a335601f5046a02e73a0756318a4e9) 中找到。