# 我们用 Rust 重写了我们的项目…速度快了将近 40 倍

> 原文：<https://betterprogramming.pub/40x-faster-we-rewrote-our-project-with-rust-120b006c6abe>

## 以下是我们选择 Rust 的原因，我们遇到的障碍，以及重写收入比率

![](img/3fbaf345d3e89c97420ed9b6c64235ec.png)

戈兰·艾沃斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Rust 已经悄悄地成为最流行的编程语言之一。作为一种新兴的系统语言，Rust 有很多特点，比如内存安全机制、接近 C/C++的性能优势、优秀的开发者社区、工具链和 ide。

本文将介绍我们使用 Rust 重写项目并逐步在生产环境中实现它的过程——以及选择 Rust 的原因、遇到的问题和结果。

我们正在利用 Rust 开发的项目叫做 [KCL](https://github.com/KusionStack/KCLVM) 。(KCL)是一种开源的基于约束的记录和函数式语言。它通过成熟的编程语言堆栈改进了复杂配置的编写。它致力于围绕配置、更简单的逻辑编写、快速自动化和良好的生态扩展性来构建更好的模块化、可伸缩性和稳定性。更多具体的 KCL 使用场景，请访问 [KCL 网站](https://kcl-lang.github.io/)。

KCL 之前是用 Python 写的。考虑到用户体验、性能和稳定性，我们决定在 Rust 中重写它，并获得了以下好处:

*   由于 Rust 强大的编译检查和错误处理，错误更少。
*   语言端到端编译和执行性能提升 66%。
*   语言前端解析器的性能提升了 20 倍。
*   语言语义分析器的性能提高了 40 倍。
*   语言编译器在编译期间的平均内存使用量是原始 Python 版本的一半。

# 我们遇到的问题[](http://localhost:3000/blog/2022-kcl-rewrite-with-rust#what-problems-have-we-encountered)

编译器、构建系统和运行时使用 Rust 在相同类型的项目中做类似的事情——像 [deno](https://github.com/denoland/deno) 、 [swc](https://github.com/swc-project/swc) 、 [turbopack](https://github.com/vercel/turbo) 、 [rustc](https://github.com/rust-lang/rust) 。我们使用 Rust 来构建编译器的前端、中间和运行时——但我们直到大约一年前才这样做。

一年前，我们使用 Python 构建了 KCL 编译器的整个实现。虽然最初运行良好，但由于 Python 易于使用，团队的研发效率也非常高。然而，随着代码的扩展和工程师数量的增加，代码维护变得更加困难。

我们被迫在项目中编写 Python 类型的注释，采用了更严格的 lint 工具——代码测试行覆盖率也达到了 90%以上——但还是有很多运行时错误，比如 Python None 空对象、找不到属性等等。重构 Python 代码一定要慎重，严重影响用户体验。

此外，当 KCL 用户构成开发人员的大多数时，编程语言或编译器内部实现中的任何错误都变得无法容忍，从而导致影响我们用户体验的问题。用 Python 编写的程序起步较慢，它们的性能需要满足我们的自动化系统所需的在线编译和执行的效率需求。在我们的场景中，用户需要能够在修改 KCL 代码后快速显示编译结果。用 Python 编写的编译器不能有效地满足这些要求。

# 为什么生锈？

我们选择 Rust 的原因如下:

*   我们使用 Python、Go 和 Rust 实现了一个简单的编程语言栈虚拟机，并比较了它们的性能。在这种情况下，Go 和 Rust 具有相似的性能，而 Python 表现出显著的性能差距。经过慎重考虑，我们选择了铁锈。三种语言实现的栈虚拟机代码详情在这里:[https://github.com/Peefy/StackMachine](https://github.com/Peefy/StackMachine)。
*   越来越多的编程语言的编译器或运行时，尤其是前端基础设施项目，是使用 Rust 编写或重构的。此外，Rust 还出现在基础设施、数据库、搜索引擎、网络、云原生、UI、嵌入式等领域。至少，实现编程语言的可行性和稳定性得到了验证。
*   考虑到后续项目开发会涉及到区块链和智能合约的方向，而且社区里大量的区块链和智能合约项目都是 Rust 写的。
*   通过 Rust 可以实现更好的性能和稳定性，使系统更容易维护，更健壮。同时，C APIs 可以通过 FFI 公开，用于多语言使用和扩展，便于生态扩展和集成。
*   铁锈以友好的方式支持 WASM。Rust 在社区中建立了大量的 WASM 生态系统。KCL 语言和编译器可以在 Rust 的帮助下编译成 WASM，并在浏览器中运行。

基于以上原因，我们选择了 Rust 而不是 Go。在整个重写过程中，我们发现 Rust 的综合素质真的很优秀(高性能，足够抽象)。虽然在一些语言特性上有一些代价，特别是生命周期，但在生态上可以更丰富。

# 使用铁锈的困难

虽然我们决定用 Rust 重写整个 KCL 项目，但大多数团队成员都没有用 Rust 编写某个项目的经验，我只学过[Rust 编程语言](https://doc.rust-lang.org/book/)。依稀记得在了解到`Rc`、`RefCell`等智能指针的时候就放弃了。当时没想到 Rust 里会有类似 C++的东西。

使用 Rust 的风险主要是语言学习的成本，在各种博客中也确实有提及。因为 KCL 项目的整体架构并没有太大的变化，而且一些模块设计和代码编写已经针对 Rust 进行了优化，所以整个重写都是在边学边练的过程中进行的。

刚开始用 Rust 写整个项目的时候，我们花了很多时间在知识查询、编译、调试上。但是随着项目的推进，我们在使用 Rust 的体验中遇到的困难主要是心智改造和开发效率。

# 精神转换[](http://localhost:3000/blog/2022-kcl-rewrite-with-rust#mental-transformation)

首先，Rust 的语法和语义很好地吸收和整合了函数式程序中与类型系统相关的概念，比如抽象代数类型(ADT)。

此外，Rust 中没有与“继承”相关的概念。如果你需要帮助理解它，即使是普通的结构定义在 Rust 中也可能比在其他语言中花费更多的时间。例如，以下 Python 代码在 Rust 中将有不同的定义:

*   计算机编程语言

```
from dataclasses import dataclass

class KCLObject:
    pass

@dataclass
class KCLIntObject(KCLObject):
    value: int

@dataclass
class KCLFloatObject(KCLObject):
    value: float
```

*   锈

```
enum KCLObject {
    Int(u64),
    Float(f64),
}
```

当然，更多的时间花在了对抗 Rust 编译器本身的错误报告上。例如，Rust 编译器经常会导致开发人员在借用检查错误时“碰壁”。特别是对于 KCL 编译器，其核心结构是抽象语法树(AST)，一种递归嵌套的树结构。

在 Rust 中有时很难考虑变量可变性和借用检查之间的关系，就像 KCL 编译器中定义的作用域结构`Scope`，对于循环引用的场景，在大量使用 Rust 中常用的智能指针结构如`Rc`、`RefCell`、`Weak`的同时，用来显示需要注意的数据的相互依赖关系。

```
/// A Scope maintains a set of objects and links to its containing
/// (parent) and contained (children) scopes. Objects may be inserted
/// and looked up by name. The zero value for Scope is a ready-to-use
/// empty scope.
#[derive(Clone, Debug)]
pub struct Scope {
    /// The parent scope.
    pub parent: Option<Weak<RefCell<Scope>>>,
    /// The child scope list.
    pub children: Vec<Rc<RefCell<Scope>>>,
    /// The scope object mapping with its name.
    pub elems: IndexMap<String, Rc<RefCell<ScopeObject>>>,
    /// The scope start position.
    pub start: Position,
    /// The scope end position.
    pub end: Position,
    /// The scope kind.
    pub kind: ScopeKind,
}
```

# 开发效率[](http://localhost:3000/blog/2022-kcl-rewrite-with-rust#development-efficiency)

锈的发展效率可以用“先抑后扬”来形容。在一个手写项目的初期，如果团队成员没有接触过函数式编程和相关的编程习惯，开发速度会明显慢于 Python、Go、Java 等语言。

但是，一旦他们熟悉了 Rust 标准库的常用方法和最佳实践，以及 Rust 编译器的常见错误修改，开发效率将会大大提高，他们可以原生地编写出高质量、安全、高效的代码。

例如，我遇到了一个 Rust lifetime 错误，如下面的代码所示。排查了很久，发现寿命不匹配是忘记标注寿命参数造成的。

此外，Rust 的生命周期与类型系统、范围、所有权和借用检查等概念相关联，导致理解的高成本和复杂性。错误报告信息通常不像类型错误那样明显。

生存期不匹配错误报告信息有时不灵活，这可能会导致高成本的故障排除。当然，在更熟悉相关概念后，效率会提高。

```
struct Data<'a> {
    b: &'a u8,
}

// func2 omit lifecycle parameters, and func2 does not.
// The lifecycle of func2 will be deduced as '_ by the Rust compiler by default,
// which may lead to lifetime mismatch error.
impl<'a> Data<'a> {
    fn func1(&self) -> Data<'a> {Data { b: &0 }}
    fn func2(&self) -> Data {Data { b: &0 }}
}
```

# 使用 Rust [重写收入比率](http://localhost:3000/blog/2022-kcl-rewrite-with-rust#rewrite-revenue-ratio-using-rust)

在几个团队成员花了几个月的时间使用 Rust 完全重写并稳定地投入生产环境几个月后，我们回顾了整个过程，觉得非常有收获。

从技术角度来说，重写过程训练我们快速学习新的编程语言和编程知识，并付诸实践。整个重写过程让我们反思了 KCL 编译器的不合理设计，并对其进行了修改。对于一门编程语言来说，这是一个长周期的项目。我们了解到，编译器系统更加稳定和安全，代码清晰，错误更少，性能更好。

虽然不是所有模块都能获得 40 倍的性能(因为有些模块的性能瓶颈，比如 KCL 运行时，就是内存深度复制操作)，但我觉得还是值得的。而当 Rust 使用到一定时期，心智和开发效率就不再是限制因素了。

# 结论[](http://localhost:3000/blog/2022-kcl-rewrite-with-rust#conclusion)

使用 Rust 重写项目的最重要的结果不仅仅是我学会了一种新的编程语言，或者 Rust 是一种非常流行的语言，允许我们编写花哨的代码。相反，使用 Rust 使 KCL 语言和编译器更加稳定，消除了启动速度和自动化效率问题，并提高了 KCL 在类似领域中的性能。这些好处都归功于 Rust 的 no-GC、高性能、更好的错误处理、内存管理、零抽象和其他特性。简而言之，用户是主要受益者。

最后，如果您喜欢 KCL 项目，想将 KCL 用于您自己的场景，或者想使用 Rust 参与一个开源项目，欢迎您访问 https://github.com/KusionStack/community加入我们的社区，参与讨论和共建。

# 参考[](http://localhost:3000/blog/2022-kcl-rewrite-with-rust#reference)

*   [https://github.com/KusionStack/KCLVM](https://github.com/KusionStack/KCLVM)
*   [https://github.com/Peefy/StackMachine](https://github.com/Peefy/StackMachine)
*   [https://doc.rust-lang.org/book/](https://doc.rust-lang.org/book/)
*   [https://github.com/sunface/rust-course](https://github.com/sunface/rust-course)
*   [https://www . influx data . com/blog/rust-can-be-different-to-learn-and-fairful-it-it-as-it-it-it-in-a-long-time 软件开发中最令人兴奋的事情/](https://www.influxdata.com/blog/rust-can-be-difficult-to-learn-and-frustrating-but-its-also-the-most-exciting-thing-in-software-development-in-a-long-time/)