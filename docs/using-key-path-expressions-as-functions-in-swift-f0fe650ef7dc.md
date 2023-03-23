# 如何在 Swift 中将关键路径表达式用作函数

> 原文：<https://betterprogramming.pub/using-key-path-expressions-as-functions-in-swift-f0fe650ef7dc>

## Swift 5.2 中一项令人惊叹的新功能

![](img/d40fdcbd4707c64f6ab30bc3c684613e.png)

照片由[merit Thomas](https://unsplash.com/@merittthomas?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/future?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

考虑这段代码:

这里，我们创建了一个名为`Car`的`struct`，它有五个属性:`model`、`brand`、`price`、`designerName`和`isExpensive`。

让我们创建这个结构的一些实例，并将它们存储在一个数组中，`cars`:

*注意:汽车的名称及其属性与现实世界中的汽车并不相似。*

如果我们想要检索`cars`数组中所有汽车的型号，直到 Swift 5.1，语法如下:

```
let carModels = cars.map({ car in
    return car.model
})
```

或者，如果那个开发者有点懒(像我！)或者更喜欢编写简短的代码，那么代码应该是:

```
let carModels = cars.map { $0.model }
```

但是等等，[雨燕 5.2](https://swift.org/blog/swift-5-2-released/) 有东西给你！由 [Stephen Celis](https://github.com/stephencelis) 和 [Greg Titus](https://github.com/gregomni) 撰写、由 [Ben Cohen](https://github.com/airspeedswift) 和[审核并在 Swift 5.2](https://github.com/apple/swift/pull/26054) 中实施的 [Swift Evolution 提案 SE-0249](https://github.com/apple/swift-evolution/blob/master/proposals/0249-key-path-literal-function-expressions.md) ，介绍了一种在一些特殊情况下使用关键路径的简便方法。演进建议将此描述为能够在允许使用`(Root) -> Value`功能的任何地方使用`\Root.value`。这意味着，如果您之前向一个方法发送了一个`Car`并取回了它的`model`，那么您现在可以使用`Car.model`来代替。

简而言之，从 Swift 5.2 开始，您可以使用 key path 来选择特定的迭代并访问其属性。因此，我们可以通过编写以下代码来获取`cars`数组中所有汽车的模型:

```
let carModels = cars.map(\.model)
```

就这么简单！关键路径使它变得简单快捷。

这种方法在另一个场景中也很有用。而在以前，您将接收一个值并传回它的一个属性。现在，您可以使用密钥路径。例如，要退回所有昂贵的汽车，我们只需这样做:

```
let expensiveCars = cars.filter(\.isExpensive)
```

要获取汽车设计师的所有姓名的数组，我们可以这样做:

```
let designers = cars.compactMap(\.designerName)
```

Swift 5.2 还引入了其他新功能，但这是我最喜欢的一个。请检查这个[要点](https://gist.github.com/sagunraj/257beefc06cf67dd1c930da98eeb5116)来查看所有代码。

您最喜欢的 Swift 5.2 功能与我的不同吗？请在评论区分享。

快乐的雨燕！

如果你觉得这篇文章有用，并且愿意支持我，请"[给我买杯咖啡](https://www.buymeacoffee.com/sagunraj)"

[](https://www.buymeacoffee.com/sagunraj) [## Sagun Raj Lage 正在撰写博客和书籍，并免费教授编程。

### 我是一名 iOS 开发人员，有从事各种领域相关项目的经验，如交通和…

www.buymeacoffee.com](https://www.buymeacoffee.com/sagunraj)