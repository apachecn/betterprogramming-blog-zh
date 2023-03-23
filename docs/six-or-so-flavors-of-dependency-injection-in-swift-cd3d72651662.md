# Swift 中依赖注入的六种风格

> 原文：<https://betterprogramming.pub/six-or-so-flavors-of-dependency-injection-in-swift-cd3d72651662>

## 依赖注入是每个程序员首先应该学习的技术之一。让我们看看它在 Swift 中是如何工作的(也许还有为什么)

![](img/c9bfc754a4572fda41cf9b4dc8d0005c.png)

[马太·亨利](https://unsplash.com/@matthewhenry?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

我遇到过的一个程序员新手曾经对我说(深深沉思):我以为类越大越好……你知道，这样它就能做更多的事情！

时间久了，新手变得有经验了，大威武班也就失去了吸引力。程序员开始意识到，他们需要一次又一次地返回到相同的代码，为了保持代码维护的有效性，代码应该是可测试的和易于理解的。这就是[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection)派上用场的地方。

注意:这个帖子不被认为是高级的，更适合初级到中级的程序员。然而，即使你是一个经验丰富的工程师，温习基础知识总是有用的。特别是因为我喜欢在我的博客文章中更深入地讨论主要焦点，并且也涉及有用的配套技术。

# 动机

该技术的主要原则是识别一个类的额外职责，该职责可以被提取出来并安装到另一个类上([关注点分离](https://en.wikipedia.org/wiki/Separation_of_concerns))。

让我们看看它是如何发生的。假设我们有这样的东西:

```
**import** UIKit**final** **class** SomeViewController: UIViewController { **override** **func** viewDidLoad() {
    **super.viewDidLoad()**
    positionView(view)
  } **private** **func** positionView(_ view: UIView) {
    *// ...* }}
```

一小段无害的代码。还是没有？嗯，我们有一个隐藏在类的实现细节深处的逻辑。即使现在也很难测试它，如果可能的话。但是简单的重构可以使逻辑易于测试，也便于以后的维护。为了实现这一点，我们需要:

```
**final** **class** PositionCalculator {
  **func** getPositionOfView(
    _ view: UIView, in viewController: UIViewController
  ) -> CGRect {
    **var** rect = CGRect()
    *// ...* **return** rect
  }
}
```

因此这个逻辑处理程序的客户端可以从这个外部对象接收位置:

```
**private** **func** positionView(_ view: UIView) {
  view.frame = PositionCalculator().getPositionOfView(view,
                                                      in: self)
}
```

一场胜利？不完全是。尽管位置计算现在可以独立于其客户端进行测试和维护，但是客户端本身与其依赖关系是紧密耦合的。如果`PositionCalculator`改变了它的依赖关系和初始化过程怎么办？`SomeViewController`也需要更新，也许是以某种复杂的方式。为了避免这种情况，我们可以从外部传递`PositionCalculator`，让`SomeViewController`直接使用它。一般的技术被称为[控制反转](https://en.wikipedia.org/wiki/Inversion_of_control)。依赖注入是实现它的方法之一。

在 Swift 中注入依赖关系有多种方式。让我们一次看一个。

# 1.财产

对于可选的依赖项，此选项很方便。Delegate 是 iOS 世界的一个典型例子:

```
**final** **class** SomeViewController: UIViewController {
  **weak** **var** delegate: SomeViewControllerDelegate?
}
```

依赖关系可以在其客户端生命周期的任何时刻注入:

```
**let** vc = SomeViewController()
vc.delegate = someDelegate
```

不过，这不是我们的选择。在最初的例子中，我们希望`PositionCalculator`始终可用，因为它对于`SomeViewController`的运行至关重要。

# 2.作曲者

对于客户端在注入依赖项之前就已经存在的情况，尽管它只是在注入之后才开始工作，但是这个选项非常方便。从那一刻起，我们不希望依赖消失。[模型-视图-展示者 UI 设计模式](https://medium.com/swlh/my-journey-on-the-ui-design-patterns-in-ios-universe-9ded70ac1b3b)可能是一个很好的例子。你身边有一个演示者，然后在某个时候你给演示者设置一个视图，后者开始真正的工作:

```
**final** **class** SomePresenter { **private** **var** view: SomeViewController? {
    **didSet** {
      **guard** **let** view = view **else** {
        **return**
      }
      presentView(view)
    }
  } **func** setView(_ view: SomeViewController) {
    **self**.view = view
  } **private** **func** presentView(_ view: SomeViewController) {
    *// ...* }}
```

通话地点:

```
**let** presenter = SomePresenter()*// ...***let** view = SomeViewController()
presenter.setView(view)
// After this presenter start its engine and never stops!
```

## 2a。连接

有时这被称为界面注入。更好的是:

```
**protocol** SomePresenter {
  **func** setView(_ view: SomeViewController)
}
```

或者甚至像这样，把某种可注射的东西的意图协议化:

```
**protocol** SomeViewControllerSettable {
  **func** setView(_ view: SomeViewController)
}**protocol** Presenter: SomeViewControllerSettable {
  // Other protocol requirements.
}// Implementation goes without change.
```

# 3.初始化

我们需要`PositionCalculator`在整个`SomeViewController`的生命周期中立即可用。在这种情况下，通过初始化器传递依赖关系的解决方案就行了:

```
**final** **class** SomeViewController: UIViewController { **private** **let** positionCalculator: PositionCalculator **init**(positionCalculator: PositionCalculator) {
    **self**.positionCalculator = positionCalculator
    **super**.init(nibName: nil, bundle: nil)
  } **@available**(*, unavailable)
  **required** init?(coder aDecoder: NSCoder) {
    fatalError("init(coder:) has not been implemented")
  } **override** **func** viewDidLoad() {
    **super**.viewDidLoad()
    positionView(view)
  } **private** **func** positionView(_ view: UIView) {
    view.frame = positionCalculator.getPositionOfView(view,
                                                      in: self)
  }}
```

让我们朝着可测试性和低耦合性再走几步，让我们抽象掉依赖性，这样它可能有不同的实现或者在单元测试中被模仿(客户端类可以保持不变):

```
**protocol** PositionCalculatorProtocol {
  **func** getPositionOfView(
    _ view: UIView, in viewController: UIViewController
  ) -> CGRect
}**final** **class** PositionCalculator: PositionCalculatorProtocol {
  **func** getPositionOfView(
    _ view: UIView, in viewController: UIViewController
  ) -> CGRect {
    **var** rect = CGRect()
    *// ...* **return** rect
  }
}
```

如果只有一个实现，我们总是可以通过为初始化器提供一个默认值来节省一些编码工作:

```
**final** **class** SomeViewController: UIViewController { **private** **let** positionCalculator: PositionCalculator **init**(
    positionCalculator: PositionCalculator = PositionCalculator()
  ) {
    **self**.positionCalculator = positionCalculator
    **super**.init(nibName: nil, bundle: nil)
  } **@available**(*, unavailable)
  **required** **init**?(coder aDecoder: NSCoder) {
    fatalError("init(coder:) has not been implemented")
  }}
```

# 4.容器

又名注册表、解析器或[服务定位器](https://en.wikipedia.org/wiki/Service_locator_pattern)，这种方式适用于一次又一次注入相同依赖项(相同类型、相同实例)的大型项目。根据我的经验，它在 Android 世界中被广泛使用(见 [Dagger](https://github.com/google/dagger) )，在 iOS 项目中不太受欢迎。

其思想是将所有可能的依赖关系存储在一个容器中，而不是单独传递依赖关系(有时通过多个初始化器，从一个类传递到另一个类)，而是注入将提供必要的现场依赖关系的容器。

一个简单的实现可能如下所示:

```
**final** **class** DependencyContainer { **static** **let** shared = DependencyContainer()
  **private** **var** dependencies = [String : Any]() **private** **init**() { } **func** register<Dependency>(dependency: Any,
                            ofType type: Dependency.Type) {
    dependencies["\(type)"] = dependency
  } **func** resolve<Dependency>(
    ofType type: Dependency.Type
  ) -> Dependency? {
    **return** dependencies["\(type)"] **as**? Dependency
  }}
```

在离客户端模块的根更近的地方，注册所有要使用的依赖项:

```
**let** positionCalculator = PositionCalculator()
DependencyContainer.shared.register(
  dependency: positionCalculator,
  ofType: PositionCalculatorProtocol.self
)
```

注射本身现在看起来像这样:

```
**final** **class** SomeViewController: UIViewController {

  **private** **let** positionCalculator: PositionCalculatorProtocol **init**(positionCalculator: PositionCalculatorProtocol
    = DependencyContainer
      .shared
      .resolve(ofType: PositionCalculatorProtocol.self)!) {
    **self**.positionCalculator = positionCalculator
    **super**.init(nibName: nil, bundle: nil)
  } **@available**(*, unavailable)
  **required** **init**?(coder aDecoder: NSCoder) {
    fatalError("init(coder:) has not been implemented")
  }}
```

你有没有注意到一个潜伏的强行解开操作者？是的，这就是代价——少量的运行时不安全性。当然，您可以将这个又小又丑的`!`移动到容器中，但是这并不能解决任何问题——如果您忘记注册一个依赖项，您还是会崩溃:

```
**func** resolve<Dependency>(
  ofType type: Dependency.Type
) -> Dependency {
  **return** dependencies["\(type)"] **as**! Dependency
}
```

以及在 Android 世界中，有一些库可以基于这种方法的思想提供现成的解决方案。比如[解析器](https://github.com/hmlongco/Resolver)和[s 对象](https://github.com/Swinject/Swinject)。

## 4a。属性包装

容器方法的现代风格隐藏在属性包装器之后，这是一个相对较新的 Swift 特性，大致类似于 Java 的注释。下面是一个可能的实现(高度受 [Antoine van der Lee](https://www.avanderlee.com) 在 Swift 中使用最新 Swift 特性 的 [*依赖注入的启发):*](https://www.avanderlee.com/swift/dependency-injection/)

```
**protocol** DependencyContainerKey {
  **associatedtype** Dependency
  **static** **var** value: Dependency { **get** **set** }
}**struct** DependencyContainer { **var** positionCalculator: PositionCalculatorProtocol {
    **get** { Self[PositionCalculatorKey.self] }
    **set** { Self[PositionCalculatorKey.self] = newValue }
  }
  **private** **static** **var** current = DependencyContainer() **static** **subscript**<Dependency>(
    _ keyPath: WritableKeyPath<DependencyContainer, Dependency>
  ) -> Dependency {
    **get** { current[keyPath: keyPath] }
    **set** { current[keyPath: keyPath] = newValue }
  }
  **private** **static** subscript<Key>(
    key: Key.Type
  ) -> Dependency where Key: DependencyContainerKey {
    **get** { key.value }
    **set** { key.value = newValue }
  }}**@propertyWrapper**
**struct** Injected<Dependency> { **var** wrappedValue: Dependency {
    **get** { DependencyContainer[keyPath] }
    **set** { DependencyContainer[keyPath] = newValue }
  }
  **private** **let** keyPath: WritableKeyPath<DependencyContainer,
                                       Dependency>

  **init**(
    _ keyPath: WritableKeyPath<DependencyContainer, Dependency>
  ) {
    **self**.keyPath = keyPath
  }}
```

要向容器添加可注入的依赖项，我们只需添加一个相应的容器键实现和一个属性:

```
**extension** DependencyContainer {
  **var** positionCalculator: PositionCalculatorProtocol {
    **get** { Self[PositionCalculatorKey.self] }
    **set** { Self[PositionCalculatorKey.self] = newValue }
  }
  **private** **struct** PositionCalculatorKey: DependencyContainerKey {
    **static** **var** value: PositionCalculatorProtocol
      = PositionCalculator()
  }
}
```

注入的依赖项如下所示:

```
**final** **class** SomeViewController: UIViewController { **@Injected**(\.positionCalculator)
  **private** **var** positionCalculator: PositionCalculatorProtocol}
```

这种方法的一个好处是运行时安全性。没有强制解包，如果您忘记向容器添加依赖项，代码就不会编译。

顺便说一下，最好将系统`private`的所有副本与`Injected`声明放在同一个文件中。否则，没有什么能阻止你误用容器，比如:

```
**let** positionCalculator = DependencyContainer().positionCalculator
```

# 结论

我的另一个同事，当时职位比我高，曾经教我如何开始编写单元测试。事实是，没有经验的程序员避免单元测试的主要原因之一是他们的类不是真正可测试的。

所以，首先要做的是分解那些类。如果在分解之后，它们仍然是不可测试的，那么分解产生的类——重复直到类最终是可测试的。依赖注入是一种非常有用的技术，它使你的代码可测试，并且通常遵循传说中的[单一责任原则](https://levelup.gitconnected.com/beyond-the-single-responsibility-principle-d4103418e2e2)。