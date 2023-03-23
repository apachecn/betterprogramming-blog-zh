# Swift Hacks:通过内嵌配置消除冗长的类型声明

> 原文：<https://betterprogramming.pub/swift-hacks-de-duplicate-tedious-type-declarations-with-in-line-configuration-13f66370754>

## 向变量类型添加已配置的方法以提高编码效率

![](img/4aa26f1b9374d0831ab8e99138035ac6.png)

斯图尔特·弗里斯比在 [Unsplash](https://unsplash.com/search/photos/barbican-estate?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 现行标准

在我们的 Swift 代码库中，我们广泛使用了“[闭包初始化](https://medium.com/the-traveled-ios-developers-guide/swift-initialization-with-closures-5ea177f65a5)”标准。例如，不要这样写:

我们会这样写:

这是一个很好的标准，有很多优点。例如，它在代码中提供了一个清晰的单一位置，在这里完成了特定 UI 元素的大部分(如果不是全部)配置。

然而，您可能已经注意到，我们被迫写出变量的类型两次——一次声明变量的类型，一次初始化它的值。

在某些情况下，这可能是需要的。面向外部的类型从内部类型中分离出来，结合多态性(通过子类化或通过协议)，允许我们使用最有用的通用类型来声明变量，并对任何客户端隐藏实际类型的细节。例如，我可能声明了一个`UILabel`属性，但是用我自己的`FancyLabel`子类的一个实例来初始化这个值。或者，我可能将属性声明为`UITableViewDataSource`，并交替使用各种符合类型作为实际值，而不必担心这会如何影响属性的客户端。

然而，在实践中，我们并不经常获得这种优势；我们通常声明和初始化完全相同的类型。如果你在这个标准中创建了足够多的变量，它就会变得有点乏味。此外，当使用一种已经很乏味的类型时，这种乏味会加倍。例如，尝试声明以下几个变量:

一个更手指友好的标准怎么样？

# 看这个问题

我们希望将使用闭包初始化标准的*而不是*的优势——即从初始化中推断变量类型的能力——与标准的优势相结合——例如将对象配置作为其声明的一部分。

由于 Swift 的半功能范式，我们已经有了这种可能的模式。这个范例允许我们替换如下的多语句序列:

有了这个:

所以从这种类型的语法中得到启示，我建议我们添加一个`configured`方法到我们想要在声明时配置的变量类型中。这基本上就像能够对单个变量进行`map`运算。所以让我们开始吧！

# 添加配置方法

我们的配置方法非常简单，所有类型的实现都是一样的。向任意类型添加方法的一个简单方法是声明一个协议，然后用要添加的方法扩展该协议:

任何类型的人现在只要符合`InlineConfigurable`就可以免费获得这个方法。让我们延长`NSObject`:

就这样，各种 iOS SDKs 提供的大部分类型都是可以神奇配置的。如果我们想要我们自己定义的类型的这个功能，我们可以从`NSObject`继承它们或者简单地使它们符合`InlineConfigurable`。

最后，这是我们如何使用它:

Blammo！

## 2019 年 8 月 9 日更新

我做了一个椰子版的。它叫做 PHBApplying，你可以这样使用它:

我把名字从`configured`改成了`applying`，因为正如 [Maciej Najbar](https://medium.com/@MaciejNajbar) 在评论中指出的，这个概念在 Kotlin 中被称为`apply`(感谢 Maciej！)

我还发现这个概念已经被实现为一个叫做[然后是](https://github.com/devxoul/Then)的 CocoaPod。他们现有的实现比我自己的高一级，但是我不同意这个命名，因为`then`通常与异步操作相关联。

参见 [GitHub](https://github.com/phlippieb/PHBApplying) 或 [CocoaPods](https://cocoapods.org/pods/PHBApplying) 上的 PHBApplying。

感谢阅读！