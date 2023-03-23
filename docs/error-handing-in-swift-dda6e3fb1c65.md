# Swift 中的错误处理

> 原文：<https://betterprogramming.pub/error-handing-in-swift-dda6e3fb1c65>

## 试试？或者试试！或者试试

![](img/e83c6863fd76c27eb9bad81103674d74.png)

照片由[罗姆森·普里查维特](https://unsplash.com/@woodies11?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄。

通常，导游试图通过网络来教授错误处理。然而，本指南将从基础开始探索错误处理。

难度:初级|简单| **普通** |挑战

# 先决条件

*   了解一些 Swift 中的解包知识会很有用(参见[本指南](https://medium.com/@stevenpcurtis.sc/avoiding-force-unwrapping-in-swift-6dae252e970e))。
*   可选的链接知识将是有用的(见本指南)。

# 术语

*   block:Swift 中描述闭包的一种方式(特别是有 Objective-C 背景的人使用的)。
*   错误:程序产生错误结果而导致的问题。
*   可选方案:Swift 引入了可选方案，只需声明是否有值，即可处理缺少值的情况。可选的本身就是一种类型！
*   可选链接:将多个可选链接到一个语句中。如果链中的任何一个环节为零，整个链都会失败。
*   URL:统一资源定位器。网页或资源的地址。

# 为什么扔？

您可能希望记录 Swift 程序中的错误。我在之前使用过的一个例子是文件处理。

现在，下面的例子将使用文件管理器。由于我们对 iOS 应用程序使用沙箱，我决定使用单视图 iOS 应用程序，而不是 Playgrounds(使用 Playgrounds 沙箱需要对代码进行一些更改)。

事情是这样的，创建一个文件或文件夹可能会因为很多原因而失败(资源被锁定，请求访问用户无权访问的地方，等等。).

正是因为这个原因，我们需要处理错误消息。

# 真实世界的使用

的确，在执行网络任务时，您可能必须处理错误，因为有时从网络位置下载会失败，我们需要知道在这种情况下该怎么办。

文件处理也是如此，因为操作失败或成功有各种不同的原因。

让我们开始描述我们的例子。

# 这个例子

Swift 编译器会产生以下错误:

```
**let** urls = FileManager.default.urls(for: .applicationSupportDirectory, in: .userDomainMask)**try** FileManager.default.createDirectory(at: urls[0],withIntermediateDirectories: **true**, attributes: **nil**)
```

代码中发生的事情是，我们正在检索您当前设备上的应用程序支持目录的`URL`。然后，我们使用它在该文件夹中创建一个目录。好吗？我们继续吧。

但是有一个错误:“从这里抛出的错误没有被处理。”

嗯*。这意味着我们实际上需要处理这种情况。我们要怎么做呢？*

嗯，我们可以基于[展开](https://medium.com/@stevenpcurtis.sc/avoiding-force-unwrapping-in-swift-6dae252e970e)的方法，因为我们可以强制展开可选的或者使用可选的链接来静默失败。这导致了另一种方法，帮助我们知道是否以及何时出现了问题。

# 最初的方法——尝试！

类似于强制展开，我们可以使用`try!`:

```
**try**! FileManager.default.createDirectory(at: urls[0],withIntermediateDirectories: **true**, attributes: **nil**)
```

如果运行时出现错误，应用程序就会崩溃。这是值得重复的:如果你使用`try!`并有一个错误，应用程序将简单地崩溃。

这样做的结果将是用户不高兴。换句话说，只有当你确定不会出错时，才使用`try!`。

# 可选的尝试——尝试？

与可选链接类似，我们可以使用`try?` 来忽略以下情况中的错误:

```
**try?** FileManager.default.createDirectory(at: urls[0],withIntermediateDirectories: **true**, attributes: **nil**)
```

或者如果我们有一个变量作为结果:

```
let responseJSON = try? JSONSerialization.jsonObject(with: data, options: [])
```

在这种情况下，`responseJSON`要么是类型`Any`要么是零——这是可选的`Any`或`Any?`。然后，我们可以像处理任何可选语句一样，通过使用程序员选择的可选链接或保护语句来处理这个问题。

# 做-试-抓

我们可以捕捉错误。请参见以下示例:

因此，我们尝试创建目录，如果有错误，就会触发第二个代码块。

在这种情况下，我打印了错误。这对谁都没多大好处。在另一种情况下，我们可能不关心错误是什么。

这里，我们可以使用第二个块(注意，我已经删除了捕获错误名称的代码部分)。这个想法是，你进入第二个区块，在那里你处理问题，并确保你的用户有一个伟大的整体应用体验。

# 结论

*   `try!`:小心使用。事实上，只有当你完全确定不会发生错误时，才使用它。换句话说，不要使用它，除非你能证明这是应用程序崩溃的风险。
*   `try?`:忽略错误即可。当你(作为程序员)不太关心这个错误时，使用这个。将值设为`nil`继续运行。
*   `do-try-catch`:运行代码并从代码的 catch 块中获取错误，以确保应用程序可以继续运行，不会出现问题。