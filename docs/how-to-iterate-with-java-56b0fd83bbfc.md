# 如何用 Java 正确迭代

> 原文：<https://betterprogramming.pub/how-to-iterate-with-java-56b0fd83bbfc>

## 不仅仅是“for”和“while”

![](img/aa939f72d812c3943403f4a92b53105f.png)

[内森·杜姆劳](https://unsplash.com/@nate_dumlao?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

迭代数据结构是您将遇到的最常见的任务之一。大家都知道经典，像`for`或者`while`。但是在 Java 中有更多的方法可以迭代，提供更多的功能。

*注意:假设使用 Java 8，但是如果适当的话，使用 Java 10 特性* [*局部变量类型推断*](https://developer.oracle.com/java/jdk-10-local-variable-type-inference.html) *来增加可读性。*

```
**TABLE OF CONTENTS**[The Classics](#8237)
[Iterator-based](#0240)
[Lambda-based](#b7e9)
[Lambda vs. Traditional](#9c69)
[Resources](#5c78)
```

# 经典作品

有三种经典的方法来迭代语言集成的关键字:

*   `for`
*   增强的`for` (Java 5+)
*   `while` / `do-while`

## `for` 循环

一个`for`回路是[，在文档中定义为](https://docs.oracle.com/javase/specs/jls/se8/html/jls-14.html#jls-14.14.1):

```
for (<initialization>; <termination>; <increment>) {
    ...
}
```

括号中的三个表达式具有不同的生命周期:

*   初始化—在循环开始时调用一次。
*   终止—如果计算结果为`false`，则终止循环。
*   增量— 每次迭代后调用。

通常，这三个表达式都会用到，但它们不是必需的:

即使有可能编写这样的代码，我也不推荐这样做。`for`循环是一个很好的工具，通过将三个表达式放在一对括号中，可以将迭代限制在一个地方。如果我们把表达式分开，我们的代码会变得难以理解。

如果没有终止表达式，循环将无休止地运行:

我们需要在循环中使用`break`来终止它。无限循环经常用于一种叫做[忙等待](https://en.wikipedia.org/wiki/Busy_waiting)的技术，这被认为是一种反模式。还有许多其他方法来处理并发性(例如，等待线程，等等。)，但这些都超出了本文的范围。

## 增强 for 循环

传统的基于表达式的`for`循环做得很好，但是它也是一个嘈杂而冗长的结构。大多数时候，我们的循环实际上是从相同的逻辑开始的:迭代所有的项并访问当前项。

Java 5 引入了增强的`for`循环来简化这个常见任务。它适用于符合`[java.util.Iterable<T>](https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html)`或者是数组的数据结构。

每个数组都有`Object`作为其基类，并实现`[Serializable](https://docs.oracle.com/javase/8/docs/api/java/io/Serializable.html)`和`[Cloneable](https://docs.oracle.com/javase/8/docs/api/java/lang/Cloneable.html)`，但没有`[Iterable](https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html)`。一个主要的优点是能够存储原始类型(例如`int`、`long`)，而不仅仅是成熟的对象，所以我们不必依赖自动装箱。

语法很简单:

我们之前的例子变得更加合理:

通常，由于直接的上下文，实际类型是清楚的。所以使用`var`将减少来自更长类型名称的任何额外噪音。

## while/do-while

`while`循环可以看作是一个简化的`for`循环，只有一个终止表达式:

与`for`一样，如果终止表达式的计算结果为`false`，则终止表达式会中断循环。

有些情况下，需要在循环块之后评估可能的终止。这就是`do-while`的作用:

这样，不管终止表达式如何，循环块至少运行一次。

# 基于迭代器

## 可迭代的

接口`[java.util.Iterable<E](https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html)>`通过提供`[java.util.Iterator<E>](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html)`使数据结构可用于增强的`for`循环:

它是一个简单的数据结构的抽象，通过知道是否有另一个元素以及如何获得它。实际迭代的周围框架通常由一个循环提供:

与增强的`for`环路相比，这种方法噪音很大，那么它有什么优势呢？

`[Iterator#remove](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html#remove--)()`方法让一切变得不同。当迭代时，我们不能改变当前正在迭代的数据。这段代码会抛出一个`[java.util.ConcurrentModificationException](https://docs.oracle.com/javase/8/docs/api/java/util/ConcurrentModificationException.html)`:

实际上，在这种情况下，在调用`[List#remove](https://docs.oracle.com/javase/8/docs/api/java/util/List.html#remove-java.lang.Object-)(Object)`的过程中并没有抛出`[ConcurrentModificationException](https://docs.oracle.com/javase/8/docs/api/java/util/ConcurrentModificationException.html)`。相反，访问`[next](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html#next--)()`的`[Iterator](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html)<Integer>`将抛出异常。

通过直接使用迭代器，我们可以在迭代时删除:

我们也可以使用一个`for`循环，将初始化和终止合并成一行:

## 列表迭代器

作为本质上的`[Iterator](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html)<E>`,`[ListIterator](https://docs.oracle.com/javase/8/docs/api/java/util/ListIterator.html)<E>`提供了向后导航和修改的附加功能:

可以将`[ListIterator](https://docs.oracle.com/javase/8/docs/api/java/util/ListIterator.html)`视为元素之间的*:*

```
 E[0]   E[1]   ... E[n]
Positions: ^      ^      ^          ^
```

任何修改操作都将由`[next](https://docs.oracle.com/javase/8/docs/api/java/util/ListIterator.html#next--)()`或`[previous](https://docs.oracle.com/javase/8/docs/api/java/util/ListIterator.html#previous--)()`在最后返回的元素上执行。

# 基于λ的

Java 8 中引入的 [lambdas](https://medium.com/better-programming/functional-programming-with-java-an-introduction-daa783355731#78bc) 为迭代带来了新的可能性。多亏了[默认方法](https://medium.com/@benweidig/java-8-interfaces-default-methods-for-backwards-compatibility-2767a6a70947)，每个实现`[Iterable](https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html)<T>`的类型都获得了将`[Consumer](https://docs.oracle.com/javase/8/docs/api/java/util/function/Consumer.html)<T>`应用于每个元素的能力:

正如我们可以从它的简单实现中看到的，与普通的增强型`for`循环相比，我们不会获得太多。这就是为什么它最适合简单的用例，比如调用方法引用:

## 使用流的 forEach

流是功能块的惰性顺序数据管道，这意味着它们将迭代数据，适当地应用功能块。

除了使用流的中间操作本身进行迭代，我们还可以使用终端操作`[Stream#forEach](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#forEach-java.util.function.Consumer-)(Consumer<? super T> action)`。但是就像使用`[Iterable](https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html)`一样，更小更合理的代码块提高了代码的总体清晰度。

最好将尽可能多的逻辑移到中间操作，并且尽可能高，这样操作的总数就尽可能少:

# Lambda 与传统

我们现在已经了解了迭代数据结构的不同方法。

但是我们如何选择使用哪一个呢？

尽管我很喜欢 lambdas，但它们也有很多缺点——尤其是在迭代方面:

*   异常:用 lambdas 处理(被检查的)异常并不有趣。我写了一整篇文章,讲述如何用函数方式处理它们。
*   没有中断:lambda 中的`return`将像传统循环中的`continue`一样工作，但是没有对应的`break`。
*   JVM 优化:没有[循环展开](https://en.wikipedia.org/wiki/Loop_unrolling)。这并不意味着 lambdas 没有优化，但与传统的循环相比，它们是不同的。
*   更深的调用堆栈:将为附加的方法调用创建一个堆栈框架。
*   调试:尽管 ide 在处理 lambdas 和 streams 方面做得更好了，但是遍历一个循环仍然更简单——特别是有了额外的堆栈框架。
*   没有副作用:只有有效的`final`变量可以在 lambda 中使用。

这听起来可能很糟糕，但 lambdas 和 streams 也提供了许多优势:

*   并行执行:流可以并行化，不需要额外的`[ExecutorService](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ExecutorService.html)`。
*   流畅:流畅的通话可以将操作分成更容易管理的模块。与循环块相比，整个调用更加简洁明了。
*   无副作用:有效的`final`变量不会产生副作用。

```
**You like my ramblings about Java?
Check out my upcoming book!**
[https://belief-driven-design.com/book/](https://belief-driven-design.com/book/)
```

# 结论

正如您所看到的，对于使用哪种类型的迭代没有简单的答案。这完全取决于上下文。代码的可读性和可维护性应该在决定哪种迭代最合适的过程中扮演重要角色。

# 资源

*   [For-Each 循环](https://docs.oracle.com/javase/8/docs/technotes/guides/language/foreach.html) (Oracle)
*   [列表界面](https://docs.oracle.com/javase/tutorial/collections/interfaces/list.html#Iterator) (Oracle)
*   [for 语句](https://docs.oracle.com/javase/specs/jls/se7/html/jls-14.html#jls-14.14) (JLS)

[](https://medium.com/better-programming/functional-programming-with-java-streams-f930e0e4d184) [## Java 函数式编程:流

### 最佳实践概述

medium.com](https://medium.com/better-programming/functional-programming-with-java-streams-f930e0e4d184)