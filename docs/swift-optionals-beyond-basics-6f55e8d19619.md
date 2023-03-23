# Swift 选项:超越基础

> 原文：<https://betterprogramming.pub/swift-optionals-beyond-basics-6f55e8d19619>

## 更清晰代码的映射和平面映射选项

![](img/63bc162f8396bd8589c33a84dec14aac.png)

马克斯·尼尔森在 [Unsplash](https://unsplash.com/search/photos/swift?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在这个阶段，你可能知道斯威夫特(Swift)`Optional`只是服用了类固醇的`enum`:

```
enum Optional<Wrapped> {
  case none
  case some(Wrapped)
}
```

事实上，您可以使用*长格式*来使用它们:

```
let longFormSomeOptional = Optional<Int>.some(1)
let longFormNoneOptional = Optional<Int>.none
```

您可能还知道如何使用`Optional`绑定(`if let`、`guard let`和`switch`)、`Optional`链接、零合并操作符以及强制展开。

但是，你可能不知道下面解释的其他一些可以让你的生活更轻松的操作。

# 映射可选的

`Optional` `enum`中有一个映射操作，允许我们映射它的展开值:

`func map<U>(_ transform: (Wrapped) throws -> U) rethrows -> U?`

当`Optional`不是`nil`时，该函数计算闭包，将展开的值作为参数传递。它返回闭包的结果，或者当`Optional`为`nil`时只返回`nil`。

让我们看一个例子:

```
let someNumber: Int? = 2
let noneNumber: Int? = nil// closure will be evaluated and result1 is Optional(4)
let result1 = someNumber.map { $0 * $0 }// closure won't be evaluated and result2 is Optional(nil)
let result2 = noneNumber.map { $0 * $0 }
```

如果我们将`map`与 nil-coalescing 操作符一起使用，我们有一个很好的替代丑陋的`var-if-let-else`:

```
let someNumber: Int? = 2
let noneNumber: Int? = nil// closure will be evaluated and result1 is 4
let result1 = someNumber.map { $0 * $0 } ?? 0// closure won't be evaluated and result2 is 0
let result2 = noneNumber.map { $0 * $0 } ?? 0
```

# 与地图的可选绑定

`map`函数对于`Optional` s 有一个特殊的用法，我认为知道这个很好。

如果我们再次检查`map`签名:

`func map<U>(_ transform: (Wrapped) throws -> U) rethrows -> U?`

我们可以看到，当`U`为`Void`时，地图将返回 nothing(形式上为`Void?` 或`()?`)。因此，我们可以使用这里的`map`作为`Optional`绑定。

让我们看一个例子:

```
func log(_ message: String) {
  print(message)
}let someMessage: String? = "Hello World"
let noneMessage: String? = nil// closure will be evaluated
someMessage.map { log($0) }// closure won't be evaluated
noneMessage.map { log($0) }
```

我们没有正式地*映射*上面的选项，而是使用它作为`if-let`的替代，以防它更适合您的代码。

# 平面映射可选

假设前面的*转换*闭包返回一个可选的。然后，`map`将返回一个 double 可选值。

```
let someString: String? = "2"
let result1 = someString.map { Int($0) } // result1 is Int??
```

在这种情况下，你可能想用`flatMap`这个可选的来代替，这样闭包的结果就变平了。

`func flatMap<U>(_ transform: (Wrapped) throws -> U?) rethrows -> U?`

```
let someString: String? = "2"
let result1 = someString.flatMap { Int($0) } // result1 is Int?
```

与我提到的`map`操作一样，我们可以使用带有零合并操作符的`flatMap`作为丑陋的`var-if-let-else`的替代。

```
let someString: String? = "2"
let noneString: String? = nil// closure will be evaluated and result1 is 2
let result1 = someString.flatMap { Int($0) } ?? 0// closure won't be evaluated and result2 is 0
let result2 = noneString.flatMap { Int($0) } ?? 0
```

感谢阅读！