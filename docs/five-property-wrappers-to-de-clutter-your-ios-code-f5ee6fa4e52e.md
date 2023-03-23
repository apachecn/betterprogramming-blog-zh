# 5 个属性包装器来整理你的 iOS 代码

> 原文：<https://betterprogramming.pub/five-property-wrappers-to-de-clutter-your-ios-code-f5ee6fa4e52e>

## 你可能熟悉@State 和@EnvironmentObject，这里还有 5 个

![](img/10565aaeb6a53c473aaf076fb1f5c316.png)

[毛绒设计工作室](https://unsplash.com/@plushdesignstudio?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/organising?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Swift 5.1 给了我们属性包装器，允许我们复制我们自己从关键字中获得的功能。如果你用过 SwiftUI，你会发现它们无处不在，其中`@State`和`@EnvironmentObject`是开发的关键。

为了帮助您开始在应用程序中使用属性包装器，我收集了一些非 SwiftUI 的示例，它们改进了我的工作流程。

# **1。已获取**

Fetched 消除了设置获取请求和`NSFetchedResultsControllers`的痛苦，并用一个简单的变量声明来代替它。

不用编写服务或者用核心数据逻辑来混淆视图模型，您只需将`@Fetched`添加到一个变量中，并让它自动排序。

有了这个包装器，你可以完全免费地获得实时更新数据，你所要做的就是获得`wrappedValue`。

# 2.普遍存在的

在编写现代 iOS 应用程序时，您可能需要保存一些关于用户的信息。

我见过的最好的方法是创建管理器类，它为您照看单个属性，并在需要时获取它们。我见过的最简单的方法就是在声明变量时获取它。

如果我们可以拥有一行程序的简单性，拥有一个 manager 类的干净底层代码，并将这些值存储在 iCloud 中，会怎么样？

输入`Ubiquitous`。

非常感谢[安托万·范德·李·🇳🇱](https://medium.com/u/7946a85da51b?source=post_page-----f5ee6fa4e52e--------------------------------)的灵感，我所做的只是将用户默认设置替换为`NSUbiquitousKeyValueStore`以获得 iCloud 支持，并注入可测试性。

# 3.文本

或者，在这种情况下，`Title`。

当使用编程布局时，您会发现自己在重复设置`UILabel`的属性。

虽然代码不多，但你可以重用其中的很多，你会发现复杂的视图变得非常大，非常快。如果您正在附近进行约束逻辑，这一点尤其正确。

大多数设计良好的应用程序都有一些严格遵循的文本样式，你不希望重复制作标签并用这些值来设置它们。

通常，您有一个想要显示的字符串，并且知道如何显示它。为什么不让包装器为您处理所有这些呢？

在这个例子中，我将标题标签的代码包装到了`Title`属性包装器中，这意味着每个带有标题的屏幕都简单地使用了`@Title`。

# 4.卡片

遵循与`Text`相同的模式，您可以使用属性包装器来设置`UIView`的公共属性

以一张基础卡为例，你有一个特定的阴影和圆角半径来应用。您可以很容易地创建一个`UIView`子类来为您设置所有这些属性——但是如果您所做的只是使用现有的属性，为什么要走那么远呢？

`Card`可应用于任何`UIView`并设置整个视图，包括拐角曲线和阴影不透明度等内容。

正如您所看到的——属性包装器和`UIView`的这种模式确实有助于减少编程布局代码的大小。

这甚至可以更多地用于向视图添加约束，其中`@Pinned`是一个可能的解决方案，将一个视图固定到它的 superview。

# 5.图像下载

我保证这不仅仅是设置 UIKit 属性。

图像下载是一个非常琐碎的任务，但是它最终会涉及到大量的代码。您有从数据创建`UIImage`的`URLSession`代码，甚至可能有一个缓存。

在这个例子中，`ImageDownloading`包装器将为您设置一个占位符图像(如果它存在的话)，然后继续从 web 下载图像并将其设置在`UIImageView`上。

它甚至处理线程，以帮助保持主线程漂亮和自由地呈现您的用户界面。

`ImageDownloading`可以很容易地被扩展以包含对共享缓存的引用，或者适合与可重用的视图一起工作。

# 结论

我希望这些属性包装器对您有所帮助，并启发您自己添加一些——它们是避免重复代码的一种强大的新方法，并且它们帮助我弥合了 SwiftUI 和声明性 UIKit 之间的差距。

感谢阅读！