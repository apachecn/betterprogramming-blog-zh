# Java 流收集器解释

> 原文：<https://betterprogramming.pub/java-stream-collectors-explained-6209a67a4c29>

## 收集器是如何工作的——我们如何建立自己的收集器？

![](img/b9ed34555910745698c927e4599f99af.png)

照片由[莎伦·麦卡琴](https://unsplash.com/@sharonmccutcheon?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/@sharonmccutcheon?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

Java 8 带来了 Java 最伟大的附加功能之一:流 API。它使处理数据流变得非常方便，因为它允许我们将操作缓慢地链接在一起，并通过终端操作结束流畅的呼叫来执行实际的数据处理。

`[java.util.Stream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)`提供了两个不同的名为`collect(...)`的终端操作，它们将执行可变归约:

> 可变归约操作在处理流中的元素时，将输入元素累积到可变结果容器中，如集合或 StringBuilder。— [甲骨文](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#MutableReduction)

# 含电池

Java 8 在`[java.util.stream.Collectors](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html)`类中为我们提供了 37 个不同的收集器，这些收集器大致可以分为三个独立的组:

*   **减少/汇总成一个值或集合类型** 从用`[joining()](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html#joining--)`连接字符串到用`[toSet()](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html#toSet--)`创建新的集合，甚至利用新的特性，比如用`[summarizingInt(...)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html#summarizingInt-java.util.function.ToIntFunction-)` — 汇总数字流等等。
*   **分组** 三种不同的方式使用`[#groupingBy(...)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html#groupingBy-java.util.function.Function-)`和另外三种进行并发/并行处理。
*   **分区有
    和**两种`[#partitionBy(...)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html#partitioningBy-java.util.function.Predicate-)`方法可用。

最棒的是:我们不局限于所提供的收集者。如果我们需要一些更独特的处理，我们总是可以创建自己的。

# 收集器

如果你曾经检查过一些[流 API](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#package.description) 的源代码，你会发现许多泛型和许多不容易阅读或理解的代码。

这源于 Java 本身，因为不改变其核心或语言本身，实现函数式编程特性是不容易的。但是他们设法添加了这些伟大的新特性，而没有通过一些看起来吓人的代码来损害向后兼容性——至少乍一看是这样。

每个收集器都必须实现接口`Collector<T, A, R>`:

让我们稍微剖析一下界面，以便更好地理解发生了什么。

## 泛型类型

该接口由三种通用类型组成:

*   **T***–*将*类型的*输入元素进行归约操作
*   **A**–可变*累加*类型的归约运算。
    用于在收集过程中保留部分结果的累加器对象类型。
*   **R**–*T32*结果*归约操作的类型。收款流程的实际返回类型。*

## 方法

知道泛型类型代表什么，这些方法更有意义:

*   `supplier()`
    提供了一个`Supplier<A>`，用于创建累加器对象的新实例。
*   `accumulator()` `Collector`的核心，包括一个 `BiConsumer<A, T>`，负责将`T`类型的流元素累加到一个累加器对象中。
*   `combiner()`
    在[并行处理一个流](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html#parallelStream--)的情况下，`Collector`可能会创建多个累加器对象。组合器提供了合并结果的功能。
*   `finisher()` 通过将累加器对象转换为返回类型`R`来完成收集过程。
*   `characteristics()` 形容`Collector`的特点。

## 收集器特性

`Collector`的特性可用于优化归约操作的实施。这三个特征的任何组合都是可能的:

*   `[Collector.Characteristics.CONCURRENT](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collector.Characteristics.html#CONCURRENT)` 表示累加器对象支持并行或并发处理。
*   `[Collector.Characteristics.IDENTITY_FINISH](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collector.Characteristics.html#IDENTITY_FINISH)` 表示完成器函数是恒等函数，因此累加器可以直接转换为结果类型。
*   `[Collector.Characteristics.UNORDERED](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collector.Characteristics.html#UNORDERED)` 表示流中元素的顺序不一定保持不变。

# 示例:连接字符串

Java 已经提供了一个用分隔符连接字符串的`Collector`，但是它为我们自己实现提供了一个很好的例子:

很简单——但是仍然是功能很少，代码很多。

接口`Collector`提供了静态方法`[of(...)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collector.html#of-java.util.function.Supplier-java.util.function.BiConsumer-java.util.function.BinaryOperator-java.util.function.Function-java.util.stream.Collector.Characteristics...-)`以一种更具功能性的方式创建`Collector`，帮助我们减少对额外类的需求:

现在我们可以在一个接口或不可实例化的类中组合我们的自定义`Collector` creator 方法——就像 Java 对`java.util.Collectors`所做的那样，使用起来更加简单。

# reduce(…)呢？

代替`Collector`，我们也可以使用`[Stream#reduce(...)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#Reduction)`来实现类似的结果。两者的区别更微妙。reduce 操作通过以不可变的方式组合两个值来创建一个新值。

然而，collect 操作以可变的方式处理 accumulate 对象，并使用 finisher 来获得最终结果。

您应该选择哪一种取决于您的需求—考虑实际的预期目的、性能考虑等。

# 结论

一旦你理解了它们背后的一般概念，创建一个定制`Collector`并不复杂。

通过像 Java 那样结合我们的定制`Collector` creator 方法，我们可以在整个项目中使用和共享我们的收集器。

# 资源

*   [https://docs . Oracle . com/javase/8/docs/API/Java/util/stream/package-summary . html](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html)
*   [https://docs . Oracle . com/javase/8/docs/API/Java/util/stream/collectors . html](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html)
*   [https://www . Oracle . com/technical-resources/articles/Java/ma14-Java-se-8-streams . html](https://www.oracle.com/technical-resources/articles/java/ma14-java-se-8-streams.html)

[](https://medium.com/better-programming/functional-programming-with-java-streams-f930e0e4d184) [## Java 函数式编程:流

### 最佳实践概述

medium.com](https://medium.com/better-programming/functional-programming-with-java-streams-f930e0e4d184)