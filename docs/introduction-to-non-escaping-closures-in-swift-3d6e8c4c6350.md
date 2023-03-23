# Swift 中的非转义闭包介绍

> 原文：<https://betterprogramming.pub/introduction-to-non-escaping-closures-in-swift-3d6e8c4c6350>

## 让我们来谈谈 Swift 中的非转义闭包是什么，以及它们在内存方面是如何工作的

![](img/b3cdef8bc7cdd72f4e0a6974f1305a1b.png)

照片由 [Ali Kazal](https://unsplash.com/@lureofadventure?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/escape?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在 Swift 中，闭包是一个自包含的代码块，可以在声明它的环境或上下文中捕获变量。闭包在 Swift 中被视为一等公民:它们可以作为参数传递，分配给属性，或者由其他闭包返回。

## 非逃逸与逃逸

在 Swift 中，当处理网络请求或其他异步操作时，我们通常会看到`@escaping`属性。转义闭包是指可以在使用它的范围内存活(转义)的闭包，也就是说，它甚至可以在函数体被执行之后被调用。

另一方面，一旦函数体被执行，非转义闭包就会超出作用域并停止存在于内存中。

让我们看一个代码示例:

**注意** : Online 17，由于`self` 正在闭包内部使用，我们需要使用一个`[weak self]`捕获列表来避免任何内存泄漏。

让我们特别注意第 4 行:我们将`onClose`参数标记为转义。由于它是一个转义闭包，我们可以将它赋给存储属性。如果您试图删除`@escaping`标记，您将面临以下编译错误:

```
Assigning non-escaping parameter 'onClose' to an @escaping closure
```

发生这种情况是因为非转义闭包不能被存储以供函数范围之外的进一步使用。

关于非转义闭包，Apple 将其用于大多数内置的高阶函数(接收一个或多个函数作为参数和/或返回一个函数作为结果的函数):

```
let mappedNumbers = numbers.map({ $0 * 2 })
let filteredNumbers = numbers.filter({ $0 > 100 })
let summedNumbers = numbers.reduce(0, { x, y in x + y})
```

他们三个接收一个闭包作为参数，这些参数没有被标记为转义。从 Swift 3 开始，闭包默认是不可转义的，如果不使用`@escaping`属性，它们将被视为不可转义的:

```
func map<T>(_ transform: (Element) throws -> T) rethrows -> [T]func filter(_ isIncluded: (Element) throws -> Bool) rethrows -> [Element]func reduce<Result>(_ initialResult: Result, _ nextPartialResult: (Result, Element) throws -> Result) rethrows -> Result
```

## 内存管理

与转义闭包相反，非转义闭包不会被存储以供后续执行(一旦函数体被执行，它们就不再存在)。这意味着非转义闭包**不能创建保持周期**。

您必须熟悉在闭包定义中使用`[weak self]`或`[unwoned self]`来捕获当前状态，以打破保持循环。在处理非转义闭包时，这两者都不是必需的:

```
**// Unnecessary [weak self] capture list**
let filteredNumbers = numbers.filter({ **[weak self]** number in 
    self.anyOtherMethod(number)
})
```

## 使用非转义闭包的过滤器实现

我们来看看苹果的滤镜方法声明:

```
func filter(_ isIncluded: (Element) throws -> Bool) rethrows -> [Element]
```

如您所见，filter 方法接收一个非转义闭包作为参数(如前所述，从 Swift 3 开始，默认情况下闭包是非转义的)。让我们试着想出这个方法的一个实现:

你可以这样使用它:

```
**let** items = [1, 0, 3, 4, 5]
let nonZeroItems = items.filterReplica { item in item != 0 }
```

让我们尝试创建一个额外的变量，以便更容易地看到传递给我们的 filter 方法的闭包:

```
**let** closure = { item **in
    return** item != 0
}
**let** items = [1, 0, 3, 4, 5]
let nonZeroItems = items.filterReplica(closure)
```

假设 filter 方法没有将接收到的闭包标记为转义闭包，那么一旦 items 迭代完成，它就会停止存在，所以根本不需要担心会产生不必要的保留循环。

就是这样！我希望这篇关于非转义闭包的简要介绍对您有用。