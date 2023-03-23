# Swift 5.3 中的可比枚举

> 原文：<https://betterprogramming.pub/comparable-enum-in-swift-374836f02251>

## Enum cases 将自动合成所需的实现，以符合 Swift 5.3 中的`Comparable`协议

![](img/49cdf5f694980bee0eecc7e095e1261a.png)

照片由[格雷格·拉科齐](https://unsplash.com/@grakozy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 1.枚举时对相关值进行分组

![](img/3a1e26db3ed08ca4df8904dd5614ffae.png)

Johannes Plenio 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

枚举，简称 Enum，是许多语言中流行的数据类型。它在代码中以类型安全的方式对相关值进行分组。

在 Swift 中，枚举采用了一级类型支持的许多特性，如`struct`和`class`。它有初始值设定项来提供初始大小写值。它可以被扩展以扩展功能。枚举可以有计算属性和实例方法来提供附加信息。它还可以实现`protocol`来对齐行为。

枚举是用一对带`enum`关键字的大括号引入的。

杨梦枚举颜色

# 2.用于比较枚举案例的样板文件

![](img/edf65208c207bf18a7863f09b9a8ca3c.png)

照片由[乔·格林](https://unsplash.com/@jg?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

但是，如何比较 Swift 中的 Enum 案例呢？

到目前为止，有几种方法，但每一种都有不同程度的不便。

## 声明可比原始值

然后，使用以下方法对它们进行比较:

这种方式的缺点是将一个无意义的`string`值关联并公开为原始值。它还介绍了`<`样板代码的静态实现。

## 声明私有原始值

为了避免暴露枚举用例的无意义的`string`值，人们实现了`private`比较值。不用说，它增加了更多的样板文件。

## 硬核比较

最常见的方法，也可能是最正确的方法，是通过检查案例本身来实现比较。

如您所见，函数`mininum`的顺序非常重要。代码相当冗长且容易出错。随着枚举案例的扩展，它不能很好地扩展。

# 3.Swift 5.3 中引入了类似的 Enum

![](img/1b7c76d42d70428d71005d47a9b08488.png)

由[杰西卡 F](https://unsplash.com/@jessica_favaro?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Swift 语言提案 [SE-0266](https://github.com/apple/swift-evolution/blob/master/proposals/0266-synthesized-comparable-for-enumerations.md) 引入了`enum`类型的综合可比一致性。

选择加入合成`Comparable`一致性的枚举类型将根据案例声明顺序进行比较，后面的案例比前面的案例进行比较。只有没有关联值的`enum`类型和只有`Comparable`关联值的`enum`类型符合合成一致性。后一种`enum`将首先按照 case 声明顺序进行比较，然后按照有效载荷值的字典顺序进行比较。没有带有原始值的类型符合条件。

通过使用 Xcode 12.0 (beta 2)，我们可以创建如下的`Comparable`枚举:

# 4.结论

![](img/ac22b8ccbd65a318c11174a549aba4b5.png)

[Olia Gozha](https://unsplash.com/@olia?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Swift 5.3 中的`Comparable` enum 是一个令人兴奋的特性。它综合了所需的实现以符合`Comparable`协议。我们现在可以避免添加容易出错的样板文件了！

# 5.资源

上述所有资源和要点代码可在以下位置找到:

SE-0266

[Swift 5.3 中的可比枚举](https://gist.github.com/ericleiyang/2ce076c08ebad4ff8b88db3ffe46d0b3)