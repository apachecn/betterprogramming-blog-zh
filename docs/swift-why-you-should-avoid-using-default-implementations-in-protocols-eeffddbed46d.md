# Swift:为什么您应该避免在协议中使用默认实现

> 原文：<https://betterprogramming.pub/swift-why-you-should-avoid-using-default-implementations-in-protocols-eeffddbed46d>

## 继承之上的组合、接口分离原则、方法调度和单元测试

![](img/d5b84701041f595339bb1741776b3778.png)

[亚历山大·奈特](https://unsplash.com/@agkdesign?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/laptop?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

因为不使用它们的理由超过了这样做的好处。让我们来看看其中的一些:

*   继承之上的组合和界面分离原则。
*   协议的方法分派。
*   单元测试。

# 协议默认实现

如 [Swift 文档](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html)所述:

> "您可以使用协议扩展为该协议的任何方法或计算属性要求提供默认实现。"

事实上，您不仅可以为协议中定义的方法或计算属性提供默认实现，还可以添加新的实现。

让我们看一个例子:

```
protocol SampleProtocol {
  func foo()
}extension SampleProtocol {
  func foo() {
    print("foo")
  }
  func bar() {
    print("bar")
  }
}class SampleClass: SampleProtocol {}let sample: SampleProtocol = SampleClass()
sample.foo() **// prints "foo"**
sample.bar() **// prints "bar"**
```

# 偏爱合成而非遗传

在我看来，你需要一个方法或计算属性的默认实现的事实是一个 [*代码闻*](https://martinfowler.com/bliki/CodeSmell.html) 。

如果你不熟悉这个术语，代码味道是指代码中的某种东西，可能表明更深层次的问题。

您认为需要使用这种方法可能还有其他原因，但最有可能的原因是为了避免代码重复。如果是这样的话，你将会做一些类似于子类化一个*抽象* *BaseX* 类的事情，这并不是最好的方法。

根据 OOP 原则，为了这个目的，你应该*更喜欢组合而不是继承*。

继承更加严格，避免了在运行时注入或更改具体的实现，破坏了封装，降低了代码的可读性，使测试更加困难，等等。

我在[的另一篇文章](https://medium.com/better-programming/swift-favor-composition-over-inheritance-the-baseviewcontroller-case-f598064bda6)中谈到这个话题。

# 界面分离原理

您需要使用默认实现的另一个原因是您错过了 Objective-C 协议中的可选方法。您可能希望提供一个默认的实现作为替代。

同样，这可能不是最好的方法。接口分离原则是五个坚实的原则之一，并且声明*任何客户端都不应该被迫依赖于它不使用的方法*。

简而言之:如果是这样的话，把你的协议分成几个小的部分。

# 方法分派

如果我们回到本文的第一个例子，并试图覆盖协议的默认实现，让我们看看会发生什么:

```
protocol SampleProtocol {
    func foo()
}
extension SampleProtocol {
    func foo() {
        print("protocol foo")
    }
    func bar() {
        print("protocol bar")
    }
}class SampleClass: SampleProtocol {
    func foo() {
        print("class foo")
    }
    func bar() {
        print("class bar")
    }
}let sample: SampleProtocol = SampleClass()
sample.foo() **// prints "class foo"**
sample.bar() **// prints "protocol bar"**
```

我重复一遍:`Sample.bar()`打印`“protocol bar”`。

如果您或任何阅读您代码的人不熟悉 Swift 中方法分派的工作方式，这是非常违反直觉的。

并且，它也是难以发现的 bug 的来源。

这就是为什么会这样:

`SampleProtocol`定义了两种方法:`foo()` *，*在协议中定义为需求，`bar()`在扩展中定义。

协议要求的方法使用动态分派，它选择在运行时执行的方法实现。

扩展定义的方法使用静态分派，静态分派选择在构建时执行的方法实现。这意味着使用的唯一实现将是扩展的实现。而且，你不能覆盖它。

# 调度优先级和约束

在方法分派问题之后，如果使用约束，还有另一件事需要考虑:

```
protocol SampleProtocol {
    func foo()
}
extension SampleProtocol {
    func foo() {
        print("SampleProtocol")
    }
}protocol BarProtocol {}
extension SampleProtocol **where Self: BarProtocol** {
    func foo() {
        print("BarProtocol")
    }
}class SampleClass: SampleProtocol, **BarProtocol** {}let sample: SampleProtocol = SampleClass()
sample.foo() **// prints "BarProtocol"**
```

您可以使用约束来覆盖默认实现(只要它们是协议所要求的)。并且受约束的默认实现优先于不受约束的实现。

所以优先级应该是:*符合协议的类/结构/枚举- >约束协议扩展- >简单协议扩展*。

同样，您可能已经知道这一点，但是对于阅读、修改或调试您的代码的任何人来说，这肯定是令人困惑的。

# 单元测试

假设您正在为您的代码编写测试，注入依赖项，并使用模拟，您会发现尝试两者都有困难:用默认实现模拟协议并测试那些默认实现。

当您试图用默认实现模仿协议时，您将遇到方法分派问题，因此，您将无法模仿扩展定义的方法:

```
protocol DependencyProtocol {}
extension DependencyProtocol {
    func foo() -> Int {
        return 0
    }
}class SampleClass {
    let dependency: DependencyProtocol
    init(dependency: DependencyProtocol) {
        self.dependency = dependency
    }

 **// You will never be able to mock dependency.foo()**
    func sampleMethod() {
        let dependencyValue = dependency.foo()
        print(dependencyValue)
    }
}
```

此外，您可能会发现很难检测何时何地用默认实现模拟新添加的方法，因为编译器不会抱怨这一点。

您可能最终会在其他类的测试中包含默认的实现，而不是模拟的方法。

测试默认实现不是一个小问题，因为您必须编码并实例化一个符合协议的虚拟类，这样您就可以调用这些方法。

这个伪类必须实现所有那些没有默认实现的协议要求的方法。

```
protocol SampleProtocol {
    func same(input: Int) -> Int
}
extension SampleProtocol {
    func double(input: Int) -> Int {
        return input * input
    }
}**// You need a dummy class so you can call protocol default implementations**
class SampleProtocolDummy: SampleProtocol { **// You need to implement notimplemented-required-methods with dummy code**
    func same(input: Int) -> Int {
        return 0
    }}let sut: SampleProtocol = SampleProtocolDummy()let result = sut.double(input: 2)XCTAssertEqual(expectedResult, result)
```

感谢阅读！如果你喜欢这篇文章，请鼓掌:)