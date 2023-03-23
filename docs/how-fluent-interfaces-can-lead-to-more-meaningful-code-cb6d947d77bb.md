# 流畅的界面如何能产生更有意义的代码

> 原文：<https://betterprogramming.pub/how-fluent-interfaces-can-lead-to-more-meaningful-code-cb6d947d77bb>

## 更简单、更安全的对象创建工作流程等等

![](img/7b8747464fb4d55c83344950330982f1.png)

Photo by [贝莉儿 DANIST](https://unsplash.com/@danist07?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/build?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

[马丁·福勒](https://www.martinfowler.com/aboutMe.html)，他在 15 年前创造了术语 [*流畅的界面*](https://martinfowler.com/bliki/FluentInterface.html) ，在他的著作 [*重构:改进现有代码*](https://martinfowler.com/books/refactoring.html) 中写道:

> *“任何傻瓜都能写出计算机能理解的代码。优秀的程序员会写出人类能理解的代码。”*

软件开发是关于沟通的。最终的接收者可能是一个编译器或解释器，以一系列 1 和 0 的形式运行我们的代码。计算机不关心源代码的可读性，但是交流中的其他参与者关心:*人类*。

这是一个开发者与另一个开发者的交流。或者在将来，当我们的代码看起来像是别人写的时候，我们自己也会这样。

我以前写过代码可视化表示的重要性，这篇文章将强调流畅界面的概念，以及我们如何将它整合到实例创建的代码库中。但是它也将展示使用这种特殊 API 风格的缺点。

所有代码示例都将使用 Java 或参考 Java 框架。但是流畅界面的一般原则也适用于任何其他语言。

# 流畅的界面

与许多其他设计模式一样，核心概念可以用一句话来概括:

fluent 接口被设计成通过高度依赖方法链来模仿特定领域语言(DSL)。

在面向对象编程中，方法链是一种连续调用多个方法的方式。这允许我们以一种更流畅、更直接的方式编写代码，并减少视觉噪音。

为了实现实际使用方法链接的能力，方法不能返回`void`或其他任意值。它们必须为给定的上下文返回最符合逻辑的值。

有三种方法可以让我们的代码更加流畅。

## 可变的方式

实例的每个 setter 或相关方法都会变异实例本身并返回`this`。

这种流畅的方式不适合实例创建，至少在我看来是这样。我们获得了方法链，但仅此而已。它应该保留给非 POJO 类型，例如工作流的流畅接口。

## 不可改变的方式

不是返回实例本身，而是返回表示变异状态的新实例。这种行为的一个典型例子是`[java.time](https://www.oracle.com/technical-resources/articles/java/jf14-date-time.html)` [API](https://www.oracle.com/technical-resources/articles/java/jf14-date-time.html) ，或`[java.math.BigDecimal](https://docs.oracle.com/javase/7/docs/api/java/math/BigDecimal.html)`:

## 使用中介构建器

类型本身不一定要通顺。我们可以使用额外的 fluent 辅助类型来创建实例，例如 fluent [builder](https://en.wikipedia.org/wiki/Builder_pattern) 。

在 Java 中，我们可以使用构建器来解决缺少命名参数的问题。

# 如何创建一个流畅的生成器

我们从描述开发人员的类型开始:

一个简单的 POJO，只是存储一些数据，没有任何额外的功能。

## 创建简单的构建器

为了改进实例创建，我们需要一个额外的构建器类型:

通过使用[构建器模式](https://en.wikipedia.org/wiki/Builder_pattern)，我们仍然拥有简单的 POJO 类型，但是现在实例创建也可以在准备构建器和创建实际实例时进行验证。

## 使用构建器

fluent builder 将构造和表示的代码分开。

我们获得了一种更容易理解的创建实例的方法，在构建过程中有额外的控制，比如验证。特别复杂的类型可以用构建器来改进。

但是我们可以改进我们的构建器，以及相应的类型，甚至更多。

## 用不变性改进构建器

一种常用的设计是使构建器成为其对应类型的嵌套类。这也意味着放弃一个纯粹的 POJO，而是获得一个不可变的类型，在我看来这是一个非常划算的交易:

`Developer`类型现在是完全不可变的，并且只能*通过使用构建器*来创建。

## 不可变生成器生成

老实说，这仍然需要很多额外的代码。我们需要为每一种类似 POJO 的类型编写它。

我们可以使用像 [*不可变*](https://immutables.github.io/) 这样的框架，而不是自己去做:

多亏了[注释处理](https://docs.oracle.com/javase/8/docs/api/javax/annotation/processing/Processor.html)，我们的构建器被生成以供使用，包括验证、更多的构建器方法、容易复制，以及比我们自己能做的更多的[特性](https://immutables.github.io/immutable.html#features):

# 流畅的工作流界面

尽管本文主要是关于流畅的构建者，但这并不是使用流畅界面的唯一方式。它们被广泛用于任何类型的多步骤工作流。

流畅 API 设计的一个很好的例子是 [Java Streams API](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html) 。我们可以通过将与流相关的调用方法链接在一起，而不是使用迭代集合的“传统”方法，来减少很多冗长的内容:

我们收集了大量代码，用于获取 2020 年前五位科幻小说作者的名字。

让我们用 streams 流畅地打这个电话:

如果我们包括格式和括号，我们设法将 20 行代码减少到大约 10 行。

减少杂乱是伟大的，但在我看来，更重要的是提高可理解性。流操作的简洁名称清楚地传达了它们的意图。

# 结论

流畅的界面很适合消费，但不适合创作。

创建一个更像 DSL 的 API 有助于开发人员使用更简洁、更容易理解的代码。

我们可以更好地控制如何创建一个对象，甚至可以将它分成多个步骤。验证可以集成在每一步中。这有助于将用于创建的代码从实际的表示中分离出来。

但是创建一个令人愉快、流畅的 API 并不是一件容易的事情。我们需要额外的辅助类型，这意味着大量额外的、经常重复的代码。

另一个缺点是可能产生更多的运行时错误。如果我们的类型需要构造函数中所有需要的参数，我们会直接看到需要什么。对于构建器，我们可能会错过它们，直到运行时才会意识到。

如果我们的 IDE 不支持链中不同点的断点，调试也会更加困难。这通常可以通过将链分成多行来解决，这同时也提高了可读性。

在我的公司，我们去年开始使用 fluent builder，这要感谢 *Immutables* 框架。我们开始只对新类型使用它，但随着时间的推移，如果合适的话，也会替换旧类型。

有时，将不可变类型集成到现有代码中并不容易，但这给了我们一个重新评估我们的 API 设计并对其进行改进的机会。

# 资源

*   流体界面(马丁·福勒)
*   [流畅的界面](https://en.wikipedia.org/wiki/Fluent_interface)(维基百科)
*   [一成不变的框架](https://immutables.github.io/)
*   [在 Java 8 中实现构建器模式](https://www.vogella.com/tutorials/DesignPatternBuilder/article.html) (Vogella)
*   【Java 函数式编程:不变性
*   【Java 函数式编程:流
*   [信噪比:突发码](https://codeburst.io/signal-to-noise-ratio-a45ae45c3545)