# 用 4 个有用的例子解释 Swift 中的字符串插值

> 原文：<https://betterprogramming.pub/string-interpolation-in-swift-explained-with-4-useful-examples-9be8a0b9a1fb>

## 你可能每天都在使用字符串插值。让我们定制它

![](img/1384968325707a3693318ce7fe66dd26.png)

照片由[贾瓦德](https://unsplash.com/@_javardh_001?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

Swift 5 用 [SE-228](https://github.com/apple/swift-evolution/blob/master/proposals/0228-fix-expressiblebystringinterpolation.md) 推出了一个体面的字符串插值版本，并更新了自 Swift 3 以来一直被弃用的旧版本`ExpressibleByStringInterpolation`协议。该协议的新版本允许我们对现有类型编写强大的扩展，这些扩展定义了对象在字符串插值中使用时如何交互。

在开发应用程序时，我们经常会打印出一些数据来帮助我们在测试或调试过程中理解我们的应用程序。[使用自定义调试描述来改进调试](https://www.avanderlee.com/swift/custom-debug-descriptions/)是改进我们在 Swift 中处理字符串的方式的一种方式，但是我们可以通过将此与字符串插值的优势相结合来达到另一个水平。

# 什么是字符串插值？

这个名字是不言自明的——它是关于将值插入字符串的。换句话说，将一个字符串添加到另一个字符串。

最简单的例子如下:

```
let name = "Antoine van der Lee"
print("Hello, I'm \(name)")
```

在本例中，我们将名称插入到 print 语句中。这产生了一个打印的句子:“你好，我是安托万·范德·李。”下面，我们利用了默认情况下处理插值的`DefaultStringInterpolation`结构，并将名称添加到了`"Hello, I'm"`字符串值中。

Swift 允许我们通过为特定类型添加我们自己的定制字符串插值逻辑来扩展这种默认行为，所以让我们来看看您可以在日常开发中使用的四个有用的案例。

# **案例 1:打印选项**

在 Swift 中打印期权可能会非常痛苦。有几个选项可供您选择:

编译器通常建议使用后者:

```
String interpolation produces a debug description for an optional value; did you mean to make this explicit?- Use String(describing:) to silence this warning
```

我一直在项目中使用这种方法，这导致了混乱的输出，在我的日志中没有非常有用的`Optional()`关键字。在许多情况下，我最终使用默认值方法，这并不总是导致干净的字符串插值。因此，我决定编写一个处理可选值的自定义字符串插值解决方案:

这现在允许我们像以前一样打印出选项，但是我们不再得到编译器的建议:

```
print("The title is \(title)")
// Prints: The title is SwiftLee - A blog about Swift
```

该值被很好地打印出来，没有关键字`Optional(..)`。如果没有设置该值，将会打印出`nil`，它清楚地表明该值没有设置，这将在调试过程中对我们有所帮助。

# **案例二:打印 JSON**

另一个常见的用例是从数据请求中打印出 JSON 响应。这在调试期间很有用，但是打印 JSON 的代码可能并不总是在手边。我发现自己搜索了几次关于栈溢出的代码，想知道如何从`Data`漂亮地打印 JSON。

为了解决这个问题，我创建了一个自定义的字符串插值方法，将`Data`作为输入:

这种插值方法引入了一种处理字符串插值的新方法，并要求我们在插值中设置一个属性名。这一开始对你来说可能不常见，但是一旦你理解了它带来的可能性，它就会变得有用。在这种情况下，我们的 JSON 打印得很好！

这也可以直接用在`URLSessionDataTask`的回复中:

# **案例 3:打印 URL 请求**

就像打印 JSON 响应一样，打印出任何传出的`URLRequest`也很有用。在开发应用程序时，我们经常利用网络请求，了解更多关于传出请求的信息会很有用。

默认情况下，一个`URLRequest`按如下方式打印出来:

尽管这为我们提供了 URL，但它也可以为我们提供关于 HTTP 方法的信息、包含的标头或您能想到的其他有用信息:

## 字符串插值与`CustomStringConvertible`

虽然这样做很好，但我还是用这个例子来指出这可以通过使用`CustomStringConvertible`协议来实现。

指出这一点的原因是，你必须明白，只有当你实际上在一个字符串内组合值时，字符串插值才起作用。

例如，即使我们已经配置了字符串插值扩展，如果按如下方式使用，前面的示例也会打印出没有额外属性的 URL:

```
print(request)
// Prints: [https://www.avanderlee.com/feed?offset=10&limit=20](https://www.avanderlee.com/feed?offset=10&limit=20)
```

这是因为该请求没有在另一个使用`\(..)`语法的字符串中使用。相反，它是使用`CustomStringConvertible`协议中定义的`description`值打印出来的。

如果你想了解更多，你可以阅读我关于使用定制调试描述来改进调试的文章。

# **案例 4:转换为 HTML**

当你使用 HTML 的时候，如果你能利用字符串插值，它可能更具可读性。一个常见的用例是在一段文本中包含一个链接。

默认情况下，您的`String`可能会被 HTML 标签弄得乱七八糟，而您希望保持它的可读性:

```
"""
   The blog post can be found at <a href="www.avanderlee.com">www.avanderlee.com</a>
"""
```

由于 HTML `<a></a>`标签，我们在我们的`String`中添加了一些额外的语法，使得它可读性更差。相反，我们可以添加一个定制的字符串插值扩展来处理添加到一个`String`的 HTML 链接:

通过使用定制的字符串插值方法，我们允许自己添加额外的验证逻辑，以确保使用有效的 URL。我们利用断言失败在调试早期给自己反馈说使用了无效的 URL。

出于同样的想法，您可以添加对 markdown 或其他导出方法的支持。我把这个留给你做练习！

# 结论

自定义字符串插值允许您进一步优化 Swift 中的字符串处理。打印 JSON 或 optionals 等常见用例可以让在 Swift 中进行开发成为更好的体验。然而，知道什么时候使用自定义字符串插值，什么时候使用`CustomStringConvertible`协议实现，这是很好的。

感谢阅读！