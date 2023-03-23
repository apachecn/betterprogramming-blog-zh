# 了解 Swift 5 中的写入时复制

> 原文：<https://betterprogramming.pub/understand-copy-on-write-in-swift-5-52a4716165a3>

## 结构、值类型和黑魔法

![](img/7c186569bec5a4fed2140131692ee625.png)

Giammarco Boscaro 在 [Unsplash](https://unsplash.com/s/photos/library?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在本文中，我们将了解什么是写时复制，以及为什么它绝对令人敬畏。我们还将了解它是如何工作的，以及如何用写时复制行为实现我们自己的定制对象。

# 什么是写入时复制？

写时复制是值类型背后的魔力。首先，考虑下面这个简单的例子:

```
var x = ["a", "b", "c"]
let y = x
x.append("d")
print(x) //*["a", "b", "c", "d"]*
print(y) //*["a", "b", "c"]*
```

您可能已经知道，像 Array 这样的集合具有值语义。

这意味着不同于引用类型，引用类型存储对象的引用，值类型存储对象的副本。在上面的例子中，`y`得到了`x`的一个副本。

每次你给一个变量赋值的时候，都会产生一个副本。这听起来非常昂贵，这样的事情怎么会发生在 Swift？

多亏了写入时复制，它实际上并不贵。

在幕后，这两个数组都包含对存储实际数组的内存位置的引用。此时，`x`和`y` 都指向同一个地方。

当我们使`x`变异时，它首先检查它是否是那个内存位置的唯一所有者。如果是，那就不用担心，`x`可以安全变异。

然而，如果有其他拥有者，`x`会被复制到一个新的内存缓冲区，并且只有在完成后才会变异。

```
//Memory address of x and y after appending to x:
print(MemoryAddress(of: x)) // **0x00007fb027b93740**
print(MemoryAddress(of: y)) // **0x00007fb0254100b0**
```

如您所见，有了写入时拷贝，我们可以两全其美。我们可以放心，同一内存位置的其他变量不会受到影响，并且通过只在必要时执行复制操作，我们在效率方面节省了很多。

# 写入时拷贝的实际应用

现在是时候动手创建我们自己的写时复制实现了。

写入时复制内置于 Swift 标准库的所有集合中，但如果您想要构建自己的具有变异功能并保留值语义的结构，则必须自己实现。这就是我们现在要做的！

一种幼稚的方法是每次我们改变我们的价值观时都复制它。它确实可以实现价值语义，但是成本太高了。

例如，如果我们在一个`for`循环中改变这个值，而这个值实际上没有与任何东西共享它的内存位置，我们将会得到一堆不必要的副本。

成功复制写入的关键魔法叫做`[isKnownUniquelyReferenced(_:)](https://developer.apple.com/documentation/swift/2430721-isknownuniquelyreferenced)`。

传入一个类的实例，函数返回一个布尔值，表明该类是否只有一个强引用。对于 [Objective-C](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/Introduction/Introduction.html) 类，它也返回 false，所以应该小心。

首先，我们创建一个`Person`类，它将存储一个有名字的人。

```
final class Person {
    var name: String init(name: String) {
        self.name = name
    }
}
```

我们的`Person`类有一个名为`name`的属性，它是我们创建并初始化的。

我们将把这个`Person`类包装成一个`struct`，并赋予它写时复制的行为。

目前为止一切正常。我们用一个包含 person 对象的新变量创建结构，并用一个名称值初始化它。

name 属性的 getter 将简单地返回名称，在这种情况下，我们不必对它做任何复杂的事情。

神奇发生在我们的二传手身上。我们先用我们的咒语`isKnownUniquelyReferenced` 来判断是否有其他对象指向同一个内存位置。

如果有，我们分配一个新的对象给 person。我们还打印了一条简单的消息，这对测试我们的代码很有帮助。

如果只有一个对我们对象的强引用，这意味着没有它的其他副本，所以我们可以安全地改变它。

就是这样。我们已经用几行代码实现了写时复制的工作实现。

让我们用一个简单的例子来测试我们的代码:

我们的`for`循环运行四次，每次都修改`person1`。然而，我们的 print 语句只在第一次变异时打印一次。

在此之后，内存位置发生了变化，所以`isKnownUniquelyReferenced` 返回`true`，所以我们可以简单地改变值而无需复制。

以下是该示例的完整代码:

# 结论

在本文中，我们研究了什么是写时复制，它为什么有用，以及它是如何实现的。

写入时复制通过消除不必要的副本，提高了值类型的使用效率。它的知识有望对您的代码有所帮助。如果您发现自己在构建自己的结构，您应该始终注意该结构的复制行为。

谢谢你看了我的文章，希望你玩得开心。如果你有任何建议、想法或问题，请在下面的评论中留下。