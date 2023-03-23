# 理解 Swift 中的递归枚举

> 原文：<https://betterprogramming.pub/understanding-recursive-enumerations-in-swift-794e4bc88b68>

## 什么是递归枚举,“indirect”关键字有什么作用？

![](img/7d033f08e760f82ff9be567fa475cb10.png)

詹姆斯·哈里森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

递归枚举只不过是一个带有`case`的枚举，它回指枚举本身。

要创建一个，您需要使用`indirect`关键字。例如:

```
**enum** Test {
    **indirect** **case** value(Test)
}
```

你也可以在枚举声明的前面加上`indirect`关键字:

```
**indirect enum** Test {
    **case** value(Test)
}
```

`indirect`关键字用于告诉 Swift 以某种方式存储这种特殊的枚举，让它的大小增长。

# 例子

让我们创建一个递归枚举的例子。在本例中，您将使用形状。

点是最简单的形状。如果你连接两点，你会得到一条线，它也是一个形状。所以两个形状可以用来构造一个新的形状。因此，形状需要是递归结构。

有很多方法可以将它转换成代码，但是让我们使用递归枚举:

让我们仔细看看代码:

*   关键的一点是，`Shape`枚举同时代表了点和线。但是由于`Line`也是一个`Shape`，它需要递归引用回`Shape`。这是通过`indirect case Line(Shape, Shape)`实现的。
*   `info()`方法只是创建形状的字符串表示。

让我们通过创建三个*形状*来测试上述内容:两个点和由点连接的一条线。

输出是从点`"(3.0, 4.23)"`到`"(7.12, 9.32)"`的“线”,表示为字符串:

```
(3.0, 4.23) <---> (7.12, 9.32)
```

# 为什么间接关键字是必要的

如果创建一个没有`indirect`关键字的递归枚举:

```
**enum** Test {
    **case** value(Test)
}
```

您会得到一个编译器错误:`recursive enum 'Test' is not marked 'indirect'`。

在 Swift 中，枚举本质上是 [*值类型*](https://medium.com/codex/structs-vs-classes-in-swift-c37ef7e7d960) 。这意味着在编译期间必须注意它们的内存管理。简单地说，枚举所需的确切内存量必须事先知道。

记住这一点，如果你创建一个递归枚举(也就是在它里面引用枚举本身)，你就创建了一个递归，其中不可能说出枚举需要多少内存。

这个问题可以用`indirect`关键字解决。通过这样做，您告诉 Swift 改变它存储枚举的方式，以允许它增加大小。

# 结论

一个*递归枚举*是一种特殊类型的枚举，可以引用回自身。您可以像创建常规 enum 一样创建一个，但是出于 Swift 的内存管理原因，您需要使用`indirect`关键字。

例如:

```
**enum** Test {
    **indirect** **case** value(Test)
}
```

感谢阅读！我希望你学到了新东西。

# 你可能也会觉得这很有见地

[](https://medium.com/codex/swift-upgrade-your-skills-with-these-8-tips-da612111668) [## swift——用这 8 个技巧提升你的技能

### 1.带有三元运算符的较短的 If…Else 语句

medium.com](https://medium.com/codex/swift-upgrade-your-skills-with-these-8-tips-da612111668)