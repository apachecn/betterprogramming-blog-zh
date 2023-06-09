# Swift:单元测试工厂

> 原文：<https://betterprogramming.pub/swift-unit-testing-factory-40043b361f55>

## 创建一个图书馆？确保它是正确的。而且会一直保持下去。

![](img/07f5e450103c696a8ed9d05891c5b52d.png)

[烈斗·富尔](https://unsplash.com/@rf999?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/measuring?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

最近我发布了[工厂](https://github.com/hmlongco/Factory)，这是我为 Swift 开发的最新依赖注入系统。Factory 是作为我早期作品 [Resolver](https://github.com/hmlongco/Resolver) 的替代品而创建的，它试图将我从那个项目中学到的一切都拿出来，看看我是否能做得更好。

长话短说。我可以，我在以前的文章中写过为什么我要这么做，以及 [Factory 如何比](/factory-swift-dependency-injection-14da9b2b5d09)更好。

但是发布一个供大众消费和使用的库会带来一些义务。一是你计划支持它，而且你会长期参与其中。

但是最重要的事情，当然，是你的图书馆在工作，它做了它应该做的事情。

这就是测试发挥作用的地方。

# 关键代码

有些代码比其他代码更关键，我碰巧认为依赖注入代码属于后一类。它是构建应用程序的基础和基石，如果它不能在需要的时候交付需要的东西，那么应用程序很可能也会失败。

与解析器不同，工厂被编写为编译时安全的。提供给定类或服务的工厂必须存在，否则代码甚至无法编译。因此，它应该为我们的应用程序提供一个坚实的基础。

Factory 的使用和理解也相当简单，这也是它的设计。但是，要确保引擎盖下的一切都像广告宣传的那样工作，还有一些错综复杂的事情要做。

首先，工厂是线程安全的。跨线程访问给定的工厂需要是安全的，并且不会使应用程序崩溃。Factory 还提供了各种现成的作用域缓存机制，这些机制也需要是线程安全的，并按预期工作。

那么，我们如何确保这一切发生呢？

# 信任。然后验证。

当我们写代码时，我们倾向于对它有一定程度的信任。尤其是当它编译和运行时，以及当它看起来像预期的那样工作时。

但是我们的信任有保证吗？

这就是测试发挥作用的地方。我们编写单元测试是为了对我们的代码进行压力测试，并且*验证*它是否按预期工作。我们测试边缘情况和错误情况，希望没有用户会在产品应用中看到。

我们这样做是因为，在你进行验证之前，你只是假设一切都会按计划进行。你知道他们说当你假设。

如前所述，工厂被设计成线程安全的。因此，为了实现这一点，我在工厂的各个关键点实现了锁定。我运行了代码，它成功了…但是它真的成功了吗？

确定这一点的唯一方法是编写测试。于是我照做了。我编写了代码来跨多个线程执行数千个同步解析和注册，然后检查以确保我的代码正常工作，没有死锁和崩溃。

剧透一下，我第一次运行这些测试时，它就崩溃了。我错过了一个缓存锁，它回来咬我。

*但是我在你之前发现了这个漏洞。*并修复。

# 领域

工厂也考虑到了范围，当我们想要使用一个已经创建的给定对象或类的同一个实例时。如果你有自己的单例，你就创建了一个作用域。工厂有几个内置的。

给我带来无穷麻烦的一个特别有问题的作用域是*共享的*作用域。共享范围基本上是弱缓存。一个对象的给定实例将保持缓存，只要外界有人维护对它的强引用。但是一旦释放了最后一个引用，就需要从缓存中释放该对象，并在下次请求时提供一个新的实例。

它们很方便，尤其是在 SwiftUI 环境中。

你可能会说，很容易。只要保持一个*弱*引用就可以了。但是，如果一个对象是一个类，那么只能定义和保持对它的弱引用。

这反过来要求我们在代码中确定我们被要求缓存的对象是否是一个类。这比人们想象的要难搞得多，尤其是在通用结构内部和与通用缓存机制耦合时。

如果我们的工厂被设计为返回一个协议而不是一个类呢？这是我们首先进行依赖注入的主要原因之一，那么我们如何检查呢？如果类型是可选的呢？如果它是一个以值类型为基础的可选协议呢？

所有这些条件、组合和排列都需要检查。我的共享范围返回共享类型吗？它释放了吗？它的行为是否符合预期？

剧透警告——它花了一段时间才到达那里。但是我现在很有信心，共享作用域对于引用类型、值类型、协议和选项都可以像预期的那样工作。

因为我测试了它们。

# 变化

很明显，我们想确保一切按预期运行。

但是这里还有另一个关键点，因为我们也想确保它会继续按预期工作。

当调试我的共享作用域的问题时，我经常在这里或那里做一些调整，以试图简化我的代码。然后我运行当前的一组测试，却发现我看似微小的改变破坏了一个测试。或者两个。或者五个。

但是一旦你为你的代码建立了一套好的测试用例，你就可以做一些改变…然后运行测试。

当它们通过时，您可以松一口气，并放心您可能没有破坏代码中的任何东西。

大概吧。

# **bug**

有一个古老的编程笑话，列出了几个公理:

1.  所有程序都包含至少一个 bug。
2.  您总是可以从给定的程序中删除至少一行代码。

如果遵循其明显的、不合逻辑的结论，这意味着任何程序都可以简化为一行不工作的代码。

不客气

不管怎样，这是生活的现实。虫子是存在的。当然，我们会尽量减少它们。好的架构，加上我们的测试，有助于确保代码中有更少的地方让他们隐藏。

但是小傻瓜总是在那里。等待中。

当一个错误被报告、发现并修复后，我们添加一个覆盖该错误的测试用例是很关键的，这样:a)我们知道我们修复了它；第二，这种事不会再发生了。

工厂维护一个文件，FactoryDefectTests.swift，它专门用于这类事情。

# 密码

这是一篇编程文章，如果你看不到一些代码，你可能会失望，所以这里有一个共享范围测试。

```
func testExplicitProtocolSharedScope() throws {
    var service1: MyServiceType? =
         Container.sharedExplicitProtocol()
    var service2: MyServiceType? =
        Container.sharedExplicitProtocol()
    XCTAssertNotNil(service1)
    XCTAssertNotNil(service2)
    // Shared cached item ids should match
    XCTAssertTrue(service1?.id == service2?.id)
    let oldID = service1?.id
    XCTAssertNotNil(oldID)
    service1 = nil
    service2 = nil
    let service3: MyServiceType? =
        Container.sharedExplicitProtocol()
    XCTAssertNotNil(service3)
    // Shared instance should have released so new
    // and old ids should not match
    XCTAssertTrue(oldID != service3?.id)
}
```

我们从工厂获得一个共享的基于协议的服务，然后我们要求另一个。因为它是共享的，所以两个实例的 id 应该匹配，告诉我们这是同一个实例。如果我们释放两个实例并重试，缓存的实例应该已经被释放，返回的实例应该是新的，其新 id 不同于。

如前所述，Factory 包含了相当多的这类工具，所以如果你对实际的细节感兴趣，可以去看看这个库。

# 完成块

今天就到这里吧。我所说的大部分都是众所周知的，这种单元测试方法对大多数开发人员来说应该是过时的了。

但是你会惊讶有多少次我遍历一个新的代码库，却找不到任何关于应用或服务中关键组件或一些关键功能的测试。

因此，我认为重申其中的一些原则，并结合实际项目中的一些例子可能是有益的。

因此，如果您正在编写供开发人员社区外部使用的库，请尝试遵循这些准则。这将使您的生活变得更加轻松，您的开发伙伴也会因此而感谢您。

我也一样，我应该下载你的回购。

有问题或意见吗？把它留在下面。

下次见。

*本文是* [*雨燕依赖注入系列*](https://medium.com/p/365ce5038ef7/edit) *的一部分。*