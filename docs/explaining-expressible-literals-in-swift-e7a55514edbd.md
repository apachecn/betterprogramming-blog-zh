# 解释 Swift 中的可表达文字

> 原文：<https://betterprogramming.pub/explaining-expressible-literals-in-swift-e7a55514edbd>

## 通过用字符串、数组或其他类型初始化自定义类型来丰富它们

![](img/045a04529152ae450642065d52b7ce4d.png)

照片由 [Zuzanna Adamczyk](https://unsplash.com/@brobroda?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/neon-green?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

可表达文字允许您通过使用文字来初始化类型。Swift 标准库中有多种可用的协议，您可能已经在使用其中一种。

一个例子是,`ExpressibleByStringLiteral`允许我们使用双引号来初始化`String`,而不是使用`String(init:)`方法。我们都可以受益于这样一个事实，即 Swift 是通过采用我们自己定义的类型的协议来构建的。

# 什么是字面意思？

文字是一种表示固定值的符号，如整数、字符串和布尔值。Swift 中的文字通过几种可用的协议成为可能。标准类型符合这些协议，并允许我们如下初始化值:

```
var integer = 0 // ExpressibleByIntegerLiteral
var string = "Hello!" // ExpressibleByStringLiteral
var array = [0, 1, 2] // ExpressibleByArrayLiteral
var dictionary = ["Key": "Value"] // ExpressibleByDictionaryLiteral
var boolean = true // ExpressibleByBooleanLiteral
```

这些是最常用的，但还有一些其他的。它们值得探索，当你探索的时候，你可能会遇到一个叫做`ExpressibleByNilLiteral`的特殊协议。

`ExpressibleByNilLiteral`用于通过`nil`初始化选项:

```
var optional: String? = nil // ExpressibleByNilLiteral
```

此协议仅用于选项，不应用于自定义类型。基本上就忽略这一条。

# 向自定义类型添加文本支持

在大多数情况下，使用默认的协议集成就可以了。但是，您可能希望向现有类型或项目中定义的自定义类型添加自定义文本支持。

为了解释这是如何工作的，我们将以向 URL 添加字符串支持为例。这允许我们直接从一个字符串初始化一个 URL。

为此，我们需要对`URL`类型采用`ExpressibleByStringLiteral`协议:

```
extension URL: ExpressibleByStringLiteral {
    public init(stringLiteral value: StaticString) {
        self.init(string: "\(value)")!
    }
}
```

值得指出的是这里的限制，因为我们不能创建一个失败的或者抛出的初始化式。这需要我们强制打开创建的 URL，这可能不是你想要的。如果是这样的话，你可以直接使用`URL(string:)`初始化器。

如果出于您的目的，强制 unwrap 是有意义的，您可以按如下方式开始使用文本:

```
let url: URL = "https://www.avanderlee.com"
```

这可以简化 URL 的创建，尤其是如果您确定 URL 是有效的。注意，我们使用的是`StaticString`类型，所以不支持动态字符串。动态字符串会增加额外的复杂性，比如 URL 编码参数。你可以支持这一点，但这不是这篇博文的重点。

# 不同类型的字符串

当您添加对字符串文字的支持时，您应该考虑采用`ExpressibleByUnicodeScalarLiteral`来支持 Unicode 标量，采用`ExpressibleByExtendedGraphemeClusterLiteral`来支持扩展的字形簇。扩展的字素簇是一组一个或多个 Unicode 标量值，这些值成为单个可见字符。

如果您想同时支持这三种情况，您可以添加以下扩展:

# 在自定义类型中使用文本的有用示例

您可以在自定义类型中使用文本。下面举几个例子给你启发。

## 直接从字符串初始化日期

当您经常使用相同的日期格式时，您可以直接从`String`中初始化您的日期:

## 从字典初始化自定义缓存

如果您定义了自己的自定义缓存存储，您可能希望直接从字典中初始化它:

## **初始化自定义自动布局优先级**

当您在代码中编写[自动布局时，您可能需要设置一个自定义布局优先级:](https://www.avanderlee.com/swift/auto-layout-programmatically/)

注意:我们不能使用`ExpressibleByFloatLiteral`协议，因为输入值总是被视为一个整数。因此，我们必须使用`ExpressibleByIntegerLiteral`协议。

# 结论

就是这样！希望您已经受到了在 Swift 中使用文字所能做的事情的启发。也许您有自己的定制类型，可以很好地与可用的文字协议兼容。

如果您想进一步提高您的 Swift 知识，请查看 [Swift 类别页面](https://www.avanderlee.com/category/swift/)。如果您有任何其他建议或反馈，请随时[联系我](mailto:contact@avanderlee.com)或在 [Twitter](https://www.twitter.com/twannl) 上发推文给我。

谢谢！