# 用高迪轻松创作无属性字符串

> 原文：<https://betterprogramming.pub/compose-nsattributedstrings-easily-with-gaudi-da57a596c9c0>

## Gaudi 框架的 NSAttributedString 字符串声明性 DSL

![](img/1a4801ef2832b81b32effbfa26d15703.png)

图片来自 [Unsplash](https://unsplash.com/photos/0aFg0U2uPJY)

使用`NSAttributedString`的最大挑战之一是保持代码的整洁和可读性。让我们分析一个简单的案例来准确理解我的意思:

![](img/e375e81e070a105c52bb7a47deb1913d.png)

```
let hello = NSAttributedString(string: "Hello, ", 
                           attributes: [
                               .foregroundColor: UIColor.red
                           ])
let swift = NSAttributedString(string: "Swift", 
                           attributes: [
                               .foregroundColor: UIColor.orange,
                               .font: UIFont.systemFont(ofSize: 18)
                           ])
let final = NSMutableAttributedString(attributedString: hello)
final.append(swift)
```

这是实现我们在图像中看到的“Hello Swift”属性字符串所需的最少代码量。这有什么不好？

*   IDE 不帮助编写属性的`NSAttributedString.Key`键。不幸的是，由于某些原因，IDE 不提供自动完成功能。
*   值的零类型安全:属性值的类型是`Any`。
*   冗长而重复的代码:我们需要为字符串的每个部分创建一个具有不同属性的属性字符串，然后我们需要使用一个`NSMutableAttributedString`来组合这两个属性字符串。

我们已经看到了高迪在多个应用[主题和黑暗模式](https://medium.com/better-programming/gaud%C3%AD-for-ios-theming-and-dark-mode-86fe8fe60aeb)方面的强大，这一次我们将分析其使用`NSAttributedString`轻松格式化字符串的解决方案。

高迪很好地利用了 Swift 5.1 的特性[函数构建器](https://www.swiftbysundell.com/articles/the-swift-51-features-that-power-swiftuis-api/#function-builders)，并将其与声明颜色和字体样式的声明方式相结合。

这个框架，关于`NSAttributedString`，承诺提供一个简单和类型安全的方法来定义不同的颜色、字体和样式。使用 Gaudi DSL 的前一个示例如下所示:

```
let final = NSAttributedString {
    "Hello, ".foreground(color: .red)
    "Swift".foreground(color: .orange)
        .font(.systemFont(ofSize: 18))
}
```

好了...Gaudi 允许一种非常简洁的、类型安全的、非冗长的方式从简单的`String`类型开始组合`NSAttributedString`。显然，[语义颜色和字体样式](https://medium.com/better-programming/gaud%C3%AD-for-ios-theming-and-dark-mode-86fe8fe60aeb)在 DSL 中也是受支持的，可以这样使用:

```
let final = NSAttributedString {
    "Hello, ".foreground(semanticColor: .label(.primary))
    "Swift".foreground(semanticColor: .label(.secondary))
        .font(forStyle: .title(attribute: .regular))
}
```

DSL 还支持 if 和 if-else 语句:

```
let final = NSAttributedString {
    "Hello, ".foreground(semanticColor: .label(.primary))
    if swiftGreeting {
       "Swift".foreground(semanticColor: .label(.secondary))
           .font(forStyle: .title(attribute: .regular))
    } else {
       "World".foreground(semanticColor: .label(.tertiary))
           .font(forStyle: .subhead(attribute: .bold))
    }
}
```

现有的`NSAttriutedString`实例也可以使用这种方法进行组合和修改:

```
let hello = NSAttributedString(string: "Hello, ", 
                           attributes: [
                               .foregroundColor: UIColor.red
                           ])let final = NSAttributedString {
    hello.foreground(semanticColor: .label(.primary)) // replace color for "Hello, "
    "Swift".foreground(semanticColor: .label(.secondary))
        .font(forStyle: .title(attribute: .regular))
}
```

如果您所需要的只是稍后应用于`NSAttributedString`实例的`NSAttributedString`属性，那么 Gaudi 也提供了相同的声明性 API。例如，这个属性

```
let attributes = [
    NSAttributedString.Key.foregroundColor: UIColor.green,
    .backgroundColor: UIColor.red
]
```

高迪变成了这样:

```
let attributes = StringAttributes {
    foregroundAttribute(withColor: .green)
    backgroundAttribute(withColor: .red)
}.attributes // will be of type [NSAttributedString.Key : Any]
```

另外，`StringAttributes` DSL 支持 if 和 if-else 语句。

这些 API 显然也可以在没有 DSL 的情况下使用，例如:

```
let hello = "Hello, Swift"
    .foreground(semanticColor: .label(.primary))
    .font(forStyle: .title(attribute: .regular))// Hello is of type NSAttributedString
```

我提醒你，Gaudi 是完全开源的，所以如果你对实现这些结果的实现细节感兴趣，请随意访问[项目的 GitHub 库](https://github.com/gringoireDM/Gaudi)。特别是，我鼓励阅读大量的单元测试来发现这个框架的所有功能。可以用 swiftPM 和 CocoaPods 采用高迪。