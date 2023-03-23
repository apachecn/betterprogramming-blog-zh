# 如何用 Combine 框架查询多个 API

> 原文：<https://betterprogramming.pub/how-to-query-multiple-apis-with-the-combine-framework-1c4e0298418e>

## 使用 Combine 对多个数据源进行多个异步服务调用

![](img/3e2043ad62118da886aed25513f1fa47.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[梅达·达伍德](https://unsplash.com/@medhatdawoud?utm_source=medium&utm_medium=referral)拍摄的照片。

*注意:如果您现在不想使用 Combine，您可以查看本文* *中的* [*的原生方法。*](https://medium.com/better-programming/how-to-query-multiple-apis-with-dispatchworkitem-and-dispatchgroup-41a757692367)

# 介绍

Combine 是苹果公司开发的一个新框架，在 2019 年 WWDC 上推出。它是一个用于处理异步操作的声明性 Swift API。换句话说，这是苹果做函数式反应式编程的方式。Combine 构建在发布-订阅模式之上。如果您熟悉该模式或任何其他 FRP 框架，如 RxSwift、ReactiveSwift 等。，你会更容易理解。

我不打算介绍出版商、订户或主题。如果你对它们一无所知，你应该先读一读。我打算直接跳到代码。然而，我在本文末尾提供了一些联合资源。

据苹果开发者称，Combine“提供了一个声明性的 Swift API，用于随时间处理值。”

您可以在下面找到示例项目:

[](https://github.com/ibrahimoktay/AsyncProgramming) [## Ibrahim oktay/异步编程

### 演示应用程序-使用 DispatchWorkItem 和 DispatchGroup。通过创建……为 ibrahimoktay/async 编程开发做出贡献

github.com](https://github.com/ibrahimoktay/AsyncProgramming) 

`DispatchWorkItem`和`DispatchGroup`用于主分支。我为联合收割机创建了另一个分支。你能猜出它的名字吗？它是结合在一起。我会将示例项目逐步转换为组合。

# 第一步

我们需要一种方法来发出网络请求。幸运的是，苹果已经建立了一个返回出版商的方法(`dataTaskPublisher`)。在现实世界的 app 中，这种方法可能是不够的。通常，你会做更多的错误检查，转换错误消息，拦截器，或者你可能有插件，等等。

为了保持代码简单，我将把它写成这样:

# 第二步

我将在我的`ImageService`协议中添加另一个方法，它将返回一个 publisher。您可能会注意到关键字`Never`。基本上就是说不会失败。我这样做是因为我将用一个空的字符串数组替换`error`。这个方法将从我们的视图模型中调用:

```
**func** search(**_** query: String) -> AnyPublisher<[String], Never>
```

让我们在服务层实现代码。我们只是将`UnsplashResponse`转换成一个字符串数组，并用一个空数组替换任何错误。

现在，我们可以在视图模型中调用这个方法。让我们更改 ViewModel 中的代码:

我们在这里暂停一下。我们刚刚改变了视图模型中的`search`方法。此时，您可以运行项目并查看结果。然而，它并不做与主分支中相同的工作，因为我们只查询第一个服务(`services.first?`)。别担心，我们以后会改变的。我们称之为来自视图控制器的`completion`。因此，我们不需要修改视图控制器中的任何代码。`[Cancellable](https://developer.apple.com/documentation/combine/cancellable)`是表示可以取消流程的协议。我们代码中的`cancellable`其实就是`[AnyCancellable](https://developer.apple.com/documentation/combine/anycancellable)`。你需要一个对`sink`方法结果的强引用。否则 ARC 会清理掉，你什么都看不到。

还有一个区别，那就是延迟。我们不会推迟我们的请求。因此，让我们以组合的方式实现那些缺失的部分，并在更新视图控制器时使用发布器。

在视图控制器中实现:

```
**func** updateSearchResults(for searchController: UISearchController) {
  viewModel.query = searchController.searchBar.text ?? ""
}
```

而不是这个:

# 第三步

让我们改变我们的视图模型:

嗯，就是这样。现在，我们的代码比用`DispatchWorkItem`和`DispatchGroup`构建的示例项目要少。看到`published`关键词了吗？这是属性包装器和组合带来的另一个奇特特性。每次您给`query`设置一个值，它将发出这些值:

```
query = "a"
query = "ab"
query = "abc"
```

我们有两个`@Published`值，一个用于 ViewController(以便它可以观察`imageUrls`)，另一个用于 ViewModel。我们使用包装发布器来访问`$query`，这允许我们过滤流并延迟它:

```
$query.filter{ $0.count > 3 } // filter if character count is less than 4
```

如果字符数小于 4，我们不必返回空字符串数组或取消任何请求，因为我们不会预先触发任何操作。我们可以用一行代码添加一个延迟:

```
.debounce(for: .seconds(1), scheduler: RunLoop.main)
```

我们的包装纸准备好了。

这里是所有奇迹发生的地方。我们查询`Services`数组中的每个服务，并合并另一个发布者中的所有请求。这个新的发布者等待每个服务的响应。最后，我们必须将结果扁平化，因为合并操作会转换我们的数据。就是这样。

# 结论

组合是一个新的框架。尽管有 iOS 13.0+这样的限制，但它会变得更加流行和强大。Timer、URLSession 和 NotificationCenter 已经采用了 Combine 框架。日复一日，更多的 API 将跟随这一趋势。在我看来，未来我们会看到很多 SwiftUI 和 Combine。我认为你用得越多，你就会越喜欢它，你会一直喜欢它，直到你讨厌它。如果你是一只孤独的狼，你可能不会那么讨厌它。

感谢您的阅读。任何反馈都将不胜感激。如果你有任何问题，请与我联系。

# 资源

 [## 结合

### Combine 框架提供了一个声明性的 Swift API，用于随时处理值。这些值可以代表许多…

developer.apple.com](https://developer.apple.com/documentation/combine) [](https://developer.apple.com/videos/play/wwdc2019/722/) [## 介绍联合收割机- WWDC 2019 -视频-苹果开发者

### Combine 是一个统一的声明性框架，用于随时间处理值。了解它如何简化异步代码…

developer.apple.com](https://developer.apple.com/videos/play/wwdc2019/722/) [](https://developer.apple.com/videos/play/wwdc2019/721/) [## 结合实践- WWDC 2019 -视频-苹果开发者

### 扩展您对 Combine 的了解，Combine 是 Apple 新的统一声明式框架，用于处理随时间变化的值。学习…

developer.apple.com](https://developer.apple.com/videos/play/wwdc2019/721/)