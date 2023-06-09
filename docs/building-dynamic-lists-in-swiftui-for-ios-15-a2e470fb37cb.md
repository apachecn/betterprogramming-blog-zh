# 在 SwiftUI 中为 iOS 15 构建动态列表

> 原文：<https://betterprogramming.pub/building-dynamic-lists-in-swiftui-for-ios-15-a2e470fb37cb>

## 增强您的 SwiftUI 列表视图

![](img/97578efb3398a0dcdcb5eaff3baef6b5.png)

照片由 [Amadej Tauses](https://unsplash.com/@amadejtauses?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

[在之前的](/customizing-views-inside-swiftui-lists-7b2cacdd9fd0)中，我们看了如何使用`List`视图来创建*静态*列表视图。静态列表视图对于在 iOS 应用中创建菜单或设置屏幕很有用，但是当我们将它们连接到数据源时，`List`视图变得更加有用。

今天，我们来看几个例子，看看如何使用`List`视图来显示数据的动态列表，比如图书列表。我们还将了解如何使用苹果在 iOS 15 最新版本的 SwiftUI 中添加的一些新功能，如 pull-to-refresh，一种搜索 UI，以及一种使用 async/await 从异步 API 获取数据的简单方法，如远程服务。

# 显示元素列表

创建列表的方法有很多种，正如我们将在本系列后面看到的，你既可以创建平面列表，也可以创建层次*、*嵌套列表。由于所有的列表行都是按需计算的，`List`视图甚至对于包含许多条目的集合也表现良好。

基于元素集合创建一个`List`视图的最简单方法是使用它的构造函数，该构造函数接受一个`RandomAccessCollection`和一个用于行内容的视图构建器:

在视图构建器中，我们以一种类型安全的方式访问集合中的单个元素。这意味着我们可以访问集合元素的属性，并使用 SwiftUI 视图如`Text`来呈现单个行，如下例所示:

因为这个视图充当我们想要显示的数据的所有者，所以我们使用一个`@StateObject`来保存视图模型。视图模型公开了保存图书列表的 published 属性。为了简单起见，这是一个静态列表，但是在现实世界的应用程序中，您可以从远程 API 或本地数据库获取这些数据。

注意我们如何通过编写`book.title`或`book.author`来访问`List`中`Book`元素的属性。这里，我们使用一个`Text`视图，通过字符串插值来显示一本书的标题和作者。

由于 SwiftUI 的声明性语法，我们可以轻松地构建更复杂的自定义 UI 来呈现数据——就像我们上次看到的那样。

让我们使用下面的代码，用一个更精细的行来替换上面代码片段中的`Text`视图，该行显示图书封面、标题、作者和页数:

使用 Xcode 的 SwiftUI 重构工具，我们可以将这些代码提取到一个自定义视图中，使我们的代码更容易阅读。

查看我关于构建 SwiftUI 视图的视频，了解这种(以及其他)重构的实际应用:

因为我们不打算修改列表行中的数据(或者细节视图中的数据)，所以我们将列表项作为一个简单的引用传递给该行。如果我们想要修改列表行中的数据(例如，将一本书标记为收藏，或者将它传递到用户可以编辑书的详细信息的子屏幕)，我们必须使用列表绑定。

# 使用列表绑定允许修改列表项

通常，视图中的数据是不可修改的。要修改数据，需要将其作为一个`@State`属性或一个`@ObservedObject`视图模型来管理。为了允许用户修改子视图中的数据(例如，`TextField`或详细信息屏幕)，我们需要使用绑定将子视图中的数据连接到父视图中的状态。

在 SwiftUI for iOS 15 之前，没有直接的方法来绑定列表的元素，所以人们必须想出自己的解决方案。我以前在这篇[文章](https://peterfriese.dev/swiftui-list-item-bindings-behind-the-scenes/)中写过这个问题，在文章中我对比了不正确和正确的做法。

在 iOS 15 的 SwiftUI 中，Apple 引入了一种直接的方式来访问作为绑定的列表项，使用以下语法:

为了允许我们的示例应用程序的用户在列表视图中编辑书籍的标题，我们所要做的就是更新书籍列表视图，如下所示:

当然，这也适用于自定义视图。下面是如何更新`BookRowView`使书名可编辑:

这里的重点是在子视图中使用`@Binding`。通过这样做，父视图保留了传递给子视图的数据的所有权，同时允许子视图修改数据。真理的来源*是父视图中`ObservableObject`上的`@Published`属性。*

要阅读更多关于列表绑定的内容，以及这个特性是如何工作的，请查看我的文章 [SwiftUI 列表绑定](https://peterfriese.dev/swiftui-list-item-bindings-behind-the-scenes/)。

# 异步获取数据

本文接下来的部分有一个共同点——它们都基于苹果公司处理异步代码的新 API。

在 WWDC 21 上，苹果推出了 Swift 新的并发模型，作为 Swift 5.5 的一部分。Swift 的下一个版本目前处于测试阶段，但你已经可以通过从[这里](https://developer.apple.com/download/)下载 Xcode 13 的测试版来体验新功能了。

在前面的例子中，我们使用了静态数据列表。这种方法的优点是我们不必获取(并等待)这些数据，因为它们已经在内存中了。这对于例子来说很好，因为它允许我们关注相关的东西，但是它不能反映现实。

在现实世界的应用程序中，我们通常显示来自远程 API 的数据，这通常意味着执行异步调用:当我们等待来自远程 API 的结果时，应用程序需要继续更新 UI。如果它不这样做，用户可能会得到应用程序挂起甚至崩溃的印象。

因此，在接下来的例子中，我将展示如何利用 Swift 的新并发模型来处理异步代码。

获取数据的一个好时机是当用户导航到一个新的屏幕并且这个屏幕刚刚出现的时候。在 SwiftUI 的以前版本中，使用`.onAppear` view 修饰符是请求数据的好地方。从 iOS 15 开始，SwiftUI 包括了一个新的视图修改器，让这个变得更加简单:`.task`。当视图出现时，它将启动一个异步的`Task`，一旦视图消失，它将取消这个任务(如果任务还在运行的话)。如果您的任务是一个长时间运行的下载，并且您想在用户离开屏幕时自动中止它，这将非常有用。

使用`.task`就像将它应用到你的`List`视图一样简单。下面是如何做到这一点:

在视图模型中，您可以使用异步 API 来获取数据。在本例中，我模仿了后端以使代码更容易阅读，并添加了人工延迟，如下所示:

如果您尝试像这样运行代码，您将会得到一个运行时警告，说*“不允许从后台线程发布更改；确保在模型更新时从主线程发布值(通过 receive(on:)之类的操作符，"*

这个运行时错误的原因是`refreshable`里面的代码没有在主线程上执行。然而，UI 更新*必须*在主线程上执行。在过去，我们必须使用`DispatchQueue.main.async { ... }`来确保任何 UI 更新都在主线程上执行。

然而，使用 Swift 的新并发模型，有一种更简单的方法:我们所要做的就是使用`@MainActor`属性包装器对任何执行 UI 更新的方法(或类)进行操作。这指示编译器在执行这段代码时切换到主参与者，从而确保任何 UI 更新都在主线程上运行。下面是更新后的代码:

要了解更多有关 Swift 新并发模式的信息，请查看 YouTube 上的视频系列。

# 拉至刷新

除非你使用像 [Cloud Firestore](https://firebase.google.com/docs/firestore) 这样的 SDK，让你可以实时听到后端的更新，否则你会想给你的应用添加一些 UI 启示，让你的用户可以轻松地请求最新的数据。让用户刷新数据的最常见方式之一是*拉动刷新*，Loren Brichter 于 2008 年在 Tweetie 应用中[流行起来(后来被 Twitter 收购，并作为 iOS 版 Twitter 重新推出)。](https://www.imore.com/hall-fame-loren-brichter-and-tweetie)

由于 SwiftUI 的声明性，只需几行代码就可以轻松地将该功能添加到您的应用程序中。而且，如上所述，该功能还利用了 Swift 的新并发模型，以确保您的应用的 UI 即使在需要等待任何更新时也能保持响应。

将`refreshable`视图修改器添加到您的`List`视图就是将*拉至刷新*添加到您的应用程序的全部工作:

如关键字`await`所示，`refreshable`打开一个异步执行上下文。这要求你从`refreshable`内部调用的代码异步执行(如果你调用的代码可以非异步执行，因为它会立即返回，这也很好。但通常情况下，您会希望与需要异步调用的远程 API 进行通信。

为了让您了解这可能是什么样子，我创建了一个视图模型，通过添加一些人工等待时间来模拟异步远程 API，如下所示:

让我们看一下这段代码，了解一下发生了什么。

1.  与前面的示例一样，`books`是视图订阅的已发布属性。
2.  `generateNewBook`是一个本地函数，使用优秀的 [LoremSwiftum](https://github.com/lukaskubanek/LoremSwiftum) 库产生一个随机的新`Book`实例。
3.  在`refresh`内部，我们调用`generateBook`生成一本新书，然后将其插入到已发布的属性`books`中，但是在我们这样做之前，我们使用`Task.sleep`调用告诉应用程序休眠两秒钟。这是一个异步调用，所以我们需要用`await`来调用它。

和前面的例子一样，这段代码将产生一个紫色的运行时警告:

> 不允许从后台线程发布更改；确保在模型更新时从主线程发布值(通过像 receive(on:)这样的操作符)

所以我们需要使用`@MainActor`来确保所有的更新都发生在主参与者身上。这一次，我们不是只标记`refresh`方法，而是将整个视图模型标记为`@MainActor`，如下所示:

在我们结束这一部分之前的最后一个调整:您会注意到，当通过拖动刷新向列表添加新项目时，新添加的项目会立即出现，没有平滑的过渡。

由于 SwiftUI 的声明式语法，添加动画使其感觉更加自然非常容易。我们需要做的就是给`List`视图添加一个`animation`视图修改器，如下所示:

通过提供`value`参数，我们可以确保这个动画只在列表视图的内容改变时运行。例如，它可能在插入或删除新项目时运行。

为了完善动画，我们还将在视图模型的`refresh`函数的末尾添加一个短暂的暂停。这确保了在进度微调器消失之前，新行以平滑过渡的方式出现。代码如下:

# 搜索

SwiftUI 使得在`List`视图中实现搜索变得容易。您所需要做的就是将`.searchable`视图修饰符应用到列表视图，SwiftUI 将自动为您处理所有的 UI 方面。它将显示一个搜索字段(并确保当您第一次显示列表视图时它不在屏幕上，就像您对本机应用程序的期望一样)。它还具有触发搜索和清除搜索字段的所有 UI 启示)。

剩下唯一要做的事情就是实际执行搜索并提供适当的结果集。

一般而言，搜索屏幕既可以本地操作(即，过滤列表视图中显示的项目)，也可以远程操作(即，针对远程 API 执行查询，并且仅显示该调用的结果)。

在本节中，我们将研究如何过滤列表视图中显示的元素。为此，我们将使用 async/await 和 Combine 的组合。

首先，我们将构建一个简单的`List`视图，显示视图模型中的图书列表。这对您来说应该很熟悉，因为我们重用了前面例子中使用的大部分代码。下面是新代码:

该视图模型与我们之前使用的模型非常相似，但有一个重要的区别—图书集合最初是空的，如下所示:

为了给`SearchableBooksListView`添加一个搜索 UI，我们应用了`.searchable`视图修改器，并将其`text`参数绑定到视图模型上的一个新的`searchTerm`属性。代码如下:

这将在`List`视图中安装搜索 UI，但是如果运行这段代码，什么也不会发生。事实上，您甚至不会在列表视图中看到任何书籍。

为了改变这一点，我们将向视图模型添加一个新的私有属性，该属性保存原始的未过滤的图书列表。最后，我们将设置一个组合管道，根据用户输入的搜索条件过滤这个列表:

这种组合管道是如何工作的？

1.  我们使用`Publishers.CombineLatest`来获取两个发布者`$originalBooks`和`$searchTerm`的最新状态。在现实世界的应用程序中，我们可能会在后台接收到图书收藏的更新，我们希望这些更新也包含在搜索结果中。每当这些发布者之一发送新事件时，`CombineLatest`发布者将发布包含`originalBooks`和`searchTerm`的最新值的新元组。
2.  然后我们使用`.map`操作符将`(books, searchTerm)`元组转换成一个图书数组，我们最终将该数组分配给 published `$books`属性，该属性连接到`SearchableBooksListView`。
3.  在`.map`闭包内，我们使用`filter`只返回标题或作者姓名中包含搜索词的书籍。流程的这一部分实际上不是特定于组合的- `filter`是`Array`上的一个方法。

如果你运行这段代码，你会发现你在搜索栏中输入的所有内容都会自动大写。为了防止这种情况，我们可以在视图修改器`searchable`之后应用`. autocapitalization`视图修改器- *，如下所示:*

# 关闭

SwiftUI 中的列表视图非常强大，随着 WWDC 21 上宣布的功能，苹果正在弥合 UIKit 和 SwiftUI 的`List` API 之间存在的许多差距。

有些特性有点隐藏，但谢天谢地官方文档正在赶上。阅读 SwiftUI 头文件中的源文档总是有好处的。如果我们能获得 SwiftUI 的源代码，那就更好了，但我不会为这个屏住呼吸。

同时，我希望这一系列文章有助于阐明苹果平台上最常用的用户界面范例之一。欢迎随时在 [Twitter](https://twitter.com/peterfriese) 上与我联系，如果你有任何问题或评论，请不要犹豫，发送推文或 DM。

在下一集，我们将研究样式`List`视图。苹果在最新版本中增加了一些令人兴奋的造型功能，使得在你的应用品牌中采用`List`变得更加容易。

感谢阅读。下次见！

*最初发布于*[*https://peterfriese . dev*](https://peterfriese.dev/swiftui-listview-part2/)*。*