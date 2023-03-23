# Swift 中的 URL:深入解释的常见场景

> 原文：<https://betterprogramming.pub/urls-in-swift-common-scenarios-explained-in-depth-316d606df6b2>

## 关于 Swift 中的 URL，您需要了解的一切

![](img/5dfc31a5d0d6e74f2d6d2c18b37bc696.png)

照片由[马克·森德拉·马托雷尔](https://unsplash.com/@marcsm?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/speed?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

URL 在我们构建的应用程序中无处不在。我们使用包路径引用本地文件，从指向 API 的路径获取数据，并获取图像以显示视觉效果。当使用 URL 时，我们经常需要验证一些东西，比如确保它指向一个文件或者设置了某个查询属性。

其中一些操作可能很容易记住，不需要像这样的深入指导。然而，我发现自己经常搜索解释如何用一组给定的查询参数构建 URL 的一个堆栈溢出问题。作为一个博主，这是我决定为自己写一本手册与大家分享的时刻！

# 基本 URL 操作

这一切都从创建和使用链接的基本操作开始。例如，您可以将字符串值转换为 URL，如下所示:

```
let url = URL(string: "https://www.avanderlee.com")!
```

这个默认初始化器的缺点是我们可能不得不在整个代码库中复制强制解包。我们可以通过一个简单的扩展来防止这种情况:一个定制的初始化器:

```
extension URL {
    init(_ string: String) {
        self.init(string: "\(string)")!
    }
}

var unwrappedURL = URL("https://www.avanderlee.com")
```

这看起来要快得多，并且允许我们保持我们的实现代码干净，不被打开！

# **将 URL 转换成字符串**

还可以使用`absoluteString`属性将 URL 转换成`String`:

```
print(unwrappedURL.absoluteString) // Prints: [https://www.avanderlee.com](https://www.avanderlee.com)
```

# **制作相对于基础 URL 的 URL**

在 Swift 中构建 API 时经常使用的一个场景是通过使用基本 URL 来构建链接。例如，我们可以建立一个博客文章类别页面，如下所示:

```
let category = "swift"
let baseURL = URL(string: "https://www.avanderlee.com")!
let blogURL = URL(string: category, relativeTo: baseURL)!
print(blogURL) // Prints: swift -- https://www.avanderlee.com
print(blogURL.absoluteString) // Prints: [https://www.avanderlee.com/swift](https://www.avanderlee.com/swift)
```

同时，如果 URL 本身不是绝对的，您可以获得某个链接的基:

```
let absoluteURL = URL(string: "https://www.avanderlee.com")!
let relativeURL = URL(string: "swift", relativeTo: baseURL)!
print(absoluteURL.baseURL) // Prints: nil
print(relativeURL.baseURL!) // Prints: [https://www.avanderlee.com](https://www.avanderlee.com)
```

一个基本 URL 经常与获取一个链接的主机相混淆，该链接打印出没有其方案的域。使用`scheme`参数可以得到方案:

```
let swiftLeeURL = URL(string: "https://www.avanderlee.com")!
print(absoluteURL.host!) // Prints: www.avanderlee.com
print(absoluteURL.scheme!) // Prints: https
```

# **使用 URL 组件**

一个链接基本上是由几个部分组成的。我们之前已经在打印语句中看到了一些，但是还有更多的组件需要介绍:

```
let twitterAvatarURL = URL(string: "https://twitter.com/twannl/photo.png?width=200&height=200")!
print(twitterAvatarURL.path) // Prints: /twannl/photo.png
print(twitterAvatarURL.pathComponents) // Prints: ["/", "twannl", "photo.png"]
print(twitterAvatarURL.pathExtension) // Prints: png
print(twitterAvatarURL.lastPathComponent) // Prints: photo.png
```

这些属性在很多情况下非常有用，比如获取文件名或扩展名，但是它们没有提供查询项。这就是`URLComponents`发挥作用的地方。

## **获取查询参数的值**

上面的 Twitter 头像链接包含两个描述图像宽度和高度的查询参数。这是很重要的信息，所以我们要确保图像以正确的比例显示。因此，我们需要能够获得这些查询参数及其值。

我们可以通过使用一个用链接初始化的`URLComponents`实例来做到这一点，这使我们能够访问一个新的属性集合。您基本上可以将它视为一个 URL 解析器，它让我们能够访问例如查询项:

```
let components = URLComponents(string: "https://twitter.com/twannl/photo.png?width=200&height=200")!
print(components.query!) // width=200&height=200
print(components.queryItems!) // [width=200, height=200]
```

*注意 URLComponents 结构根据* [*RFC 3986*](https://www.ietf.org/rfc/rfc3986.txt) *解析并构造 URL。它的行为与 URL 结构的行为略有不同，后者符合较老的 RFC。但是，您可以根据 URLComponents 值的内容轻松获得 URL 值，反之亦然。*

这允许我们通过检查查询项数组来直接访问值:

然而，这仍然不是最漂亮的一段代码。我们用了很多未包装的东西，不太可读。我们可以使用一个自定义的下标，让代码看起来更漂亮:

*自定义下标的新功能？查看我的博文* [*用代码示例*](https://www.avanderlee.com/swift/custom-subscripts/) 解释 Swift 中的自定义下标。

是的，仍然有很多解包——我会让您通过使用保护来使解包看起来更好——但至少我们可以更容易地访问查询项。

# **构造带参数的 URL**

另一种方法是用字典中的查询参数构建一个 URL。这通常在构建 API 框架时使用。

例如，如果我们想要获取一个具有特定宽度和高度的虚拟形象，我们可以像这样构建它:

在这里编写一个扩展可能也很有价值，这样可以更容易地将字典转换成一组查询项:

# **使用文件 URL**

当构建应用程序时，我们经常引用本地或远程文件。您可以从远程位置下载某个图像，也可以从本地包中获取图像。

理解远程 URL 和本地 URL 之间的区别是有好处的。使用`isFileURL`参数时，只有后者被视为文件 URL:

```
var remoteFileURL = URL(string: "https://www.twitter.com/avatar.jpg")!
var fileURL = URL(string: "file:///users/antoine/avatar.jpg")!
print(remoteFileURL.isFileURL) // Prints: false
print(fileURL.isFileURL) // Prints: true
```

该属性基本上只在 URL 方案为`file:`时返回 true。

## **从文件路径获取文件扩展名**

当处理文件时，您通常希望知道文件扩展名以确定文件类型。您可以通过使用路径扩展参数来实现这一点:

```
print(fileURL.pathExtension) // Prints: jpg
```

## **如何在 Swift 中从文件路径中获取文件名**

要从文件 URL 获取文件名，我们可以使用`lastPathComponent`并通过删除文件扩展名来提取文件名:

```
print(fileURL.deletingPathExtension().lastPathComponent) // Prints: avatar
```

这不是很简单吗？如果您想在 UI 中显示文件名，这非常有用。

# 结论

Swift 中的 URL 有很多用途。我们使用 API 获取数据，图像来可视化我们的应用程序，我们经常使用我们的包中的本地文件。`Foundation`框架允许我们使用默认参数轻松访问许多 URL 组件。如果我们需要访问像查询项这样的 URL 组件，我们可以使用`URLComponents`类型。

*你认为我错过了使用 URL 的重要部分吗？请，* [*让我知道*](mailto:contact@avanderlee.com) *！*

如果您想继续提高您的 Swift 知识，请查看 [Swift 类别页面](https://www.avanderlee.com/category/swift/)。如果您有任何其他建议或反馈，请随时[联系我](mailto:contact@avanderlee.com)或在 [Twitter](https://www.twitter.com/twannl) 上发推文给我。

谢谢！