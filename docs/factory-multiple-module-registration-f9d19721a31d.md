# 工厂:多模块注册

> 原文：<https://betterprogramming.pub/factory-multiple-module-registration-f9d19721a31d>

## 如何在初始化任何东西之前初始化所有东西？

![](img/8a0e74a6f221badbfea4f958e1aa4c5d.png)

苏珊·霍尔特·辛普森在 [Unsplash](https://unsplash.com/s/photos/blocks?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

当您想要使用一个依赖注入系统，比如具有多个模块的 Factory，您经常会遇到“谁先来”的困境。

假设 ModuleP 指定了一个抽象的`AccountLoading`协议。

```
// ModuleP
public protocol AccountLoading {
    func load() -> [Account]
}
```

接下来，我们有一个会计模块 ModuleA，它显示我们的帐户，但是需要其中一个加载器来加载它们。

最后，我们还有一个模块，我们称之为 ModuleB，它知道如何构建我们需要的任何类型的加载程序。

注意 ModuleA 和 ModuleB 是独立的。双方都不了解对方，但都直接依赖于我们的模型和协议大师 ModuleP。

这是一种典型的模块化合同模式。

但是我们要开发一个应用程序。那么，当 ModuleA 对 ModuleB 一无所知时，它如何获得帐户加载器的实例呢？

嗯，这篇*是*一篇关于依赖注入的文章，所以让我们开始吧。

## 分解器

在我早期的依赖注入系统 [Resolver](https://github.com/hmlongco/Resolver) 中，这真的不是问题。两个模块都将导入 Resolver，ModuleA 中的某些东西在需要时会简单地向它请求一个`AccountLoading`的实例。

这里有一个注入属性包装器的例子。

```
public class ViewModel: ObservableObject {
    @Injected var loader: AccountLoading
    @Published var accounts: [Account] = []
    func load() {
        accounts = loader.load()
    }
}
```

我们很好。嗯，我的意思是，只要*某人*在这个视图模型被实例化之前和*在*注入的属性包装器请求它需要的实例之前已经注册了一个`AccountLoading`的实例，我们就很好。

如果他们没有…嗯…我们就要坠毁了。这当然是一个缺点，但是一般来说，在第一次尝试运行和测试代码时，忘记注册是很明显的。

但是我想做得更好，所以我创建了[工厂](https://github.com/hmlongco/Factory)。

## 编译时安全的

工厂承诺编译时安全。

怎么会？嗯，工厂通过确保工厂的存在来提供您正在寻找的东西来实现这一点。要做到这一点，你需要将适当的工厂对象添加到一个容器中……这个容器又必须有一个工厂闭包，当被请求时，它会提供这个对象的一个实例。

在正常的应用程序中，工厂注册通常是这样的。

```
extension Container {
    public static let accountLoader = Factory<AccountLoading> { AccountLoader() }
}
```

容器存在。类型为`AccountLoading`的工厂存在。并且提供该类型实例的 clousure 是存在的。因此，我们保证编译时和运行时都是安全的。否则代码根本无法编译。

如果模块既定义了我们的协议*又实现了协议*，那么这种类型的注册也可以在模块化应用中工作。协议类型和工厂都是公共的，而实现类型`AccountLoader`被定义为模块内部的，所以它被安全地隐藏起来。

但是在我们的跨模块应用中，我们不能这样做。

该协议在 ModuleP 中定义。具体类型`AccountLoader`存在于 ModuleB…但是 ModuleA 不知道。这件事*不可能*知道。

但是 ModuleA 中的代码需要能够看到工厂才能解决这个问题。而且那个工厂*必须*有个定义。

谁在一垒？

## 可选注册

在工厂中，解决这些问题的一般方法是让我们的工厂提供一种可选的类型。所以在 ModuleA 中我们会指定这个。

```
extension Container {
    public static let accountLoader = Factory<AccountLoading?> { nil }
}
```

这是该模块中的代码用来请求帐户加载程序的实例。这意味着我们基于工厂的视图模型代码现在看起来像这样。

```
class ViewModel: ObservableObject {
    @Injected(Container.accountLoader) var loader
    @Published var accounts: [Account] = []
    func load() {
        guard let loader else { return }
        accounts = loader.load()
    }
}
```

请注意 load 函数中的额外保护，确保我们实际上拥有我们需要的加载程序。更重要的是，可选类型的使用确保了我们的应用程序仍然是编译时安全的。如果，不知何故，有些人忘记注册一些东西，而我们最终什么都没有，我们的应用程序可能无法正常运行…但至少它不会崩溃。

那么我们如何让事情运转起来呢？再说一次，ModelA 拥有工厂。但是 ModuleB 包含了我们的网络代码，它知道如何创建一个帐户加载器。

解决方案相对简单。在主应用程序的某个地方，我们只需要写一点点代码来将我们的两个模块连接在一起。

```
import ModuleA
import ModuleB

extension Container {
    static func setupModules() {
        accountLoader.register { AccountLoader() }
    }
}
```

通过在`accountLoader`上注册一个新的工厂关闭，我们现在已经确保当被请求时，它现在将返回我们刚刚注册的新服务，而不是原始的零值。

我们快到家了。现在唯一要做的就是确保我们的应用程序调用我们的函数，在一切开始之前设置我们的注册。也许像这样。

```
@main
struct AccountingApp: App {
    init() {
        Container.setupModules()
    }
    var body: some Scene {
        WindowGroup {
            NavigationView {
                ContentView()
            }
            .navigationViewStyle(StackNavigationViewStyle())
        }
    }
}
```

我们完事了。

*但是首先附带说明:我只知道一些聪明的人会决定避免可选处理并显式打开加载器，或者显式打开工厂定义本身，但是请不要做这些事情。它基本上首先破坏了工厂背后的整个基本原理。*

## 更多问题

我是说，我们结束了？对吗？

不完全是。我们仍然有一些潜在的问题。

第一，我们必须记住调用我们的设置函数。有问题，是的，但就其本身而言，它不是一个交易破坏者。

但是第二个更阴险一点。考虑一下这个。

```
@main
struct AccountingApp: App {
    let viewModel = ModuleA.ViewModel()
    init() {
        Container.setupModules()
    }
    var body: some Scene {
        WindowGroup {
            NavigationView {
                ContentView()
            }
            .environmentObject(ViewModel)
            .navigationViewStyle(StackNavigationViewStyle())
        }
    }
}
```

假设我们需要将 ModuleA 的视图模型放入环境中，为此，我们只需创建一个视图模型作为属性。

这就是问题所在。

你看，当系统试图创建一个`AccountingApp`的实例时，该结构上的*属性*将首先被初始化，*先于*初始化器。这意味着`ViewModel`将被创建，*的*注入属性包装器将向其工厂请求一个帐户加载器…所有这些都在我们的设置函数被调用之前。

所以我们最终一无所获。字面上。

这个例子有点做作，但是在多模块环境中，它实际上比我们想象的要经常出现。

## 自动注册

Resolver 通过在第一次解析发生之前自动调用设置函数解决了这个问题，而碰巧的是，我只是将相同方法的一个版本添加到了 Factory 中。

这是我们修改后的设置功能。请注意，函数的名称已经更改为`registerAllServices`，我们的扩展现在符合`AutoRegistering`。

```
import ModuleA
import ModuleB

extension Container: AutoRegistering {
    static func registerAllServices {
        accountLoader.register { AccountLoader() }
    }
}
```

这样我们就完全不需要初始化器了。

```
@main
struct AccountingApp: App {
    let viewModel = ModuleA.ViewModel()
    var body: some Scene {
        WindowGroup {
            NavigationView {
                ContentView()
            }
            .environmentObject(ViewModel)
            .navigationViewStyle(StackNavigationViewStyle())
        }
    }
}
```

工厂现在将自动为我们呼叫`registerAllServices`，在第一个工厂决议之前一次(并且只有一次)。

## 内部构件

如果你是一个极客(如果你一直读到这里，你很可能是)，这是如何实现的内部是相当有趣的。

该协议非常简单。

```
public protocol AutoRegistering {
    static func registerAllServices()
}
```

我们之前也看到了如何使用它。但是我们如何检查容器*类*是否符合那个协议？我们如何确保我们的函数只被调用一次？

```
extension Container {
    fileprivate static var autoRegistrationCheck: Void = {
        (Container.self as? AutoRegistering.Type)?.registerAllServices()
    }()
}
```

让我们先解决这个一次性的问题。在过去，我们会使用`dispatch_once`，但是 Swift 告诉我们函数已经过时了，我们应该考虑使用静态变量。

于是我们做了，创造了`autoRegistrationCheck`。正如承诺的那样，Swift 本身确保我们的静态变量初始化器只被调用一次。

在函数内部，`(Container.self as? AutoRegistering.Type)`代码看起来有点奇怪，但基本上它只是检查是否有人扩展了`Container`类型以符合`AutoRegistering`类型。

如果转换类型不为零，我们在类类型上调用`registerAllServices`。简单。

工厂中唯一剩下的事情就是在第一个决议之前调用它。

```
func resolve(_ params: P) -> T {
     let _ = Container.autoRegistrationCheck
     // remaining code
}
```

如果容器符合`AutoRegistering`，那么`registerAllServices`作为副作用被调用，返回的`Void`被丢弃。在随后的调用中，我们向静态变量请求一个`Void`,无论如何我们都要丢弃它……这导致执行的代码非常少。(这上面的 SIL 代码很有意思。)

Resolver 做同样事情的内部检查要比这简单得多，但是如果您愿意，欢迎您检查这些代码。

## 完成块

就是这样。一些与 Factory 一起使用的多模块注册和解析策略，以及魔术如何发生的幕后简要介绍。

你知道该怎么做。在下面的部分提出你的问题和评论，如果你想看更多，请按住“喜欢”按钮一会儿。有帮助。

直到下次。

*本文是* [*雨燕依赖注入系列*](https://medium.com/p/365ce5038ef7/edit) *的一部分。*