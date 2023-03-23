# 在 Swift 中使用 Typealias 的 5 种方式

> 原文：<https://betterprogramming.pub/5-ways-to-use-type-alias-in-swift-45ddce3cc941>

## 如何在 Swift 中使用类型别名

## 使用 typealias 增加代码的可读性

![](img/0f8bdcb1a562e9749e1bd7f99725f358.png)

Andy Makely 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

编写代码是可读性和简洁之间的战斗。冗长的代码增加了可读性，最终有助于更好的理解。啰嗦肯定有助于团队中的新成员。另一方面，更少的冗长意味着你有更精确的代码。为了保持这种平衡，我们可以利用 Swift 提供的一些语言功能，如 typealias。我们可以在各种不同的场景中使用类型别名。

# 组合协议的类型别名

在 Swift 中开发时，我们大量使用协议。协议是使我们的代码模块化的好方法。以 JSON 模型解析为例，我相信每个开发人员都遇到过这个问题。当我们在 Swift 中制作模型时，我们通常最终会制作符合 Codable 的模型。让我们来看看这个例子:

```
**struct** Employee:Codable {**var** name:String**var** age:String}
```

从上面可以看出，`Employee`模型符合 Codable。让我们试着检查一下 codable 的定义。你会发现它是`typealias`，它结合了`Encodable`和`Decodable`两种协议，看起来像这样:

```
**public** **typealias** Codable = Decodable & Encodable
```

这是 Swift 语言为自己的协议提供的类型别名。我们可以用同样的方式创建我们的自定义类型别名来组合我们自定义的协议。

```
**protocol** CafeteriaAccess {}**protocol** LabAccess {}**typealias** BuildingAccess = CafeteriaAccess & LabAccess**struct** Employee:BuildingAccess {**var** name:String**var** age:String}
```

# 类型别名语义基元类型

编写代码时，添加语义非常有用。它可以使代码更加清晰，更容易理解。当以平衡的方式完成时，Typealias 可以在处理基元类型时产生巨大的差异。我们在许多方法/函数签名中使用了像`String`、`Int`和`Double`这样的原始类型。基本类型根本不支持参数的使用，我们依赖参数名的方法签名来增加清晰度。如果我们的参数类型也能增加一些值就好了。让我们看一个例子:

```
**func** heatProduced() ->Double {**return** 0}
```

上面的方法`headProduced`给出了双精度形式的输出。但是假设我们在这个上下文中为`Double`创建了一个类型别名`Jules`——它看起来像这样:

```
**typealias** Jules = Double**func** heatProduced() ->Jules {**return** 0}
```

通过这一小小的改变，我们有了更多的上下文来增加清晰度。

# 类型别名泛型

类型别名也可以与泛型参数一起使用。让我们来看一个例子:

```
**typealias** EventList<T> = Array<T>**let** array:EventList = EventList(arrayLiteral: 1,2,3)
```

因此，我们声明了一个名为`EventList`的 typealias 数组，它基本上是一个泛型类型的数组。通过使用 typelias 作为事件列表，你已经使列表的本质非常清楚。我们可以进一步向别名类型的泛型参数添加约束。例如:

```
**typealias** EventList<T> = Array<T> **where** T:StringProtocol
```

当我们想要定义一个自定义类型的数组，甚至不需要对它们进行子类化时，这是非常有用的。

# 类型别名关联类型协议

协议中的关联类型也受益于类型别名。这方面的全部细节超出了本文的范围。让我们来看一个例子:

```
**protocol** Example {**associatedtype** load: StringProtocol}**struct** Implement: Example {**typealias** load = String}
```

# 类型别名闭包

假设我们有一个上传功能，可以产生三种可能的结果。让我们来看一个例子:

```
**func** upload(success: ((Int) -> Int)?,failure: ((Error) -> Void)?,progress: ((Double) -> Void)?) {}
```

如果你看到上面的例子，你会看到很多括号，看起来很难看。我们可以如下使用类型别名:

```
**typealias** Success = (Int) -> Int**typealias** Failure = (Error) -> Void**typealias** Progress = (Double) -> Void**func** upload(success: Success?, failure: Failure?, progress: Progress?) {}
```

现在上传功能看起来好多了！

# 结论

TypeAlias 看起来真的很有用，但是它也有一定的缺点。尤其是对于那些刚接触你的代码库的人来说，可能需要一点时间来理解。你必须在好的使用和过度使用之间划清界限。我一直在使用 typealias，但我尽量保持理智。

我很好奇*你们*在日常工作中是如何使用 typealias 的。请在评论中谈谈你的经历和想法。

感谢阅读！