# 我们正在加载，我们正在加载…

> 原文：<https://betterprogramming.pub/swiftui-were-loading-we-re-loading-7d689fa8b0c7>

## 如何让你的 API 调用加载一次…并且只加载一次。

![](img/d54a4f14bcd9e1ddd1ffb5bfe3c4e6b2.png)

UIKit 和 UIViewControllers 在控制生命周期事件方面给了我们很多选择:`viewDidLoad`、`viewWillAppear`、`viewDidAppear`、`viewWillDisappear`、`viewDidDisappear`等等。

SwiftUI 则基本上给了我们`onAppear`和`onDisappear`。因此，如果我们想为一个视图加载一些数据，我们通常会像下面这样做:

```
struct MyAccountListView: View { @StateObject var viewModel = MyAccountListViewModel() var body: some View {
        List {
            ForEach(viewModel.accounts, id: \.id) { account in
                NavigationLink(destination: Details(account)) {
                    AccountListCellView(account: account)
                }
            }
        }
        .onAppear {
            viewModel.load()
        }
    }
}
```

只需在`onAppear`中调用`load()`，天下太平。对吗？

好吧，如果你已经使用 SwiftUI 有一段时间了，那么你可能知道答案并不那么简单。虽然下面的大多数解决方案相对简单，但我在互联网上看到了足够多的问题(和有问题的解决方案),表明它们也不是那么明显。

所以让我们开始吧。首先，我们需要理解手头的问题。

## 去了又回来

第一个也是最明显的问题在于我们的导航链接。点击列表中的一个“账户”,你会进入一个新的账户详情页面。但是当你从那个页面返回时会发生什么呢？

正确。您的视图再次“出现”,因此加载您的数据的请求也再次发出。

当使用选择器列表和数据输入子表单进行大量来回导航的表单时，也会出现同样的问题。点击一个项目，在新屏幕上显示一个选择列表。选择一个项目，列表返回到再次出现的原始屏幕。

所有这些问题都可能因为 SwiftUI(出于只有 SwiftUI 知道的原因)在给定的转换期间也可能不止一次调用`onAppear`和`onDisappear`处理程序而加剧。在 3.0 中，这种情况有所改善，但仍有可能发生。

这并不重要*为什么，*是吗？我们仍然有导航问题，我们仍然希望加载我们的数据一次，只有一次。

那我们该怎么办？

**标志**

好吧，如果你已经编程好几天了，第一个(也是最明显的解决方案)是拿起我们工具箱里的锤子，设置一个标志。考虑一下。

```
class MyAccountListViewModel: ObservableObject { @Published var accounts: [Account] = [] private var shouldLoad = true func load() {
        guard shouldLoad else { return }
        shouldLoad = false
        // load our data here
    }
}
```

结案了。问题解决了。但是这个解决方案，就解决方案而言，还有一点需要改进，因为我们必须在视图模型中声明变量，保护它，然后记得重置我们的标志。

而且它太挑剔了，我们可能想要写一些额外的单元测试，只是为了确保我们得到了所有正确的东西。

总的来说，它有点…嗯，只能说它不太优雅。我们能做得更好吗？

## 原子学

嗯，我们可以导入新的 Atomics 库并删除额外的赋值语句。

```
private var loading = ManagedAtomic(true)func load() {
  guard loading.exchange(false, ordering: .relaxed) else { return }
  // load our data here
}
```

原子值上的`exchange`函数会将 loading 设置为新值(false)，但会返回原始值进行评估。它消除了对额外代码行的需求，但这样做的代价是一些复杂性和许多 Swift 开发人员可能不熟悉的库的使用。

在这种情况下，这也是矫枉过正，因为这段代码不太可能是可重入的，也不太可能跨多个线程调用。

## 派遣一次

在过去，当大规模的 Objective-C 程序还在地球上运行时，我们可以使用 GCD 和`dispatch_once`来确保给定的代码块被调用一次，并且只被调用一次。

```
var token: dispatch_once_t = 0func load() {
  dispatch_once(&token) {
    // load our data here
  }
}
```

不幸的是，`dispatch_once`在 Swift 3.0 中被弃用，现在试图使用`dispatch_once_t`会给你一个错误，告诉你使用惰性变量来代替。我们可以编写自己的版本来处理这种情况，但是…懒惰变量？

让我们考虑一下。

## 惰性变量

惰性变量在使用之前不会被实例化，Swift 保证所述初始化只会发生一次。听起来正是我们需要的行为。

那么，如果我们用一个延迟加载的函数替换我们的加载函数会怎么样呢？

```
class MyAccountListViewModel: ObservableObject { @Published var accounts: [Account] = [] lazy var load: () -> Void = {
     // load our data here
     return {}
  }()
}
```

