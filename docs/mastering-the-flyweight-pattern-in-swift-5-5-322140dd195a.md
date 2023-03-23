# 在 Swift 5.5 中掌握 Flyweight 模式

> 原文：<https://betterprogramming.pub/mastering-the-flyweight-pattern-in-swift-5-5-322140dd195a>

## 结构设计模式实现

![](img/3d9f01b7204b12b7a0cdb114c9208601.png)

资料来源:Undraw.co

Flyweight 模式节省了内存，降低了创建依赖于相同不可变属性的相似对象的成本。

# 什么时候我应该使用轻量级？

当您在多个类中重复相同的不可变属性集时，Flyweights 是一个很好的解决方案，尤其是当对象的创建成本很高时。

# 什么时候我应该避免使用轻量级？

当你需要一个以上的内在状态对象时，不要使用 flyweights。相反，使用单例或工厂模式与共享状态实例进行交互。

> “在创建 flyweight 时，要注意你的 flyweight 内存增长了多少。如果你存储几个 flyweight，你可以最小化同一个对象的**的内存使用，但是你仍然可以在 flyweight 存储中使用太多的内存。**
> 
> 为了减轻这种情况，设置使用多少内存的界限，或者注册内存警告，并通过从内存中移除一些 flyweights 来响应。您可以使用 LRU(最近最少使用的)缓存来处理这个问题。"
> 
> [雷·温德里奇](https://www.raywenderlich.com/books/design-patterns-by-tutorials/v3.0/chapters/18-flyweight-pattern)

# 基本设计

在高层次上，设计既有内在状态，也有外在状态。使用内部状态的引用类型。

您不能使用 *Struct* 或 *Enum* ，因为它们会自动被引用复制，导致 Flyweight 试图解决的问题。在这种情况下，外在状态将包含可变属性。

# 例子

`UIColor`使用 Flyweight 模式，因为颜色不会发生变化。

```
Console Output:Same instance!
```

使用`===`我们可以比较两边在内存中是完全相同的实例，而不仅仅是彼此的副本。所以，我们已经证明了`redColorOne`和`redColorTwo`指向内存中的同一个地址。

# 如何重构现有代码

让我们把轻量级模式付诸实践。在 Xcode 中创建一个新的游乐场，并定义一个名为`Tesla`的类。我们将从这个类开始，然后重构使用 Flyweight 模式。

第一步是将对象的不可变部分从可变属性集中分离出来。

永远不会改变的部分可以存储在 Flyweight 中，客户机可以管理可变属性，在必要时将它们传递给 Flyweight。

我们还需要一个地方来存储初始化的 flyweight 对象。`HashMap`使用键/值配对(KVP ),在访问内存中的底层值时比数组有效得多，所以让我们在这个例子中使用 HashMap。

作为奖励，我们可以使用`identifier`作为键，这样更容易维护。添加名为`gigaFactory`的新私有收藏。

在 UIColor 示例中，`UIColor.red`是一个静态函数。在分配属性时，最佳实践是依靠类型推断，所以添加类型的三个静态属性:`Tesla`。(我们还没有写`build(_:)`方法。但不用担心，这是下一步)

最后一步是管理对 flyweights 的访问。让我们制定那个`build(_:)`方法。它将检查我们的`gigafactory`,查看请求的车辆是否已经存在。

如果是，则返回它，否则，实例化一个新的，并在更新集合以包含新车辆后返回它。

工厂方法返回先前存储的 Tesla 实例或实例化/返回新实例。

构建并运行应用程序。每个`Tesla`只加载一次，不会注册多次。

您已经成功缩短了应用的处理和加载时间！

```
Console Output:
true   // 1
false  // 2
false  // 3
true   // 4
```

希望你对轻量级结构模式的理解更加自信。

# 纪念

*   Flyweight 模式最小化了内存使用和处理。
*   这个模式有称为 flyweights 的对象，以及返回它们的静态方法。这是单例模式的变体。
*   当创建 flyweight 时，要注意你的 flyweight 内存的大小。如果你存储了几个 flyweight，仍然有可能在 flyweight 存储中使用太多的内存。
*   flyweights 的例子包括缓存图像等对象，或者在内存中保存一个对象池以便快速访问。
*   轻量级在 UIKit 中很常见。`UIColor`、`UIFont`和`UITableViewCell`都是带有 flyweights 的类的例子。