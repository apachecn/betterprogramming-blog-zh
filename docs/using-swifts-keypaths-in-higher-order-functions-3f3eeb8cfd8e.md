# 在高阶函数中使用 Swifts 关键路径

> 原文：<https://betterprogramming.pub/using-swifts-keypaths-in-higher-order-functions-3f3eeb8cfd8e>

## 关键路径如何改进贴图、压缩贴图、平面贴图和过滤器

![](img/93fd35a19a3bbff88f31e8d2e9c69302.png)

亚历山大·博古斯瓦夫斯卡在 [Unsplash](https://unsplash.com/s/photos/path?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

当处理像数组或集合这样的集合时，我们可以使用所谓的高阶函数，比如`map`来转换集合中的每个元素，或者使用`filter`来获得集合元素的子集。

高阶函数是接受或返回其他函数的函数。如果你对他们不熟悉，网站 Use Your Loaf 会给你一个很好的概述。

在本文中，我们将探讨一种使用 Swift 关键路径的新方法。

首先，我们将探索关键路径，看看它们是什么以及如何使用它们。稍后，我们将查看函数`map`、`compactMap`、`flatMap`和`filter`的新实现，它们使用关键路径，允许我们将高阶函数的功能与关键路径的方便使用结合起来。

# 什么是关键路径？

简而言之，KeyPath 是一个函数，它通过使用特殊的符号来提供对类或结构的属性的访问。

让我们看一个简短的例子:

关键路径介绍。

`// 1` —这里，我们定义了一个名为`User`的小型结构，它只有一个属性`name`，并创建了一个名为 *Max* 的新用户。

`// 2` —访问属性`name`的值的正常方式是使用`.name`。

`// 3` —但是我们也可以使用 KeyPath，它由一个反斜杠和我们想要访问的类型和属性名组成，例如`\User.name`。

`// 4`—我们现在可以通过使用这个关键路径来访问`name`的值。由于编译器知道`user`的类型是`User`，我们可以使用更短的形式，省略结构或类的名称，例如，在本例中，用`\.name`代替`\User.name`。我们可以使用 KeyPath 来读取和写入一个值。

现在，我们已经了解了什么是关键路径以及如何使用它，让我们看看如何将它们与高阶函数结合起来！

# 在地图中使用关键路径

对于下面的例子，我们将使用一个`User`对象的数组，但是我们将使用一个与上面略有不同的结构:

用 KeyPaths 演示 map、compactMap、flatMap 和 filter 的结构。

`// 1` —这一次，用户有了名字、昵称、他们正在关注的其他用户的列表，他可以在线也可以离线。这些属性涵盖了我们新方法的所有情况:一个用于`compactMap`的可选字符串，一个用于`flatMap`的数组，以及一个用于`filter`的布尔值。

`// 2` —我们创建两个示例用户的数组， *Jeff* 和 *Tom* 。

既然我们已经有了要操作的东西，让我们看看如何实现`map`来使用关键路径:

实现映射以使用关键路径。

`// 1` —为了使我们的新`map`与标准`map`一样可用，我们将把它作为`Sequence`的扩展添加进来。这个协议在元素列表上定义了各种方法，比如`forEach`、`prefix`，当然还有`map`、`compactMap`、`flatMap`和`filter`。

其中，它由数组、字典和集合实现，因此可用于这些类型的集合。

`// 2` —由于我们使用了两个泛型类型参数，函数签名一开始可能看起来有点混乱，但是如果我们仔细观察，就很容易理解了。

`Element`表示序列中元素的类型，也是 KeyPath 的根类型。另一个泛型类型参数`T`表示`map`将这些元素转换成的类型。

例如，如果我们想通过提取用户的名字将一个用户数组转换成一个字符串数组，`Element`将会是`User`，而`T`将会是`String`。参数`keyPath`是访问`Element`类型的类或结构中`T`类型的属性的关键路径，在本例中为`\User.name`。

`// 3` —我们现在可以通过使用标准的`map`并将给定的关键路径应用到集合的每个元素来实现这个新版本的`map`。

`// 4` —为了使用新的`map`，我们现在可以向它传递一个关键路径。这里，我们将用户列表转换为包含他们姓名的字符串列表。与我们使用标准`map`的方式相比，我们的新版本看起来更干净，更好读。

# 在 compactMap 和 flatMap 中使用关键路径

协议`Sequence`定义了另外两种`map`:`compactMap`和`flatMap`。

两者都将一个元素序列转换为一个新的元素序列，但是第一个元素序列会丢弃转换产生的任何 nil 值，第二个元素序列会展平嵌套数组。

更多信息，你可以在 SwiftLee 上阅读[这个比较。](https://www.avanderlee.com/swift/compactmap-flatmap-differences-explained/)

接下来，我们还可以重新创建`compactMap`和`flatMap`来使用关键路径:

`// 1` —同样，我们扩展了协议`Sequence`，使它们可以用于数组、字典和集合。

`// 2`—`compactMap`的签名看起来与`map`的前一个签名几乎相同，唯一的区别是参数`keyPath`的泛型值类型。它不再是`T`而是`T?`，因此使用给定的 KeyPath 可以得到零值。

`// 3` —就像`map`一样，我们使用标准的`compactMap`并传递关键路径。从 KeyPath 接收的任何 nil 值将被`compactMap`删除，产生一个只包含非零值的新数组。

`// 4` —我们可以用类似的方式实现`flatMap`。同样，签名的不同之处在于给定的 KeyPath 的值类型，这次是`[T]`。这允许我们访问结构或类中的数组。

`// 5` —与我们实现`map`和`compactMap`的方式相同，我们可以使用标准的`flatMap`作为基础，并在闭包内应用关键路径。

`// 6` —当使用 KeyPaths 和标准函数比较这两个版本时，它看起来又好了很多。

# 使用`filter`中的关键路径

最后，让我们看看如何让`filter`与关键路径一起工作:

`// 1` —因为`filter`期望一个返回布尔值的函数，我们需要约束 KeyPath 只返回`true`或`false`。因此，我们不需要前面例子中需要的额外的泛型类型参数`T`。

`// 2` —如上所述，我们从标准`filter`开始，使用给定的关键路径来确定当前元素是否应该包含在新序列中。

`// 3` —这允许我们将一个布尔值的关键路径传递给`filter`，从而消除了通过`$0`访问当前元素的需要。

`// 4` —但是当将多个高阶函数链接在一起时，最大的区别是明显的。例如，通过使用我们的新版本，`filter`、`flatMap`和`map`的组合来获取当前在线用户所关注的所有用户的名称，这是简短、易于理解和易于书写的。

# 结论

关键路径是 Swift 中访问结构或类属性的一个很酷的特性。

通过编写我们自己的`map`、`compactMap`、`flatMap`和`filter`，我们可以将键路径的易用性与高阶函数的强大功能结合起来，以减少代码中的噪声，并使其看起来更好。

特别是当组合多个高阶函数时，例如`filter`，然后是`flatMap`，最后是最后一个例子中的`map`，关键路径可以大大提高代码的可读性。

如果你喜欢这种方法，序列上有更多的高阶函数可以使用关键路径重新创建，例如`first(where:)`、`drop(while:)`、`contains(where:)`或`sorted(by:)`。

# 资源

[](https://useyourloaf.com/blog/swift-guide-to-map-filter-reduce/) [## 地图过滤器简化快速指南

### 使用 map、filter 或 reduce 对 Swift 集合类型(如数组或字典)进行操作可能需要…

useyourloaf.com](https://useyourloaf.com/blog/swift-guide-to-map-filter-reduce/) [](https://www.avanderlee.com/swift/compactmap-flatmap-differences-explained/) [## CompactMap vs flatMap:解释差异- SwiftLee

### CompactMap 和 flatMap，有什么区别，分别在什么时候使用？Swift 4.1 引入了这一新方法，并提供了…

www.avanderlee.com](https://www.avanderlee.com/swift/compactmap-flatmap-differences-explained/)  [## 序列和收集方案

### 编写适用于任何集合的通用代码，或者构建您自己的集合类型。

developer.apple.com](https://developer.apple.com/documentation/swift/swift_standard_library/collections/sequence_and_collection_protocols)  [## 关键路径

### 从具体的根类型到任何结果值类型的部分类型擦除的键路径。一个类型擦除的键路径，来自…

developer.apple.com](https://developer.apple.com/documentation/swift/keypath)