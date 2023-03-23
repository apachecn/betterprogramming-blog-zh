# 在 Swift 中使用 Decodable 和 Enum 解析不确定类型

> 原文：<https://betterprogramming.pub/parsing-indeterminate-types-with-decodable-and-either-enum-using-swift-93e6e1bc0c3a>

## 在 Swift 中有效处理各种 JSON 响应

![](img/385f19f77a927d7d0d94d5b1651b22f3.png)

照片由[费伦茨·阿尔马西](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

正如你们中的一些人可能已经知道的，`Decodable`是一种用于从外部表示(如 JSON 和属性列表)中解码类型的协议，在 Swift 4 中发布。

[根据苹果](https://developer.apple.com/documentation/swift/decodable)的说法，一个`Decodable`是:

> 一种可以从外部表示中自我解码的类型。

假设我们得到一个 JSON，如下所示:

```
[
    {
        "type": "car",
        "identifier": "12345",
        "model": "Audi"
    }
]
```

我们可以定义一个符合`Decodable` 的`Car`类型，并使用`JSONDecoder`实例对其进行解码。您可以在 Swift playground 中尝试以下代码:

现在，假设后端团队决定在同一个响应中也包含`Motorcycles`:

```
[
    {
        "type": "car",
        "identifier": "12345",
        "model": "Audi"
    },
    {
        "type": "Motorcyle",
        "brand": "Yamaha"
    }
]
```

在理想情况下，我们应该有两个不同的键:一个用于`Cars`，一个用于`Motorcycle`。但是，尽管这并不常见(或者至少不应该如此)，我们有时还是会收到设计糟糕的 JSON 响应。

可以想象，我们的`Car`类型不再足以解码整个 JSON 响应。这是一个利用我们强大的 swift 枚举的好场景。

在这种情况下，我们将利用`Either` enum，它是一个只有两种情况的枚举，一次只能使用其中一种。关于这个自定义实现有很多好的参考资料，所以我就不深入介绍了。

`Either`枚举的一个常见实现如下所示:

为了让`Either`和`Decodable`*一起工作，我们需要扩展它的功能。如果我们使用类似于`Either<Car, Motorcycle>`的东西直接尝试解码 JSON 响应，编译器将会抛出一个错误，因为`Either`需要符合`Decodable`协议。*

*由于枚举不能包含任何存储的属性，因此不能为`Either`合成编码键。因此，它还需要实现`[init(from: Decoder)](https://developer.apple.com/documentation/swift/decodable/2894081-init)`初始化器:*

*现在，我们应该准备好使用我们的`Either`枚举来解析 JSON 响应:*

*应该就是这样了！这样，您应该能够从 JSON 响应中检索到`Cars`或`Motorcycles`。*

*在这一点上，你可能会问:如果我们得到一辆额外的车会怎么样？如果我们得到两个额外的呢？或者更多？*

*正如您可能已经猜到的那样，`Either`在这种情况下不起作用。更好的方法是使用类型擦除来定义一个`AnyDecodable`类型。*

*我将在以后的文章中讨论这个主题。您可以在这个 GitHub 资源库中找到完整的示例代码:*

*[](https://github.com/DeluxeAlonso/Decodable-Either) [## GitHub-DeluxeAlonso/Decodable-要么:用 Decodable 和要么枚举解析不确定类型…

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/DeluxeAlonso/Decodable-Either)*