这里我们创建一个带有闭包的惰性变量，它执行我们的 load 函数，然后返回一个空闭包。添加到末尾的`()`确保了当变量被访问时闭包本身被求值。

因此，在这个解决方案中，我们的加载代码在第一次计算惰性函数时被调用，然后每当再次调用`load()`时，将使用*空*闭包。

请注意，如果需要的话，我们仍然可以向 load 函数传递一个值，当然，请注意，我们返回的存根闭包也需要反映一个空的、未使用的值`{ _ in }`。

这个解决方案……还不错。它消除了额外的标志变量和守卫，代价是变得有点棘手，并让我们的加载例程纯粹作为初始惰性求值的副作用而被调用。

## 呼叫一次

当然，确保我们的代码只执行一次的最好方法是只调用它一次。考虑我们的视图模型的以下变化。

```
class MyAccountListViewModel: ObservableObject { enum State {
        case loading
        case loaded([Account])
        case empty(String)
        case error(String)
    } @Published var state: State = .loading @Injected(Container.userServiceType) var service: private var cancellables = Set<AnyCancellable>() func load() {
        service.fetch()
            .receive(on: DispatchQueue.main)
            .sink { [weak self] completion in
                switch completion {
                case .failure(let error):
                    self?.state = .error(error.localizedDescription)
                case .finished:
                    break
                }
            } receiveValue: { [weak self] (users) in
                if users.isEmpty {
                    self?.state = .empty("No users found...")
                } else {
                    self?.state = .loaded(users)
                }
            }
            .store(in: &cancellables)
    }}
```

请注意我们的状态枚举，以及我们现在正在处理错误、空状态等的事实。老实说，这些都是我们在现实生活中可能要做的事情。

现在来看看我们的视图的相应变化。

```
struct MyAccountListLoadingView: View { @StateObject var viewModel = MyAccountListViewModel() var body: some View {
        switch viewModel.state {
        case .loaded(let accounts):
            AccountListView(accounts: accounts)
        case .empty(let message):
            MessageView(message: message, color: .gray)
        case .error(let message):
            MessageView(message: message, color: .red)
        case .loading:
            ProgressView()
                .onAppear {
                    viewModel.load()
                }
        }
    }
}
```

这里我们根据视图模型的状态显示不同的视图，并且`onAppear`现在被附加到了`ProgressView`上。因为我们的视图模型的初始状态是`.loading`，所以`ProgressView`出现并且我们的 load 函数被调用。

一旦账户被加载，进度视图将被移除，取而代之的是我们的账户列表视图(或者错误消息或空消息)。

但是在任何情况下，拥有`onLoad`修饰符的视图都被删除，因此`load()`永远不会被再次调用。

我曾在 SwiftUI 中使用视图模型协议的[中详细讨论过这种技术。你做错了。](/swiftui-view-models-are-not-protocols-8c415c0325b1)在那里，我还解释了如何将这种方法与协议一起使用，以帮助测试和模拟数据。看看这个。

当然，如果你有妄想症，你可以使用这种技术*和*其中一个早期的技术只是为了绝对积极的加载只会被调用一次。(类似腰带和吊带的方法。)

## 拉至刷新

我们最后一种方法的另一个好处是，它使得像拉至刷新这样的行为变得简单而容易。

只需再次调用视图模型中的`load()`,当它完成时，加载将使用新数据、错误或消息再次更新结果状态。

您*可以*将状态重置为`.loading`，但是那会显示我们原始的进度视图以及拉至刷新微调器，这可能不是最好的用户体验。

## OnAppearOnce 视图修改器

最后，虽然我更喜欢 state 方法，但它也可以方便地回到我们最初的基于标志的方法……稍加改动。我们将把标志放在*视图*中，而不是*视图模型*中。

```
extension View {
    func onAppearOnce(_ action: @escaping () -> ()) -> some View {
        self.modifier(OnAppearOnceModifier(action: action))
    }
}private struct OnAppearOnceModifier: ViewModifier {
    let action: () -> ()
    @State private var appearOnce = true
    func body(content: Content) -> some View {
        content
            .onAppear {
                if appearOnce {
                    appearOnce = false
                    action()
                }
            }
    }
}
```

创建我们自己的`onAppearOnce`视图修改器，并添加我们自己的视图扩展来调用它，这允许我们在应用程序的许多地方和情况下使用基于标志的代码。

```
var body: some View {
    List {
        ...
    }
    .onAppearOnce {
        viewModel.load()
    }
}
```

我认为这让我们兜了一圈。

## 完成块

所以你有它。解决我们问题的几种方法。

你自己有吗？在评论里说说吧。当然，如果你想看更多，请鼓掌并订阅。

下次见。