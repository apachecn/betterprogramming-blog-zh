# Swift 中的功能依赖注入

> 原文：<https://betterprogramming.pub/factory-and-functional-dependency-injection-2d0a38042d05>

## 面向协议编程的替代方案。

![](img/2fc0fae6d02ba15d557780f95655263f.png)

照片由[莎拉·巴克西](https://unsplash.com/@sarabakhshi?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/injection?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

几乎我们所有人都创建了基于服务的协议，以便在我们的应用程序中进行单元测试。在 SwiftUI 中开发时，我们还实现了预览功能。

模式很简单。创建一个描述你想要做什么的协议。然后创建实现该协议的类或结构。接下来，实现一些方法，将协议实例注入到依赖它的类、服务或视图中。

这可以使用传统的依赖注入技术来完成，比如构造函数或属性注入，或者可以使用现代的基于容器的系统，比如 [Factory](https://github.com/hmlongco/Factory) 。

然后，最后，我们实现一个或两个 mock 或 stub 来实现协议的虚拟版本，并在我们想要编写测试或实现预览时注入*和*。

*注意:下面的例子使用了工厂，但是同样的技术可以用于几乎任何依赖注入模式。*

## 一个例子

说得够多了，让我们定义一个可以实现的非常简单的服务，这样我们就可以查看一些代码，看看我们在这里谈论的是什么。

我选择的例子是一个打开 URL 的简单协议。在经典的 POP 风格中，为了使事情更简单，我们将在现有的`UIApplication`上可用的`openURL`函数上建模我们的协议。

```
protocol URLOpening {
    func openURL(_ url: URL) -> Bool
}
```

然后我们确保`UIApplication`符合我们的协议。

```
extension UIApplication: URLOpening {}
```

有了协议，我们可以创建一个工厂来提供它。

```
extension Container {
    static let urlOpening = Factory<URLOpening> {
        UIApplication.shared
    }
}
```

因为`UIApplication`符合我们的协议，所以只要需要依赖，我们就可以获取共享实例。

现在，我们终于可以使用我们新发明的服务了。这是我们的视图模型。

```
class MyViewModel {
    @Injected(urlOpening) var urlOpener
    func open(site: String) {
        _ = urlOpener.openURL(URL(string: site)!)
    }
}
```

一切都好。我们还可以创建一个 URL 打开服务的模拟版本，并使用它进行测试和预览。

```
class URLOpenningMock: URLOpening {
    var openedURL: URL?
    func openURL(_ url: URL) -> Bool {
        openedURL = url
        return false
    }
}
```

mock 只是记录要求它打开的任何网站的 URL。如果*应该*已经打开了一些东西，但是站点仍然是空的，那么我们知道我们有一个问题。下面是使用它的测试。

```
func testOpenFuctionality() throws {
    let mock = URLOpenningMock()
    Container.urlOpening.register { mock }
    let viewModel = MyViewModel()
    viewModel.open(site: "[https://google.com](https://google.com)")
    XCTAssert(mock.openedURL != nil)
}
```

这些都不是新的，只是面向协议编程的工作方式。

但是…我们能做得更好吗？

## 功能注射

为了给提供单一功能的服务创建一个协议，我们费了很大的劲。仔细想想，我们可能会问几个简单的问题:

1.  我们真的需要服务容器吗？
2.  我们能在需要的地方注入*功能*吗？

事实证明我们可以。观察下面的 typealias 和工厂定义。

```
typealias OpenURLFunction = (_ url: URL) -> Boolextension Container {
    static let openURL = Factory<OpenURLFunction> {
        UIApplication.shared.openURL
    }
}
```

与我们之前的协议一样，typealias 与`openURL`函数签名完全匹配，这样我们就可以创建一个设计为*而不是*返回服务对象的工厂，而是一个*函数*。

在工厂内部，我们将`openURL` *函数*引用传递给我们的工厂，以备需要时使用，而不是使用`UIApplication`的实例。这个工厂可以像我们以前一样通过注射来消耗。

```
class MyViewModel: View {
    @Injected(Container.openURL) private var openURL
    func open(site: String) {
        _ = openURL(URL(string: site)!)
    }
}
```

相同的`Injected`属性包装器。但是请注意，现在我们的 open 函数直接调用函数，而不是调用服务上的方法。

到目前为止，这看起来干净多了。测试呢？

```
func testOpenFuctionality() throws {
    var openedURL: URL?
    Container.openURL.register {
        { url in
            openedURL = url
            return false
        }
    }
    let viewModel = MyViewModel()
    viewModel.open(site: "https://google.com")
    XCTAssert(openedURL != nil)
}
```

如你所见，为了验证我们的 open 函数是否被正确调用，我们的代码所做的工作与我们之前在 mock 和原始测试中所做的工作基本相同。

然而，我们确实用 typealias 替换了我们的协议，我们不再需要类符合那个协议，并且我们不再需要创建模拟对象。

底线是我们节省了几行代码，但是总的来说*可能*看起来这种技术是一种浪费。

让我们再试一次，看看是否有一个案例，它的优势可能会更明显。

## 网络请求

看看下面的 typealias 和 registration。

```
typealias AccountProviding = () async throws -> [Account]extension Container {
    static let accountProvider = Factory<AccountProviding> {
        { try await Network.get(path: "/accounts") }
    }
}
```

这种注入依赖于一个通用的网络函数，该函数自动从服务器获取数据，并将其解码为适当的类型。那里的代码并不重要，我将把它留给读者作为练习。

但是现在，让我们来看一看使用这种依赖关系的视图模型上的一个测试。

```
func testAllAccounts() async throws {
    Container.accountProvider.register {{ Account.mockAccounts }}
    do {
        let viewModel = AccountViewModel()
        try await viewModel.load()
        XCTAssert(viewModel.accounts.count == 5)
    } catch {
        XCTFail("Account load failed")
    }
}
```

这里，我们用一个简单的函数替换了注册，该函数用于返回一组五个预定义的模拟帐户。

显然，我们可以返回更多的帐户。或者一个。或者没有。

```
func testEmptyAccounts() async throws {
    Container.accountProvider.register {{ [] }}
    ...
}
```

或者编写一个测试来确保我们的错误处理能够胜任工作。

```
func testErrorLoadingAccounts() async throws {
    Container.accountProvider.register {{ throw APIError.network }}
    ...
}
```

正如您所看到的，以这种方式使用函数依赖使得*非常容易返回边缘情况，如空的帐户列表，或者模拟特定的错误情况，以确保它们得到正确处理。*

我们做到了这一切，却没有编写大量代码来创建一组必须遵守特定协议的模拟服务类或存根。

```
struct MockNoAccounts: AccountLoading {
    func loadAccounts() async throws -> [Account] {
        []
    }
}Container.accountLoading.register { MockNoAccounts() }
```

## 网络容器

我现在也在考虑在`NetworkContainer`的一个工厂中使用它，在那里我可以放置我的大部分 API 调用存根。

```
class NetworkContainer: SharedContainer { typealias AccountProviding = () async throws -> [Account] static let accountProvider = Factory<AccountProviding> {
        { try await Network.get(path: "/accounts") }
    } typealias UserProviding = () async throws -> User static let userProvider = Factory<UserProviding> {
        { try await Network.get(path: "/user") }
    }}
```

同样，我获得了注入的优势，而不需要在每种情况下都创建特定的协议、实现对象和一组模拟对象。

我还没有决定是继续使用 typealias，还是工厂名就足够了，我应该直接指定工厂的函数签名。

```
class NetworkContainer: SharedContainer {

    static let accounts = Factory<() async throws -> [Account]> {
        { try await Network.get(path: "/accounts") }
    }

    static let user = Factory<() async throws -> User> {
        { try await Network.get(path: "/user") }
    }}
```

决定，决定。

## 完成块

原来如此。

我认为这项技术最大的优势不在于标准的应用程序功能，而在于我们现在可以轻松地测试这些功能。

您已经看到了我们如何简单地为视图模型提供一个新的函数，该函数返回我们需要的边缘情况或错误，而不需要定义协议，也不需要创建符合该协议的新存根或模拟服务。

从我的角度来看，任何使编写测试和预览代码更快更容易的东西都值得重新审视。还有第三个。第四个也是如此。

这是否在所有情况下都取代了面向协议编程的需要？显然不是。复杂的服务和存储库仍然存在，我们仍然需要复杂的协议来帮助管理它们。

但是对于简单的服务和依赖来说，功能注入可能是最好的选择。

你觉得怎么样？打算在你的代码中尝试一下吗？

请在下面的评论中告诉我，如果你想看更多，请点击“喜欢”按钮几次。

*本文是* [*雨燕依赖注入系列*](https://medium.com/p/365ce5038ef7/edit) *的一部分。*