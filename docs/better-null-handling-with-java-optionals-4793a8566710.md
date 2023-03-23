# 使用 Java 选项更好地处理空值

> 原文：<https://betterprogramming.pub/better-null-handling-with-java-optionals-4793a8566710>

## 可怕的`NullPointerException`是许多 Java 开发人员的祸根

![](img/7d017641bb72fdb463747bc1497488b8.png)

[https://unsplash.com/photos/OEdSGyyRWgs](https://unsplash.com/photos/OEdSGyyRWgs)

在 Java 8 之前，处理`null` 引用最好的方法就是处处检查变量。新的阶层`java.util.Optional<T>`极大地改变了这一点。

但首先，一些历史和与其他语言的比较。

# 一个十亿美元的错误

空引用的[发明者](https://en.wikipedia.org/wiki/Tony_Hoare)于 2009 年为其创造道歉:

> 我称之为我的十亿美元错误。这是 1965 年零引用的发明。当时，我正在用面向对象语言(ALGOL W)设计第一个用于引用的综合类型系统。
> 
> 我的目标是确保引用的所有使用都是绝对安全的，由编译器自动执行检查。但是我无法抗拒放入空引用的诱惑，仅仅是因为它太容易实现了。
> 
> 这导致了数不清的错误、漏洞和系统崩溃，在过去的四十年里，这些可能造成了数十亿美元的痛苦和损失。—查尔斯·安东尼·理查德·霍尔爵士 2009 年在伦敦 QCon

编程语言处理这种“错误”的方式非常不同。他们中的许多人都有处理`null`的适当方法，直接集成到语言本身中。

[Groovy](http://groovy-lang.org/) ， [Swift](https://developer.apple.com/swift/) ， [Ruby](https://www.ruby-lang.org/en/) ， [Kotlin](https://kotlinlang.org/) ， [C#](https://docs.microsoft.com/en-us/dotnet/csharp/) ，更支持一个[安全导航操作符](https://en.wikipedia.org/wiki/Safe_navigation_operator) : `.?`，或者在 Ruby 的情况下`&.`。

其他语言，如 [Objective-C](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html) 或 [Clojure](https://clojure.org/) ，决定嵌入更好的空处理。是时候用一些不同的方法来处理空值了。

# 迅速发生的

在 [Swift](https://swift.org/) 中，空引用被称为 nil。该语言对可选类型和`nil`处理有很好的支持。在它的帮助下，我们可以安全地调用属性，甚至流畅地将它们链接在一起:

结合一个[零合并操作符](https://en.wikipedia.org/wiki/Null_coalescing_operator)，我们可以做得更好，并且不必处理可选类型:

# Clojure

这篇文章是关于 Java 的，所以让我们看一个来自另一种 JVM 语言的例子。对于空引用，Clojure 也倾向于 nil。

但是 Clojure 没有提供一个安全的导航操作器来解决这个问题，而是决定尝试去掉`NullPointerException`。

即使 nil 等于 Java 的`null`，它也只是另一种值类型。只有 nil 的一个实例存在，它测试到`false`。

仍然有可能遇到一个`NullPointerException`，但是如果你按照预期的方式使用 Clojure，那就更难了。由于 nil 是一个表示空的值，而不是空无一物，所以处理和合并到您的代码中要容易得多。

# 目标-C

作为一种受 Smalltalk 启发的语言，Objective-C 不调用方法或字段，而是向对象发送消息。并且向 nil 发送消息不会引发异常，那么消息将会无声无息地失败。

这可能很好，你不必检查所有的东西，但这也是不好的，因为你可能没有意识到一条消息没有得到回复。

# Java 选项

随着 Java 8 的发布，以新类的形式提供了一种处理`null`引用的直接方式:`java.util.Optional<T>`。

它是其他对象的一个薄薄的包装，所以如果包含的对象不存在，您可以直接与`Optional`而不是空引用进行交互。

## 创建选项

可选件的创建方式有多种:

每个案例都有其存在的理由:

*   案例一。我们知道/需要一个价值:即使期权是处理`NullPointerExceptions`的好方法，如果我们真的需要一个呢？仅仅因为 API 设计使用了选项，就可能需要在特定情况下抛出异常。这就是`Optional#of(...)`的作用，它不接受空值。
*   案例二。我们不确定:最常用的创作形式。不执行空值检查。
*   案例三。我们确信没有值:`Optional.empty()`返回一个`static final`字段。没有必要在每次空的时候都创建一个新的可选的，我们肯定知道它。

## 检查值

最简单的检查方法是`Optional#isPresent()`:

为了不总是检查`false`，Java 11 引入了`Optional#isEmpty()`。

## 获取内部值

仅仅检查一个值是否存在并没有太大的帮助，我们想要使用它！最简单的方法是`get()`:

恭喜，我们刚刚抛出了一个异常！即使它不是一个`NullPointerException`，也一定有一个比我们每次使用选项都检查`isPresent()`更好的方法。

# 更好的方法

在真正的代码中，我们不只是想得到内部的值。相反，我们想和它一起工作。感谢 [lambda 表达式](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)，我们可以轻松而懒散地做到这一点。

在我们得到期权之前，处理价值的通常方式是这样的:

通过选项，我们可以将这段代码压缩到一个易读的单一方法调用链中:

`Optional#map(...)`只有在可选项实际包含一个值时才会被调用，否则，`Optional#orElse(...)`会被调用。

# 零合并

Java 不像其他语言那样支持空合并操作符:

对于可选件，我们可以通过使用`Optional#orElse`来使用相同的模式，中间没有任何额外的动作:

# 行动和替代方案

`java.util.Optional`的方法大致可以分为两组:动作和替代。

## 行动

*   `[map(Function<? super T,? extends U> mapper)](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html#map-java.util.function.Function-)`
    如果存在一个值，将应用映射函数，并返回一个新的可选结果。否则，将返回一个空的可选项。
*   `[flatMap(Function<? super T,Optional<U>> mapper)](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html#flatMap-java.util.function.Function-)` 与`map(...)`相似，但可以自己处理选项。
*   `[filter(Predicate<? super T> predicate)](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html#filter-java.util.function.Predicate-)` 如果一个值存在并且与谓词匹配，则返回可选的。否则，将返回一个空的可选项。
*   `[ifPresent(Consumer<? super T> consumer)](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html#ifPresent-java.util.function.Consumer-)` 只有值存在时，才会调用消费者。

## 可供选择的事物

从`or`开始，在没有价值的情况下提供替代方案的方法:

*   `[orElse(T other)](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html#orElse-T-)` 如果没有值，则返回`other`。
*   `[orElseGet(Supplier<? extends T> other)](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html#orElseGet-java.util.function.Supplier-)` 如果在可选的。
*   `[orElseThrow(Supplier<? extends X> exceptionSupplier)](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html#orElseThrow-java.util.function.Supplier-)` 如果不存在任何值，则调用供应商进行例外处理，例如`orElseThrow(IllegalArgumentException::new)`。

# 原始数据类型

由于`java.util.Optional<T>`是泛型类型，我们应该如何处理像`int`或`long`这样的原始数据类型？

当然，原语永远不可能是`null`，但这不一定是一件好事。与未初始化/默认值相比，能够表示空值是一个优势。

处理原语的一种方式是不使用它们，而是依赖于它们对应的对象包装类，例如`int -> Integer`。但是这只会产生来自[自动装箱/拆箱](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)的开销。

在[项目 Valhalla](https://wiki.openjdk.java.net/display/valhalla/Main) 及其改进的值类型处理对我们可用之前，我们可以使用专门的可选类来使用基本数据类型的可选功能:

*   `java.util.OptionalDouble`
*   `java.util.OptionalInt`
*   `java.util.OptionalLong`

仍然缺少一些原始数据类型，但这是一个开始。而且，他们缺乏所有的行动方法，除了`ifPresent(LongConsumer consumer)`。

# 警告

选项就像任何其他对象一样是 Java 对象，所以它们本身可能是`null`。如果我们设计一个 API 并决定使用可选的，我们*不能*返回`null`为可选的，*总是*使用`Optional.empty()`。

这必须按照惯例来执行，编译器不能帮助我们，除非使用额外的工具和`@NonNull`注释。

其他我们不应该直接与可选项一起使用的标准 Java 特性是关于等式的:`equals(...)`和`hashCode()`首先是关于比较可选项，其次才是关于值。

文档警告我们，结果可能是不可预测的。

# 结论

选项是以更简洁的方式处理空引用的好方法。我们可以替换许多`if-else`结构，甚至合并 lambda 表达式。

因此，如果可能和适用的话，我们应该用选项来设计我们的 API，而不是使用`null`。甚至原语在一定程度上也是可用的，所以在我们的下一个项目中没有理由使用可选的。

```
**You like my ramblings about Java? Check out my upcoming book!**
[https://belief-driven-design.com/book/](https://belief-driven-design.com/book/)
```

# 资源

*   [空指针](https://en.wikipedia.org/wiki/Null_pointer)(维基百科
*   [可选件](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)(甲骨文)
*   [Java 8 选项](https://www.oracle.com/technical-resources/articles/java/java8-optional.html)(甲骨文)
*   [运行中的 Java 8](https://livebook.manning.com/book/java-8-in-action/chapter-10?origin=product-toc)(曼宁)
*   [瓦尔哈拉](https://wiki.openjdk.java.net/display/valhalla/Main) (OpenJDK)