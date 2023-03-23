# Swift 5.3 有什么新功能？

> 原文：<https://betterprogramming.pub/whats-new-in-swift-5-3-142d89d4d1f7>

## 跨平台支持、多结尾闭包、多模式 catch 子句等等

![](img/ead29813e8b2e63495f641ad35a75ae0.png)

[基拉·奥德海德](https://unsplash.com/@kadh?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

Swift 5.3 发布流程始于 3 月底，最近才进入开发的最后阶段。扩展跨平台的语言支持，如 Windows 和 Linux 发行版，是这个版本的主要目标之一。

但苹果也非常关注改善整体语言及其性能，以促进 iOS 的 SwiftUI 和机器学习。让我们深入了解一下我们将在即将发布的更新中看到的主要变化和功能。

# 多重尾随闭包

SE-0279 提案为尾随闭包带来了一种新的语法，让您可以以更易读的方式调用多个闭包作为函数的参数。这是更多的语法糖，最小化了函数签名中过多括号的使用。

相反，它允许您在最初的未标记闭包之后附加几个标记闭包。以下示例演示了这种情况的一个使用案例:

上面的更改将使我们的 SwiftUI 视图更容易编写。

# 多模式捕获条款

目前，do-catch 语句中的每个 catch 子句只能包含一个模式。为了解决这个问题，开发人员最好使用 switch cases 的功能在 catch 语句体中包含模式匹配，从而增加嵌套和重复的代码。

SE-0276 是在 catch 子句中引入模式匹配的另一个改进。Catch 子句现在允许用户指定一个逗号分隔的模式列表，能够将变量与 catch 主体绑定在一起——就像 switch 语句一样。这里有一个例子:

多模式 catch 子句将有助于使我们的代码库清晰简洁。

# 枚举的综合可比一致性

到目前为止，比较两个 enum 案例并不简单。你必须遵守`Comparable`并写一个`static fun <`来确定一个案例的原始价值是否低于另一个案例(或者反之亦然`>`)。

幸运的是， [SE-0266](https://github.com/apple/swift-evolution/blob/master/proposals/0266-synthesized-comparable-for-enumerations.md) 允许我们选择与枚举的`Comparable`一致，只要你的枚举有合适的类型，你就不需要显式地实现协议。如果没有设置关联值，`enums`将按照声明的语义顺序进行比较。

下面是一个对它们进行排序的枚举示例:

# 将病例枚举为方案见证

Swift 有一个非常严格的协议见证匹配模型，其中使用与协议相同的名称和参数编写 enum 案例不被视为匹配。我们被迫转而依靠手动实现，如下所示:

[SE-0280](https://github.com/apple/swift-evolution/blob/master/proposals/0280-enum-cases-as-protocol-witnesses.md) 取消了这一限制，因此如果 enum 病例提供了与方案要求相同的病例名称和参数，它们就可以成为方案见证。

# 自我并不是到处都需要的

SE-0269 提案允许我们在不再需要的地方省略`self`。早先，当我们从外部范围获取值时，在闭包中使用`self`是必要的。现在，当参考周期不太可能发生时，`self`将隐含地存在。

下面的例子说明了这种变化:

使用 SwiftUI 的开发人员会欣然接受这一点。由于视图保存在`structs`中，这是值类型，引用周期不能发生。

# 基于类型的程序入口点

[SE-0281](https://github.com/apple/swift-evolution/blob/master/proposals/0281-main-attribute.md) 赋予了我们一个新的`@main`属性，让我们定义应用的入口点。您不必再手动调用`AppDelegate.main()`方法，因为用属性标记结构或类可以确保它是您程序的起点。

我们可以假设旧的特定领域提供的属性`@UIApplicationMain`和`@NSApplicationMain`将在未来的版本中被弃用，而支持`@main`。

# 上下文泛型声明上的 where 子句

到目前为止，`where`子句不能放在嵌套在泛型上下文中的声明上。例如，如果您试图在一个函数上设置一个`where`约束，编译器会抛出一个错误。作为一种变通方法，我们必须创建单独的扩展来处理特定的`where`子句。

有了 [SE-0267](https://github.com/apple/swift-evolution/blob/master/proposals/0267-where-on-contextually-generic.md) ，只要我们引用通用参数，我们就可以用`where`子句实现函数。下面是一段代码片段，可以让我们对它有所了解:

通过允许在成员声明中使用`where`子句，我们可以轻松地创建更短、更简洁的通用接口，避免创建单独的扩展。

# 非连续元素的新集合操作

目前，访问集合中连续范围的元素非常简单。对于数组，你需要做的就是`[startIndex...endIndex]`。

[SE-0270](https://github.com/apple/swift-evolution/blob/master/proposals/0270-rangeset-and-collection-operations.md) 引入了一个新的优化的`RangeSet`类型，它允许我们获取一个可以不连续的索引子范围。

使用`RangeSet`，我们可以对集合进行大量的计算和操作。例如，通过使用`moveSubranges`函数，我们可以移动数组中的一系列索引。看看通过将所有偶数移到开头来调整数组是多么容易:

```
let rangeOfEvens = numbers.moveSubranges(indicesOfEvens, to: numbers.startIndex)
// numbers == [2, 4, 6, 8, 10, 12, 14, 1, 3, 5, 7, 9, 11, 13, 15]
```

# 提炼 didSet 语义

以前，`didSet`属性观察者的 getter 总是被调用来检索`oldValue`,不管引用是否首先被赋值。

对于变量来说，这可能不是一个大问题，但是对于大型数组来说，分配存储和加载一个不使用的值很容易影响应用程序的性能。

SE-0268 通过只在需要时加载`oldValue`来提高`didSet`属性观察器的效率。此外，如果我们有一个简单的`didSet`而没有`willSet`，那么修改现在将在适当的位置发生。

这里有一个精炼的例子`didSet`:

# 一种新型浮子 16

[SE-0277](https://github.com/apple/swift-evolution/blob/master/proposals/0277-float16.md) 介绍了`Float16` —一种半精度浮点。随着近年来机器学习在移动设备上的出现，这只能表明苹果公司进一步推进这一目标的雄心。A `Float16`通常在移动 GPU 上用于计算，并作为 ML 应用中权重的压缩格式。

```
let f16: Float16 = 7.29
```

# 结束语

以上总结了重要的 Swift 5.3 语言功能，同时 Swift Package Manager 也有许多改进。让我们快速浏览一下:

*   [SE-0271](https://github.com/apple/swift-evolution/blob/master/proposals/0271-package-manager-resources.md) 允许您添加资源(图像、数据文件等。)到您的 Swift 包裹。
*   [SE-0278](https://github.com/apple/swift-evolution/blob/master/proposals/0278-package-manager-localized-resources.md) 可以添加本地化资源。
*   SE-0272 允许你在你的包管理器中集成闭源依赖，比如二进制格式的 Firebase。
*   [SE-0273](https://github.com/apple/swift-evolution/blob/master/proposals/0273-swiftpm-conditional-target-dependencies.md) 允许您有条件地为不同的目标平台指定依赖关系管理器。

你可以从这篇博文中下载 Swift 的 Linux 发行版。或者你可以前往 [Swift 的网站](https://swift.org/download/#snapshots)先睹为快 Swift 5.3 的快照版本。

这一次到此为止。感谢阅读。