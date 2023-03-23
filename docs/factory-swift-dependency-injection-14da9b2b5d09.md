# 工厂:快速依赖注入

> 原文：<https://betterprogramming.pub/factory-swift-dependency-injection-14da9b2b5d09>

## 解决老问题的新方法。

![](img/c391ce98e6ead6052815147a21c6c282.png)

## 为什么要做新的东西？

我写过的第一个依赖注入系统是[解析器](https://github.com/hmlongco/Resolver)。这个开源项目虽然非常强大，并且仍然在许多应用程序中使用，但是也有一些缺点。

1.  解析器要求预先注册所有服务工厂。
2.  解析器使用类型推断来动态地从容器中查找并返回注册的服务。

第一个问题相对较小。虽然预注册可能会导致应用程序启动时的性能下降，但实际上这个过程通常很快，通常不会引起注意。

不，这是第二个问题。

如果我们试图解析给定的类型，并且没有找到匹配的注册，那么找不到匹配的类型会导致应用程序崩溃。在现实生活中，这并不是一个真正的问题，因为当你第一次运行单元测试或者当你运行应用程序来查看你的最新特性是否工作时，这样的事情往往会被注意到并且很快被修复。

但是…我们能做得更好吗？这个问题[引导我寻找编译时类型安全](https://medium.com/geekculture/i-hate-swift-i-love-swift-318171a0f0df)。其他几个项目也试图解决这个问题，但是我不想在构建过程中添加源代码扫描和生成步骤，也不想放弃基于运行时的系统中固有的大量控制和灵活性。

我还想要一些简单、快速、干净、易于使用的东西。

我可以把我的蛋糕也吃掉吗？

让我们找出答案。

> 注意:本文假设您熟悉依赖注入及其概念。如果没有，你可能想读一读我为 Resolver 写的[关于依赖注入的温和方法](https://github.com/hmlongco/Resolver/blob/master/Documentation/Introduction.md)指南。

## **工厂**

工厂受到 SwiftUI 的强烈影响，在我看来非常适合在那个环境中使用。工厂是…

*   **安全**:工厂是编译时安全的；给定类型* *的工厂必须* *存在，否则代码根本无法编译。
*   **灵活:**很容易在运行时覆盖依赖关系，并在 SwiftUI 预览中使用。
*   **强大:**像解析器一样，工厂支持应用程序、缓存、共享和自定义范围、客户容器、参数、装饰器等等。
*   **轻量级:**所有工厂都是精简的，不到 300 行代码。(不到分解器大小的三分之一。)
*   **Performant:** 您的绝大多数服务都不需要设置时间，解决速度极快，并且不需要编译时脚本或构建阶段。
*   **简洁:**定义一个注册通常只需要一行代码。
*   **Tested** :单元测试确保注册、分辨率和作用域的正确操作。
*   **免费:**在麻省理工学院的许可下，Factory 是免费和开源的。

听起来好得难以置信？让我们来看看。

## 简单的例子

大多数基于容器的依赖注入系统要求您以某种方式定义给定的服务类型可用于注入，并且许多系统要求某种工厂或机制，在需要时提供服务的新实例。

工厂也不例外。这里有一个简单的依赖关系注册。

```
extension Container {
    static let myService = Factory { MyService() as MyServiceType }
}
```

不像 Resolver 经常需要定义过多的注册函数，或者 SwiftUI，定义一个新的环境变量需要创建一个新的 EnvironmentKey 并添加额外的 getters 和 setters，这里我们只需向默认容器添加一个新的 static `Factory`。当需要我们对象的一个实例时，工厂闭包被求值并返回我们依赖关系的一个实例。就是这样。

在需要的地方使用服务同样简单。这里有一种方法。

```
class ContentViewModel: ObservableObject {
    @Injected(Container.myService) private var myService
    ...
}
```

这里，我们的视图模型使用一个`@Injected`属性包装器来请求所需的依赖关系。类似于 SwiftUI 中的`@EnvironmentObject`,我们为属性包装器提供对所需类型工厂的引用，其余的由它来处理。

这就是核心机制。为了使用属性包装器，你必须定义一个工厂。当被询问时，工厂*必须*返回想要的类型。任何一项都做不好，代码将无法编译。因此，工厂是编译时安全的。

## 什么是工厂？

`Factory`是一个轻量级结构，它管理给定的依赖关系。由于静态变量的惰性，工厂直到第一次被引用时才会被实例化。

当一个工厂被评估时，它提供一个期望依赖的实例。注意，也可以绕过属性包装，直接调用工厂。

```
class ContentViewModel: ObservableObject {
    // dependencies
    private let myService = Container.myService()
    private let eventLogger = Container.eventLogger()
    ...
}
```

你可以直接引用容器或者使用属性包装器，但是为了清楚起见，我建议把一个给定对象的所有依赖项都放在类的顶部。哦，把它们也标记为私人的。

## 嘲笑和测试

检查上面的代码，有人可能想知道为什么我们要这么麻烦？为什么不直接说`let myService = MyService()`就完事了呢？

或者保留容器的想法，但是写一些类似这样的东西…

```
extension Container {
    static var myService: MyServiceType { MyService() }
}
```

使用基于容器的依赖注入系统的主要好处是，我们能够根据需要改变系统的行为，如果你硬编码依赖，我们就不能这样做。

考虑以下代码:

```
struct ContentView: View {
    @StateObject var model = ContentViewModel1()
    var body: some View {
        Text(model.text())
            .padding()
    }
}
```

我们的`ContentView`使用我们的视图模型，它被分配给一个`StateObject`。太好了。但是现在我们想预览我们的代码。我们如何改变`ContentViewModel`的行为，使得它的`MyService`依赖在开发过程中不进行实时 API 调用？

很简单。只是用一个模仿来代替`MyService`。

```
struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        let _ = Container.myService.register { MockService2() }
        ContentView()
    }
}
```

请注意预览代码中的那一行，我们回到了容器，并在工厂中注册了一个新的闭包。该功能*覆盖*默认的工厂关闭。

现在，当我们的预览显示时,`ContentView`创建一个`ContentViewModel`,它又依赖于使用`Injected`属性包装器的`myService`。但是当属性包装器调用工厂来请求一个`MyServiceType`实例时，它现在接收一个`MockService2`实例，而不是最初定义的`MyService`实例。

我们可以这样做，因为我们最初将`myService`工厂的结果转换为协议`MyServiceType`。因为`MockService2`符合`MyServiceType`协议，所以我们很好，我们可以用一个替换另一个。

```
extension Container {
    static let myService = Factory { MyService() as MyServiceType }
}
```

如果没有专门化，工厂的类型被推断为工厂闭包返回的类型。您也可以通过专门化通用工厂获得相同的结果，如下所示。两者相当。

```
extension Container {
    static let myService = Factory<MyServiceType> { MyService() }
}
```

另外要注意的一点是，我们注册块的结果也必须符合原厂的类型。如果不是这样，如果你试图返回其他东西，Swift 会抱怨并给你一个错误。简而言之，这也是编译时安全的。

如果我们有几个一直在使用的 mocks，我们也可以在容器中添加一个设置函数来简化这个过程。

```
extension Container {
    static func setupMocks() {
        myService.register { MockServiceN(4) }
        sharedService.register { MockService2() }
    }
}struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        let _ = Container.setupMocks()
        ContentView()
    }
}
```

这是一个强大的概念，它让我们深入到依赖链中，并根据需要改变系统的行为。

但是工厂还有更多锦囊妙计。

## 领域

如果您以前使用过 Resolver 或其他依赖注入系统，那么您可能已经体验过作用域的好处和威力。

如果不是，这个概念很容易理解:

> 一个对象的实例应该存在多久？

毫无疑问，在你职业生涯的某个阶段，你已经在应用程序中创造了一个 singleton。这是一个范围的例子。应用程序中的所有方法和函数创建、使用和共享一个实例。

这可以在工厂中简单地通过添加一个范围属性来完成。

```
extension Container {
    static let someService = Factory(scope: .singleton) { 
        SomeService() 
    }
}
```

现在，每当有人请求一个`someService`的实例时，他们都会得到和其他人一样的对象实例。

除非改动，否则默认范围是`unique`；每当工厂被请求一个对象的实例时，它将获得该对象的一个新实例。

其他常见的范围有`cached`和`shared`。缓存项会一直保存到缓存被重置，而共享项只要有人持有对它们的强引用就会一直存在。当最后一个引用消失时，弱持有的共享引用也消失。

您也可以添加您自己的特殊用途的范围。

```
extension Container.Scope {
    static var session = Cached()
}extension Container {
    static let authentication = Factory(scope: .session) { 
        Authentication()
    }
}
```

一旦创建好了，`Authentication`的单个实例将被提供给任何需要的人……直到会话范围被重置，可能是用户注销。

```
func logout() {
    Container.Scope.session.reset()
    ...
}
```

瞄准镜是你武器库中的强大工具。使用它们。

## 构造函数注入

有时，我们可能更喜欢(或需要)使用一种称为*构造函数注入*的技术，在初始化时向对象提供依赖关系。

这在工厂里很容易做到。这里我们有一个依赖于前面定义的`MyServiceType`实例的服务。

```
extension Container {
    static let constructedService = Factory {
       MyConstructedService(service: myService())
    }
}
```

容器中的所有工厂对容器中的其他工厂都是可见的。只需调用所需的工厂作为函数，就会提供依赖关系。

## 定制容器

在大型项目中，您可能希望将工厂分隔成额外的较小容器。

```
class OrderContainer: SharedContainer {
    static let optionalService = Factory<SimpleService?> { 
        nil
    }
    static let constructedService = Factory { 
        MyConstructedService(service: myServiceType()) 
    }
    static let additionalService = Factory(scope: .session) { 
        SimpleService()
    }
}
```

只需定义一个从`SharedContainer`派生的新容器，并在那里添加您的工厂。您可以拥有任意多的容器，甚至可以从您自己的容器中派生出其他容器。

虽然容器*树*使得依赖关系解析更加容易，但是不要忘记，如果需要，您可以通过指定完整的 container.factory 路径来跨越容器。

```
class PaymentsContainer: SharedContainer {
    static let anotherService = Factory {
       AnotherService(OrderContainer.optionalService())
    }
}
```

## 共享容器

注意，你也可以将自己的工厂添加到`SharedContainer`中。在那里添加的任何内容都可以在系统中的每个容器上看到。

```
extension SharedContainer {
    static let api = Factory<APIServiceType> { APIService() }
}
```

## 单元测试

工厂还增加了一些条款，使单元测试更容易。在您的单元测试设置功能中，您可以* *推送* *注册系统的当前状态，然后注册并测试您想要的任何东西。

然后在你的拆卸函数中，简单地* *弹出* *你的修改，将一切恢复到运行测试套件之前的状态。

```
final class FactoryCoreTests: XCTestCase { override func setUp() {
        super.setUp()
        Container.Registrations.push()
     } override func tearDown() {
        super.tearDown()
        Container.Registrations.pop()
    }

    ...
}
```

## 重置

您也可以重置注册以恢复原始工厂关闭。或者，如果需要，您可以用一个命令将一切都重置回原点。

```
Container.myService.reset() // single
Container.Registrations.reset() // all
```

## 开放源码

如上所述，Factory 在 MIT 许可下是免费和开源的。

在 GitHub 上的[工厂存储库下有源代码、一个小的演示应用和单元测试。](https://github.com/hmlongco/Factory)

## 分解器

工厂可能标志着解析器的终结。我从那个项目中学到了很多，它甚至为我赢得了来自谷歌的开源同行奖金[。(我一直觉得一个 iOS 开发者获得谷歌的奖项有点奇怪，但你知道了。)](https://opensource.googleblog.com/2021/09/announcing-latest-open-source-peer-bonus-winners.html)

但是工厂更小、更快、更干净，总之是比分解器更好的解决方案。

## 完成块

原来如此。一如既往，请在下面的评论中让我知道你的想法。

*本文是* [*雨燕依赖注入系列*](https://medium.com/p/365ce5038ef7/edit) *的一部分。*