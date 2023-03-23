# Swift 中的测量:再次增强和简化

> 原文：<https://betterprogramming.pub/measurements-in-swift-enhanced-simplified-again-7095b4600f58>

## 让我们利用泛型的力量来避免重复代码

![](img/30204ff4f0048265ccc053dd292212eb.png)

由[弗勒](https://unsplash.com/@yer_a_wizard?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/measures?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

为什么我不能这么做？有了 Xcode 自动完成功能的帮助？

```
let v: Velocity = 55(.kilometers, per: .hours)
print(v * 2 * 2(.hours)) // -> 220 km
```

原来你可以！只需将这个[包](https://github.com/wildthink/anymeasure)添加到您的项目中，并开始编码。

如果你想知道一些背景故事和更多的细节，请继续读下去。

如果您还没有阅读 Swift: Simplified 中的 [Measurement，这只是 2 分钟的时间，作为早期作品，它为随后的改进和更新奠定了基础。](https://medium.com/@jasonj_2009/measurement-in-swift-simplified-30207fd8282c)

显然，我有一个性格缺陷，这使得我很难对不太好的解决方案感到满意。它不仅使我能够清晰简洁地写作，还能从一个特定的解决方案扩展到一个更一般的解决方案。

考虑到支持简单的`Measurements`(如前所述)很容易，我真的想将这种模式扩展到复合单位度量，或者至少扩展到更常见的`Ratio`。

每次我开始创建一个新的单元组合集合时，我都会对创建新组合的重复性感到不满。代码并不多，但足以扰乱调用站点，让我不得不寻找解决方案。

所以，经过更多的思考和与泛型的激烈争论，我解决了这个问题；事实证明，你可以做到这一点，只需添加很少的东西。

当然，我刚想出自己的解决方案，就发现`[Rate.swift](https://github.com/Flight-School/Rate/blob/master/Sources/Rate/Rate.swift)`在一个更干净的地方。所以我使用了它，只是按照我用于标准`Measurements`的模式做了一些小的扩展。

诀窍是使用标准度量`Unit`创建一个通用结构。添加`addition`、`subtraction`和`multiplication`的方法以及适当重载的操作符，您就可以开始比赛了。

```
public struct Ratio<Numerator, Denominator>
where Numerator: Unit, Denominator: Unit {
   /// The value of the numerator unit per single denominator unit.
   public var value: Double

   /// The numerator unit.
   public var numeratorUnit: Numerator

   /// The denominator unit.
   public var denominatorUnit: Denominator

   /// The ratio symbol.
   public var symbol: String {
       return "\(self.numeratorUnit.symbol)/\(self.denominatorUnit.symbol)"
   }
}
```

除了`typealias`和明智的使用`callAsFunction`来启用`2(.hours)`语法，我们可以使用所有开箱即用的`Foundation`度量单位。

```
typealias Velocity = Measurement<Ratio<UnitLength, UnitDuration>>
​
public extension BinaryFloatingPoint {
   func callAsFunction <N: Dimension, D: Dimension>(
       _ numerator: N, per denominator: D)
   -> Ratio<N,D> {
       Ratio<N, D>(value: Double(self), unit: numerator, per: denominator)
   }
}
...
```

真的没有比这更多的了。对于更多的数据类型来说，这几乎是一个清洗和重复的过程。参考 [GitHub 项目](https://github.com/wildthink/anymeasure)获得完整的源代码和可以包含在项目中的包。

我从许许多多的贡献者那里学到了很多。对他们，我说“谢谢”。对于你，读者，我期待着你的反馈，并希望你继续保持好奇和参与。

# 信用和资源

*   完整的包可以在 [AnyMeasure.git](https://github.com/wildthink/anymeasure) 获得。
*   感谢[飞行学院 Rate.swift](https://github.com/Flight-School/Rate)