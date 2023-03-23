# 工厂:循环依赖链

> 原文：<https://betterprogramming.pub/factory-circular-dependency-chains-ece39a7d2bc8>

## 如何找到并消除它们

![](img/3205b42dfe6cf3a48f85488ca7eaf4c5.png)

[阿德利·瓦希德](https://unsplash.com/@adliwahid?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/photos/V395x8YyfJE?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

像 [Factory](https://github.com/hmlongco/Factory) 这样的依赖注入系统可以让你的代码更容易编写和测试。但是偶尔会出现问题，我们在 DI 系统中看到的一个更常见的问题与循环依赖有关。

# 什么是循环依赖？

假设 A 需要 B 来构造，B 需要一个 C，但是如果 C 需要 A 会怎么样呢？检查下列类定义。

```
class CircularA {
    @Injected(Container.circularB) var circularB
}

class CircularB {
    @Injected(Container.circularC) var circularC
}

class CircularC {
    @Injected(Container.circularA) var circularA
}
```

试图构造`CircularA`的实例将导致无限循环。

为什么？A 的注入属性包装需要一个 B 来构造 A，好吧。我们做一个吧。但是 B 的包装器需要一个 C，不注入 A 就无法完成，而 A 又需要一个 B…等等。无限地。

这是一个循环依赖链。

不幸的是，当这段代码编译并运行时，要打破这个循环已经太晚了。我们已经在程序中有效地编写了一个无限循环。

这就引出了两个问题:

1.  我们如何找出问题所在？
2.  我们如何解决它？

# 我们如何找出问题所在？

先从发现问题开始。虽然检查上面的代码并找出错误很容易，但是在拥有数百甚至数千个依赖关系的大型系统中，找到有问题的依赖关系链可能会很困难。

如前所述，当这些代码被编译并运行时，做任何事情都为时已晚。Swift 要求我们提供一个对象的实例，我们*可以*创建该对象的所有依赖项……但是该对象的属性初始化器要求我们首先提供另一个对象的实例，这将我们带到链的下游。华盛顿州冲洗。重复一遍。

此时，应用程序所能做的就是退出。

但是有了 Factory，我们可以优雅地死去，并在这个过程中抛弃显示问题所在的依赖链。

```
2022-12-23 14:57:23.512032-0600 FactoryDemo[47546:6946786] Factory/Factory.swift:393: 
Fatal error: circular dependency chain - CircularA > CircularB > CircularC > CircularA
```

在调试模式下运行时，工厂现在会跟踪依赖关系链，并在检测到循环依赖关系链时发出致命错误。我们不需要通过检查调用堆栈来找出问题所在。工厂会告诉我们。

`CircularA > CircularB > CircularC > CircularA`

我们试图制造一个 A，这是制造一个 B，这是制造一个 C…这是循环，并试图再次制造一个 A。

有了上面的信息，我们应该能够找到问题并解决它。

# 修复问题

我们可以让工厂自己解决问题。考虑对`CircularC`的以下更改和对`circularA`的新工厂定义。

```
class CircularC {
    weak var circularA: CircularA?
}

extension Container {
    static var circularA = Factory<CircularA> {
        let a = CircularA()
        a.circularB.circularC.circularA = a
        return a
    }
}
```

这种机制通常用于修复严格的父子关系，也可以用在这里……但是很臭。如果有人试图自己制造一个`CircularB`会发生什么？

让我们看看我们是否能做得更好。

## 惰性装载

这个问题的经典解决方案是延迟加载。

只需将 CircularC 的注入包装改为`LazyInjected`，或者更好的是`WeakLazyInjected`，以避免保留周期。

```
class CircularC {
    @WeakLazyInjected(Container.optionalA) var circularA
}
```

因为`CircularA`的这个实例直到被请求时才会被提供，所以在对象被构造之后*，循环依赖链已经被打破。*

注意，如果我们真的想让 C 引用原来的 A，我们可能要把原来的工厂范围`circularA`改为 `.shared`，并把`optionalA`指向那个工厂。

不管怎样，这仍然不是最佳的。

## 重构

从架构的角度来看，如果 A 依赖于 B，那么我们通常希望 A 能够看到 B，但是 B 应该不知道 A 的存在。

可见性沿着链条向下流动。

因此，更好的解决方案可能需要找到`CircularA`和`CircularC`所依赖的功能，并将其分解到它们都可以包含的第三个*对象中。*

像这样的循环依赖通常违反了单一责任原则，应该首先避免。

# 引擎盖下。

所以工厂做循环依赖链检测，但是它是怎么做的呢？

在工厂内部，有一个单一的函数可以解决特定的依赖关系。它检查工厂是否被新的注册覆盖了，它还检查对象是否被缓存在不同的作用域中。

```
func resolve(_ params: P) -> T {
    let _ = Container.autoRegistrationCheck
    let currentFactory: (P) -> T = (SharedContainer.Registrations.factory(for: id) as? TypedFactory<P, T>)?.factory ?? factory
    let instance: T = scope?.resolve(id: id, factory: { currentFactory(params) }) ?? currentFactory(params)
    SharedContainer.Decorator.decorate?(instance)
    return instance
}
```

所有的东西都流经这个地方。

为了提供循环依赖检测，代码已经扩展，现在看起来像这样。

```
func resolve(_ params: P) -> T {
    let _ = Container.autoRegistrationCheck

    let currentFactory: (P) -> T = (SharedContainer.Registrations.factory(for: id) as? TypedFactory<P, T>)?.factory ?? factory

    #if DEBUG
    defer { dependencyChain.removeLast(); dependencyLock.unlock() }
    dependencyLock.lock()
    let typeComponents = String(describing: T.self).components(separatedBy: CharacterSet(charactersIn: "<>"))
    let typeName = typeComponents.count > 1 ? typeComponents[1] : typeComponents[0]
    let typeIndex = dependencyChain.firstIndex(where: { $0 == typeName })
    dependencyChain.append(typeName)
    if let index = typeIndex {
        fatalError("circular dependency chain - \(dependencyChain[index...].joined(separator: " > "))")
    }
    #endif

    let instance: T = scope?.resolve(id: id, factory: { currentFactory(params) }) ?? currentFactory(params)

    SharedContainer.Decorator.decorate?(instance)

    return instance
}
```

所有的原始代码都在那里，但是现在有一个`DEBUG`部分，它通过将当前类名推送到堆栈(数组)上，创建所需的实例，然后将该名称移除到堆栈上来跟踪当前类名。

这个函数是可重入的，所以如果在创建 A 的实例时，它试图创建 B，它将再次调用这个函数。同样的情况，当 B 试图做一个 C，当 C 再次试图做一个 a。

但是每次我们想创建一个类型的新实例时，我们都要检查该类型是否已经在堆栈中。

如果是，那么我们有一个循环依赖链，我们需要中止，在这个过程中转储链。

有一点很重要，工厂类型可能是可选的，所以需要一点额外的代码来从看起来像" Optional < CircularA >"的类型字符串中提取基本类型名。

由于 Factory 是线程安全的，我们在检测代码周围做了一些额外的锁定，以防止数组损坏。

# 完成块

由于社区的支持，Factory 仍在发展、成长和变得更好。我感谢你的帮助，评论，是的，甚至是错误报告。

有什么要说的吗？一如既往，请在下面留下评论或类似内容。

*本文是* [*雨燕依赖注入系列*](https://medium.com/p/365ce5038ef7/edit) *的一部分。*