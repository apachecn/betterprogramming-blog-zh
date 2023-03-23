# Swift 5.1 的新功能

> 原文：<https://betterprogramming.pub/whats-new-in-swift-5-1-4c6aefb24006>

## 有了 Xcode 11，苹果让 Swift 疲于奔命。让我们看看新的关键特性

![](img/828c98ca3e64ff82866a68d4cd2a7e78.png)

照片由 [Przemyslaw Marczynski](https://unsplash.com/@pemmax?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

9 月 20 日，苹果发布了全新的 Xcode 11，主要支持新的 iOS 13。与往常一样，新的 iPhones 已经推出，苹果发布了其 IDE 的新版本，包含对其开发语言 Swift 5.1 新版本的支持。

即使不是主要版本，Swift 5.1 也集成了新的语言功能和改进的模块稳定性。

# 新的“自我”(是的，大写的“S”)

当使用面向对象技术的静态方法时，在 Swift 中引用类名是很常见的。在 Swift 的早期版本中，可以通过显式引用或在使用对象实例时使用`self.dynamicType`来引用类名。

在子类化过程中，`self.dynamicType`变得更有意思来引用在*的那段*代码中想要的类型。新的`Self`基本上是旧的`self.dynamicType`的替代品，旧的`self.dynamicType`仍然受支持并且运行良好。

这就是为什么现在你可以简单地使用`Self`关键字来引用类名:

```
class MediumAuthor {
 static func sayHello() {
    print(“Hello guys!”)
 } func newMethod() {
  **Self**.sayHello()
  *// equals to MediumAuthor.sayHello()*
 }
}
```

# 成员式初始值设定项中的合成默认值

这将非常感谢。当您在 Swift 5 中使用 structs 并且需要为未赋值的属性设置默认值时，您需要为其定义一个自定义的 init 方法，该方法根据参数值在属性上设置这些值。

在 Swift 5.1 中，这种实现是自动的，老派开发人员会说属性会自动合成它们的默认值。因此，不用 init 方法实现，您只需给它们分配一个自定义值，如下所示:

```
struct MediumAuthor {
  let name : String
  var articles = 0
}let theAuthor = Author(name: "Ivano Di *Gese")**// there's no need of implementing init() setting the articles default value*
```

# 不透明的返回类型

这个特性将允许开发人员更好地理解继承和子类化，用一个简化的机制来处理类型。

Swift 开发人员经常使用泛型来支持动态类型，这可能很乏味，因为它迫使您使用反射来理解托管对象的类型，并在编码时导致开销。

在 Swift 5.1 中，我们最好使用不透明的返回类型，它告诉编译器一个方法将返回一个泛型类的可能实现之一。

这比使用泛型好得多，因为 Swift compile“将知道”并理解该方法将返回哪种对象，这取决于它在该点的具体实现。

这就是为什么现在可以使用下面的语法:

```
func getAnimal() -> some Animal { return Dog() // or Cat(), or Elephant() or whatever}
```

你可能想知道为什么我们不能只使用`-> Animal`符号。这取决于您如何使用`Self`并在类中实现方法，因为在某些情况下它可能会产生编译错误。

例如，在比较对象时，如果您使用`some`关键字，Swift 将正确处理该类型，而不是抛出一个错误。

# 动态访问的静态和类下标

由于 Swift 开发人员广泛使用订阅，Apple 已经改进了其实现，简化了访问类成员、枚举属性等的语法。

在 Swift 5 中，您可以创建`get()`和`set()`方法来访问对象属性列表中的特定值，并且您可以使用静态属性来使方法与类相关，而不是与实例相关。

在 Swift 5.1 中，您可以更好地创建一个`subscript()`方法来下标一个对象并返回所需的值:

```
public class MyJson {
 private static var json = [String: String]()

 **public static subscript(keyName: String) -> String? {

   get {
     return json[keyName]
   }
   set {
     json[keyName] = newValue
   }
 }**
}MyJson[“author”] = “Ivano”
print(MyJson[“author”] ?? “Unknown author”)
```

# 单表达式函数的隐式返回

在 Swift 5 中，只要闭包的使用除了返回值之外没有其他事情可做，仍然允许省略`return`关键字。

这类似于 JavaScript 行为，当你使用箭头函数并省略 return 关键字时，通常指的是`map()`、`reduce()`和类似的实现。

这就是为什么在 Swift 5 中，只需使用赋值右侧的`map()`或`reduce()`函数就可以轻松地给`let`变量赋值，而无需专门使用`return`关键字。

在 Swift 5.1 中，这一点得到了改进，现在您可以这样做，甚至在自定义函数和方法中。

```
func multiply() -> Int {
 reduce(0) { $1.isMultiple(of: 2) ? $0 + $1 : $0 }
}func decrement(myNumber : Int) -> Int {
 myNumber - 1 *// no need of "return"!*
}
```

# 有序集合差分

最终有可能用简化的方法得到有序集合之间的区别。

开发人员经常使用集合和数组，因为它们像`UITableViews`和`UICollectionViews`对象的数据源一样频繁使用，这就是为什么这是一个真正受欢迎的新特性。

新的`difference(from:)`方法计算可以引用到两个目标集合的差异，甚至让您能够在其上循环并评估它们。

```
let myFirst = [10, 20, 30]
let mySecond = [10, 40, 50]let differences = mySecond.difference(from: myFirst)*// differences will contain references to evaluate removing and inserting actions to have arrays containing same values*
```

# 结论和其他改进

Swift 5.1 扩展了 Swift 5 的稳定性，提高了模块的编译时间。

模块稳定性还使用基于文本的模块接口文件，该文件描述了二进制框架的 API，允许使用不同版本的编译器用代码编译它。

[SwiftSyntax](https://github.com/apple/swift-syntax) 也已更新，在访问语法树和使用内部数据时会更快。

在这篇简短的文章中，我介绍了 Swift 更新，但没有介绍 Xcode 11 更新，Xcode 11 更新嵌入在同一个 macOS 更新中。

Xcode 11 有几个改进，包括 SwiftUI 支持和一些新的外观和感觉功能。更新 Xcode 和进入新的 Swift 语言更新肯定会提高你的专业 iOS 开发者技能。