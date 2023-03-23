# Swift 5.4 有什么新功能？

> 原文：<https://betterprogramming.pub/whats-new-in-swift-5-4-88949071d538>

## 多个可变参数、扩展的隐式成员语法、结果生成器等等

![](img/f114326dc3aaabfa33d00e6922707d23.png)

作者照片。

Swift 5.4 带给我们很多，这也是我喜欢它的原因。在本文中，我们了解了 Swift 5.4 的新功能。

***注:*** *您可以在 GitHub 上* [*下载本文的示例项目和来源*](https://github.com/Unobliging/What-s-New-in-Swift-5.4-) *。要打开和编辑这些文件，您必须使用 Xcode 12.5 beta。你可以在这里* *下载 Xcode 12.5 beta* [*。不用下载 Xcode 12.5 beta，直接下载 Swift 5.4*](https://developer.apple.com/download/)[*这里*](https://swift.org/download/) *。*

# 最重要的改进😄

任何以前创建过 Xcode 项目或 playground 文件的人都知道，当您创建一个新的 playground 或一个新的 Xcode 项目时，以下值将被写入该项目:

```
**var** str = "Hello, playground"
```

在 Swift 5.4 中，该值的名称已更改如下:

```
**var** greeting = "Hello, playground"
```

是的，我觉得这就是 Swift 5.4 有趣搞笑的地方。

现在我们可以看看真正有效的改进了！

# 多变量参数

在 Swift 5.4 中，我们可以在函数、方法、下标和初始化器上使用多个可变参数。在 Swift 5.4 之前，我们只有一个可变参数，如下所示:

```
**func** method(singleVariadicParameter: String) {}
```

现在，我们可以编写多个可变参数，如下面的代码:

```
**func** method(multipleVariadicParameter: String..., secondMultipleVariadicParameter: String...) {}
```

我们可以调用上面写的函数，当然，如果我们愿意，也可以只写一个`String`元素。代码如下:

```
method(multipleVariadicParameter: "Can", "Steve", "Bill", secondmultipleVariadicParameter: "Tim", "Craig")
```

多个可变参数的工作方式就像数组一样。当然，在调用参数中的值时，需要事先检查那个值是否存在；否则就会出错，死机。代码如下:

```
**func** chooseSecondPerson(persons: String...) -> String {
    **let** index = 1
    **if** persons.count > index {
        **return** persons[index]
    } **else** {
        **return** "There is no second person."
    }
}
```

# 结果生成器

自从 SwiftUI 问世以来，结果构建器在 Swift 中是如此重要。现在，随着新的改进，它变得更加重要。

我们可以用一个输出一个`String`的函数创建几十个字符串吗？如果我们使用结果生成器，答案是肯定的！

我们可以通过用`@resultBuilder`定义新的结构来定义新的结果构建器。您将定义的方法和属性必须是`static`。

回到我们将`String`元素转换成单个`String`元素的例子。使用下面的结果生成器，我们可以连接写在它们下面的`String`元素。代码如下:

```
**@resultBuilder**
**struct** StringBuilder {
    **static** **func** buildBlock(_ strings: String...) -> String {
        strings.joined(separator: "\n")
    }
}
```

让我们用下面的代码来描述它:

```
**let** stringBlock = StringBuilder.buildBlock(
    "It really inspires the",
    "creative individual",
    "to break free and start",
    "something different."
)print(stringBlock)
```

当定义一个值时，我们必须直接使用`buildBlock`方法。因此，我们必须在每个`String`元素的末尾加一个逗号。相反，我们可以在函数中使用`StringBuilder`来做同样的事情，而不用逗号。代码如下:

```
**@StringBuilder func** makeSentence() -> String {
    "It really inspires the"
    "creative individual"
    "to break free and start"
    "something different."
}print(makeSentence())
```

到目前为止，我们用结果构建器所做的对您来说可能没什么意义，但是如果我们更有效地使用结果构建器，您将会更好地理解它们的威力。例如，通过我们将添加到结果构建器中的两个新方法，我们可以使用条件来使用我们的结果构建器生成`String`元素。代码如下:

```
**@resultBuilder**
**struct** ConditionalStringBuilder {
    **static func** buildBlock(_ parts: String...) -> String {
        parts.joined(separator: "\n")
    } **static func** buildEither(first component: String) -> String {
        **return** component
    } **static func** buildEither(second component: String) -> String {
        **return** component
    }
}
```

正如你所看到的，通过创建一个`if`循环，我们可以根据布尔值改变`String`元素。结果如下:

```
**@ConditionalStringBuilder func** makeSentence() -> String {
    "It really inspires the"
    "creative individual"
    "to break free and start" **if** Bool.random() {
        "something different."
    } **else** {
        "thinking different."
    }
}print(makeSentence())
```

使用结果生成器可以做很多事情。你可以通过尝试找到它们。

# 扩展隐式成员语法

当在修饰符中定义元素时，我们不再需要指定元素的主类型。因此，您可以将多个成员属性或函数链接在一起，而无需在开头添加类型，如下所示:

```
.transition(.scale.move(…))
```

在 Swift 5.4 之后，为了同样的结果，我们必须在下面编写这个代码块。这是一行代码:

```
.transition(AnyTransistion.scale.move(…))
```

# 函数支持相同的名称

有时候，你想用相同的名字写函数。至少我想这么做。借助 Swift 5.4，我们可以做到这一点。

例如，如果我们用相同的名称创建函数——并且这些函数有相同的参数名称——如果我们用不同的对象类型定义这些参数，我们的代码将会工作。

你可以试着把这些写在下面:

```
**struct** iPhone {}
**struct** iPad {}
**struct** Mac {}**func** setUpAppleProducts() {
    **func** setUp(product: iPhone) {
        print("iPhone is bought")
    }

    **func** setUp(product: iPad) {
        print("iPad is bought")
    }

    **func** setUp(product: Mac) {
        print("Mac is bought")
    }

    setUp(product: iPhone())
    setUp(product: iPad())
    setUp(product: Mac())
}
```

## 结论

我希望这篇文章对你有所帮助。有新的报道称，Swift 6.0 可能会发布。我也会写一篇关于这个主题的文章。

感谢您的阅读。

```
**My Other Articles** - [Building Cross-Platform Apps With SwiftUI](/building-cross-platform-apps-with-swiftui-3fea88cdb0ae)
- [How to Create Onboarding Screens in Your iOS App](/how-to-create-onboarding-screens-in-your-ios-app-e41b518db31f)
- [How To Use Firebase in SwiftUI’s New Application Lifecycle](/how-to-use-firebase-in-swiftuis-new-application-lifecycle-c77a8a306d63)
```

如果你想见我或有关于 iOS 开发等问题。你可以在这里和我进行一对一的会谈[。](https://superpeer.com/canbalkya)