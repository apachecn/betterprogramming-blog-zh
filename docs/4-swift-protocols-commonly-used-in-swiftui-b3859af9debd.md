# SwiftUI 中常用的 4 种 Swift 协议

> 原文：<https://betterprogramming.pub/4-swift-protocols-commonly-used-in-swiftui-b3859af9debd>

## 等价、可比、可识别和可散列的解决方案

![](img/fdc4eb6d20a7f88e1400bdf5c1e161b0.png)

照片由[尼克·费因斯](https://unsplash.com/@jannerboy62?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

协议对于 iOS 或它的表亲 OS X 来说肯定不陌生；事实上，委托协议是一半以上框架的基础，尽管随着新的 async/await 的出现，它可能会在未来几年发生变化。也就是说，自 SwiftUI 在 2019 年问世以来，协议似乎一直在改变颜色。

我这样说是因为 SwiftUI 包含了几个与语言本身相关的强制性协议。基本协议，如等价、可比、可识别和可散列，尽管并不总是很清楚发生了什么。

# 可辨认的

这是作为一个新的 SwiftUI 编码器的第一个协议，当你试图定义一个`ForEach`循环时，例如，在一个`List`中——假设我们有一个包含自定义结构的骰子数组。

```
struct ContentView: View {
  @State var dice = [Dice]()
  var body: some View {
    ForEach(dice) {
      Text(String($0.value))
    }
  }
}
```

编译器需要的是一种唯一标识结构循环中每一行的方法。这里显示的骰子变量需要符合`Identifiable`。你用这样的代码获得的一致性。

```
struct Dice: Identifiable {
  //  var id = UUID()
  var id = Date().timeIntervalSince1970 // epoch [dies Jan 19, 2038]
  var value: Int!
}
```

# 易消化的

您可能遇到的第二个协议是`Hashable`，SwiftUI 将需要这个协议来完成一个循环，就像这里举例说明的那样。

```
ForEach(dice, id: \.self) { die in
  Text("Die: \(die.value)")
}
```

但是要小心，因为如果你随后添加了第三个协议`Equatable`并给出了定义——它会使你的代码崩溃。

```
**struct** Dice: **Equatable, Hashable** {
  **var** id = **UUID**()
  **var** value: **Int**! **static** **func** ==(lhs: Dice, rhs: Dice) -> **Bool** {
    lhs.value == rhs.value
  }
}
```

这里的 hashable 需求需要一个惟一的标识符，就像可识别的协议(当它需要可识别的时候，为什么它要求 Hashable，这超出了我的理解)。

为了同时使用`Hashable`和`Equatable`协议，您需要告诉`Hashable`协议关注 id，所以是的，那个唯一的`Identifiable`值。

```
extension Dice: Hashable {
  static func ==(lhs: Dice, rhs: Dice) -> Bool {
    lhs.id == rhs.id
  } func hash(into hasher: inout Hasher) {
    hasher.combine(id)
  }
}
```

也就是说，散列函数在这里也可以派上用场，因为它保证在给定相同输入的情况下产生相同的输出。虽然这个例子看起来有点无意义，但是您可以使用这样的代码一次又一次地生成相同的密钥。

```
.onAppear {
  var hash = Hasher()
  hash.combine(die.id)
  print("hash \(hash.finalize()) \(die.hashValue)")
}
```

Apple 的手册页[给出了如何使用该协议的更好的实际应用。](https://developer.apple.com/documentation/swift/hashable)

# 可比较的

在我的协议列表中，下一个是 comparable，它看起来几乎与 Equatable 相同。

```
extension Dice: Comparable {
  static func < (lhs: Dice, rhs: Dice) -> Bool {
    lhs.value < rhs.value
  }
}
```

将此代码添加到我们的 SwiftUI 接口，以利用新的协议/属性。

```
if dice.count == 2 {
  if dice.first! > dice.last! {
    Text("Winner 1st")
  } else {
    Text("Winner 2nd")
  }
}
```

但是这里有一个警告。因为我必须指向`id`来符合散列协议，所以我不能以同样的方式使用`==`。

```
if dice.first! == dice.last! {
  Text("Unequal \(dice.hashValue)")
} else {
  Text("Equal \(dice.hashValue)")
}
```

为了解决这个问题，我需要一个新的运营商。对上述问题的修复需要我创建一个新的中缀运算符，如`====.`，显然我需要更新上面显示的代码片段，用`====`代替所示的`==`。

```
infix operator ==== : DefaultPrecedence
extension Dice {
  static func ====(lhs: Dice, rhs: Dice) -> Bool {
    lhs.face == rhs.face
  }
}
```

我相信苹果公司希望你在日常代码中使用协议，让你想做的事情更加明显，这是一种有效的类型扩展，你可以在你的定制对象上使用。

# 等价的

好吧，我承认中缀运算符并不适合所有人——尤其是敏捷的清教徒。因此，这里有一个替代使用更等值，没有中缀。

在其中，我将视图定义为符合等价协议，将我的骰子的正面作为目标。

注意，我最初使用`.onAppear`来设置`die1`和`die2`，然后使用`.onChange`来管理每次我掷出新的一对骰子的所有后续重新加载。

至此，我结束了这篇论文。如果你想下载我用来一次性构建不同案例的整个项目，你可以在这里找到它。