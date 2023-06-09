# Swift 中的类与结构——基础和内存管理

> 原文：<https://betterprogramming.pub/classes-vs-structs-basics-and-memory-management-4707714d82e7>

## 值类型与引用类型、堆栈与堆分配等等

![](img/17479066b94609496bf37b6462a6cf10.png)

照片由 [Fotis Fotopoulos](https://unsplash.com/@ffstop?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

我在采访中多次被问到这个问题:“什么是类，什么是结构？提及一些不同之处，以及何时会用到它们。”

所以我想我应该从最基本的开始着手解决这个问题，然后再讨论他们各自的利基属性。

# 概述

在很高的层次上，结构和类可以被认为是用来保存值(变量和常量)和函数的构造。它们各自提供的特性集是相似的，除了一些实现细节，这些细节实际上有助于您决定何时使用结构，何时使用类。

结构在两个方面不同于类:

1.  结构是值类型，而类是引用类型。
2.  结构不支持继承(本质上是第一点的结果)。

# 值类型与引用类型

在 Swift 中，类和闭包是引用类型，而结构和枚举是值类型。

值类型和引用类型的不同工作方式可以用下面的句子来总结:对值类型的每个新引用都是该成员的一个新副本，而对引用类型的每个新引用只是对同一成员的另一个引用。

举个例子就能很好地理解这一点:

从上面的例子来看，很明显，下面的事情正在发生:

1.  每当你把一个值类型变量(`personStruct`)赋给一个新变量(`personStruct2`)，这个变量的一个新副本就会被赋予这个新变量(`personStruct2`)。因此，对其中任何变量*的任何改变都不会影响其他变量*。
2.  每当你将一个引用类型变量(`personClass`)赋给一个新变量(`personClass2`或`personClass3`)，该变量的一个新副本就会被赋予新变量(`personClass2`或`personClass3`)。因此，对其中任何一个变量*的任何改变都会影响其他变量*。
3.  使用`let`定义的值类型不会让你改变它们的内容，但是引用类型就不是这样了。只有当变量本身指向另一个变量时，常量引用类型才会引发编译错误。

# 变化

`mutating`是一个特殊的关键字，保留给你想改变一个`struct`的内部成员时使用。

# 内存管理

这两种类型的内存管理在您决定是否使用结构或类时起着重要作用，因为它们会影响每种类型在使用它们的场景中的性能。在 Swift 中，有两种方式管理值类型和引用类型的内存:堆栈分配和堆分配。

所以我们来讨论一下他们是如何比较的。

## 堆栈分配

这种分配是通过在堆栈上分配内存来实现的，堆栈是一种线性数据结构，表现为后进先出的方式。最后输入到堆栈中的对象将首先被逐出。

每个线程都有自己的堆栈，分配在该堆栈内存中的对象是该线程专有的，这使得分配在堆栈上的对象是线程安全的。此外，堆栈上可以分配的内存少于堆内存。

在堆栈内存分配中，程序为您提供运行特定范围的代码所需的内存量。然后，堆栈向自身添加相同数量的内存，一旦作用域被缩小(即代码已经执行完毕)，内存就被释放。

现在，在该堆栈上分配/取消分配内存的成本是将堆栈指针移动所需的内存量，这仅需要执行一条指令。

没有任何引用类型与之相关联的值类型(即，它们或者不包含引用类型，或者包含引用类型)所需的内存可以在编译时计算，因为它们不是动态的，并且不需要引用计数语义来决定它们必须存在多久。它们存在于需要它们的作用域的范围内，一旦它们的使用完成，就被转储。因此，这种值类型的内存是在堆栈上管理的。

从表面上看，这使得值类型使用起来更快，因为它们的内存是使用堆栈分配来管理的。

## 堆分配

对于在编译时无法计算大小的对象和具有引用类型语义的对象，会进行堆分配，因为这意味着它们的生存期不能仅限于堆栈上分配的内存范围。相反，它需要一些全局内存空间来存放它。

现在，堆分配发生在堆数据结构上，与堆栈相比，堆数据结构通常更难管理。内存的分配和释放不会发生在一条指令中，因此比堆栈上的相应过程稍微复杂一些。使用[自动引用计数](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html)来取消内存分配。

此外，堆内存是一个全局托管空间，因此是线程不安全的，因为它不是特定于线程的。这要求您在访问堆内存时为它管理线程安全。

所有这些都表明，与堆栈内存相比，堆内存相对更难管理。它有自己的管理费用，因此只应在形势需要时使用 Swift 正是这样做的。

# 与引用类型关联的值类型

在现实世界中，您总是会遇到必须同时使用引用类型和值类型的情况。下面的讨论将帮助您做出更明智的决定，决定使用哪种类型来保存数据。

使用这两种类型时，可能会出现两种情况:

1.  具有值类型成员(或几个成员)的引用类型。
2.  一种值类型，其成员是引用类型。

## 案例 1:包含值类型的引用类型

在堆栈分配一节中，我特别提到，没有任何引用类型与之关联的值类型是使用堆栈进行内存管理的。

这样做的原因是，当您的引用类型包含值类型成员时，该引用类型将在堆中进行内存管理。因此，与其关联的值类型也需要保存在堆中，以便在取消分配引用类型之前不会取消分配。这种类型的一个非常常见但经常被忽略的例子是在闭包内部使用的值类型。

## 案例 2:包含引用类型的值类型

在这种情况下，值类型将不会在堆上分配，但它需要有关联的引用计数逻辑，因为它现在在内部保存了一个引用类型。这样做的原因是，如果它没有，那么它内部保存的引用类型可以被释放，即使值类型本身保存了对它的引用。

## 分配时拷贝和写入时拷贝

默认情况下，值类型遵循赋值时复制行为。这意味着当你将一个结构赋给一个新变量时，会创建该结构的一个新副本。

“写入时复制”是一种内存优化，它规定在向新成员赋值时不会创建新的值类型。相反，只有当第二个成员的值发生变化时，才会创建它们。这样做是因为复制包含引用类型的值类型会有很大的开销，因为引用计数语义需要与它们相关联。这是一项昂贵的手术。

这种内存优化仅适用于某些特殊的 Swift 结构(如`Array`、`Set`和`Dictionary`)，不适用于所有包含引用类型的值类型。

但是，如果你真的想这么做，你总是可以使用这个[例子](https://github.com/apple/swift/blob/main/docs/OptimizationTips.rst#advice-use-copy-on-write-semantics-for-large-values)创建你自己的写时复制结构。

这两种情况都会导致值类型的性能开销，因为它们要么需要不同的分配，要么需要有一些与它们相关联的特性，而这些特性不是它们的固有行为。这需要由 Swift 单独管理(这需要额外的时间)。

# 遗产

作为值类型，结构不支持继承，这给了它们很多性能上的好处(在内存管理、方法调度等方面)。)，但这并不意味着你不能扩展它们的功能。

可以在结构上扩展功能的方法是使用协议。

# 在结构和类之间选择

我们已经看到，结构和类在管理方式上有一些不同，这为结构的使用带来了一些令人信服的论据:

*   线程安全——每个线程都有自己的变量副本。因此，您不需要实现锁定策略。
*   没有副作用——您可以放心，仅仅因为您共享了一个`struct`实例，对该实例的任何更改都不会影响同一个`struct`的其他实例，因此满足了最小惊讶的[原则。](https://en.wikipedia.org/wiki/Principle_of_least_astonishment)
*   派生值的安全使用。

使用不变量有更多的好处，但这是另一天的主题(它们通常围绕着这样一个事实，即它们通常更容易预测和推理)。

但是我们也已经看到，将结构和类一起使用可能会使您陷入困境，因为使用结构可能不会给您带来您最初认为的好处。您可能最终会遇到这样的情况:使用类实际上会有更好的性能。

因此，在结构和类之间进行选择时，需要遵循以下一些一般规则:

1.  当您想要保存本身就是值类型的简单数据值时，请使用结构。
2.  因为堆栈内存通常是有限的，所以当需要保存轻量级对象时使用结构。
3.  当您无法控制对象的标识时，请使用结构。
4.  当您需要 Objective-C 的互操作性时，请使用类。

# 杂读

*   [利用价值语义—Sundell Swift](https://www.swiftbysundell.com/articles/utilizing-value-semantics-in-swift/)
*   [内存管理— SwiftRocks](https://swiftrocks.com/memory-management-and-performance-of-value-types)
*   [类和结构的比较— Swift 文档](https://docs.swift.org/swift-book/LanguageGuide/ClassesAndStructures.html)
*   [在类和结构之间选择——苹果开发者文档](https://developer.apple.com/documentation/swift/choosing_between_structures_and_classes)