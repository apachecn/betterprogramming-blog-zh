# Java 函数式编程:映射、过滤和归约

> 原文：<https://betterprogramming.pub/functional-programming-with-java-map-filter-and-reduce-d0df1092d6ee>

## 最常用的流操作

![](img/3ca22272535081e021ddfd85d6923477.png)

照片由[凯蒂·史密斯](https://unsplash.com/@kate5oh3?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/katie-smith-cooking?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

这三个方法，`map`、`filter`和`reduce`，是任何[函数式编程](https://medium.com/better-programming/functional-programming-with-java-an-introduction-daa783355731)的基石。

通常，我们的数据管道由一个或多个[中间操作](https://docs.oracle.com/javase/9/docs/api/java/util/stream/package-summary.html#StreamOps)，转换(又名*映射*)和/或过滤元素，以及一个再次收集数据的终端操作(又名*减少*)组成。

有了这三个，我们可以做很多事情，所以有必要深入了解它们。但是他们有一些近亲也是有用的。

注意:本文假设 Java 9。为了可读性，方法签名和可见性修饰符被缩短了。

![](img/35205717f70a0d30f1178327d4e89c98.png)

照片由[捕捉人心。](https://unsplash.com/@dead____artist?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[下](https://unsplash.com/s/photos/map?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 地图

`[Stream#map(Function<T> mapper)](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#map-java.util.function.Function-)`是转换每个元素的中间流操作。它应用它的参数 a `[Function<T, R>](https://docs.oracle.com/javase/8/docs/api/java/util/function/Function.html)`，并返回一个`[Stream<R>](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html)`:

这就是要点；使用起来非常简单。但是根据类型的不同，有专门的`map`函数。

## 平面地图

`[Stream#flatMap(Function<T, Stream<R>)](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#flatMap-java.util.function.Function-)`是经常被误解的`map`的兄弟姐妹。

有时映射函数会返回任意数量的结果，包装成另一种类型，比如`java.util.List`:

```
var identifier = List.of(1L, 5L);Function<Long, List<String>> mapper = (id) -> ...;identifier.stream()     // Stream<Long>
          **.map(mapper)**  // Stream<**List<String>**>
          **???**
```

最有可能的是，我们想要处理列表的内容，而不是列表本身。通过使用`flatMap`，我们可以将`Stream<List<String>>`映射到一个`Stream<String>`:

```
var identifier = List.of(1L, 5L);Function<Long, List<String>> mapper = (id) -> ...;identifier.stream()                    // Stream<Long>
          .map(mapper)                 // Stream<List<String>>
          **.flatMap(Collection::stream) // Stream<String>**
          ...
```

## 可选<t>#平面图</t>

在`[java.util.Optional<T>](https://docs.oracle.com/javase/9/docs/api/java/util/Optional.html)`的情况下，`[flatMap](https://docs.oracle.com/javase/9/docs/api/java/util/Optional.html#flatMap-java.util.function.Function-)`方法用于将`Optional`展平回其内容:

实际上，`[flatMap](https://docs.oracle.com/javase/9/docs/api/java/util/Optional.html#flatMap-java.util.function.Function-)`的实现甚至比`[map](https://docs.oracle.com/javase/9/docs/api/java/util/Optional.html#map-java.util.function.Function-)`做得更少，因为它省略了将映射器的返回值重新打包到新的`[Optional](https://docs.oracle.com/javase/9/docs/api/java/util/Optional.html)`中。

## 值类型映射/平面映射

在[项目 Valhalla](https://openjdk.java.net/projects/valhalla/) 和[泛型专门化](https://openjdk.java.net/jeps/218)到来之前，处理值类型和泛型总是一个特例。

我们可以依靠自动装箱，但我们不能否认这有额外的开销。JDK 包括专门的流类型来改进值类型的处理:

*   `[IntStream](https://docs.oracle.com/javase/9/docs/api/java/util/stream/IntStream.html)`
*   `[LongStream](https://docs.oracle.com/javase/9/docs/api/java/util/stream/LongStream.html)`
*   `[DoubleStream](https://docs.oracle.com/javase/9/docs/api/java/util/stream/DoubleStream.html)`

如果我们的映射函数返回一个相关的值类型，我们可以使用相应的`mapTo...(mapper)` / `flatMapTo...(mapper)`来创建一个基于值类型的流:

```
 IntStream [**mapToInt**(ToIntFunction<T> mapper)](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#mapToInt-java.util.function.ToIntFunction-)
  LongStream [**mapToLong**(ToLongFunction<T> mapper)](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#mapToLong-java.util.function.ToLongFunction-)
DoubleStream [**mapToDouble**(ToDoubleFunction<T> mapper)](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#mapToDouble-java.util.function.ToDoubleFunction-) IntStream [**flatMapToInt**(Function<T, IntStream> mapper)](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#flatMapToInt-java.util.function.Function-)
  LongStream [**flatMapToLong**(Function<T, LongStream> mapper)](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#flatMapToLong-java.util.function.Function-)
DoubleStream [**flatMapToDouble**(Function<T, DoubleStream> mapper)](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#flatMapToDouble-java.util.function.Function-)
```

这样，我们可以得到一个真正的`long`数组，而不需要中间装箱:

```
long[] hashCodes =
    List.of("hello", "world")
        .stream()
        **.mapToInt**(String::hashCode)
        .toArray();
```

## 为每一个

如前所述，`map`是一种中间操作。许多其他语言使用它来对所有元素执行操作，如果不使用`void`，则丢弃任何返回类型。

我们也可以像那样使用`map`,但是还有更好的方法。

通过利用终端操作`[Stream#forEach(Consumer<T>)](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#forEach-java.util.function.Consumer-)`，我们将消费者应用于流的每个元素:

```
listeners.stream()
         ... // other operations
         **.forEach**(this::trigger);
```

![](img/3b8dd5481c71f3c1b5fe9c8c040c7d4c.png)

韦德·奥斯汀·埃利斯在 [Unsplash](https://unsplash.com/s/photos/filter?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 过滤器

`[Stream<T>#filter(Predicate<T> predicate)](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#filter-java.util.function.Predicate-)`用于，你猜对了，过滤元素。如果谓词返回`true`，元素将在流中进一步移动:

如果我们使用一个变量作为谓词，那么使用`[Predicate<T>#negate()](https://docs.oracle.com/javase/9/docs/api/java/util/function/Predicate.html#negate--)`很容易被否定。Java 11 甚至为我们提供了`[static <T> Predicate<T>not​(Predicate<T> target)](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/function/Predicate.html#not(java.util.function.Predicate))`方法，所以我们可以用 lambda:

并非所有人都已经使用了 Java 11。但是我们可以在一个助手类中复制它:

助手类也可以是`import static`，所以我们可以省略`StreamHelpers`。

## takeWhile / dropWhile

这两种方法，`[takeWhile](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#takeWhile-java.util.function.Predicate-)`和`[dropWhile](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#dropWhile-java.util.function.Predicate-)`，是`filter`的近亲。他们的名字很容易理解。

它们是*短路流操作*，如果不必要，不处理流的所有元素。如果谓词返回`false`，则丢弃流的剩余部分(`[takeWhile](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#takeWhile-java.util.function.Predicate-)`)或者丢弃之前的所有内容(`[dropWhile](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#dropWhile-java.util.function.Predicate-)`):

## 无序流

只要流是有序的，这些方法就能按预期工作。在无序流的情况下，它们很容易变得不确定。如果不是所有元素都与谓词匹配，则返回的元素是任意的:

原因很简单:因为不清楚谓词遇到元素的顺序，所以结果不可能是确定的。

## 平行流

由于这些方法的有序性质，在并行流中使用它们是非常昂贵的，会影响整体性能。通常，顺序流是`[takeWhile](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#takeWhile-java.util.function.Predicate-)`或`[dropWhile](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#dropWhile-java.util.function.Predicate-)`的更好选择。

![](img/0a188a3646408b6a2366ec5be8529e29.png)

照片由[艾丹·托德](https://unsplash.com/@aidan_t?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/french-press?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 减少

`reduce`方法，在函数式编程行话中也称为 [*fold*](https://en.wikipedia.org/wiki/Fold_(higher-order_function)) ，用一个`[BinaryOperator<T>](https://docs.oracle.com/javase/9/docs/api/java/util/function/BinaryOperator.html)`累加流中的元素，并将它们缩减为一个值:

```
T [**reduce**(T initialValue, BinaryOperator<T> accumulator)](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#reduce-java.util.function.BinaryOperator-)
```

一个常见的用例是对值求和:

另外还有两种`reduce`型号可供选择:

```
Optional<T> [**reduce**(BinaryOperator<T> accumulator)](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#reduce-T-java.util.function.BinaryOperator-) <U> U [**reduce**(U initialValue](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#reduce-U-java.util.function.BiFunction-java.util.function.BinaryOperator-),
                   BiFunction<U, T, U> accumulator,
                   BinaryOperator<U> combiner);
```

第一个不需要初始值。因此，我们可能找不到匹配的元素来累积，因此将`Optional<T>`作为返回类型。

第二个用于并行流。累加可以并行化，多个结果合并。

## 计数/总和/最小值/最大值

常见的`reduce`用例已经可供我们使用，取决于流类型:

```
// All Streams[long Stream<T>#**count**()](https://docs.oracle.com/javase/9/docs/api/java/util/stream/Stream.html#count--)// Value-Type Streams[int IntStream#**sum**()](https://docs.oracle.com/javase/9/docs/api/java/util/stream/IntStream.html#sum--)
[int IntStream#**min**()](https://docs.oracle.com/javase/9/docs/api/java/util/stream/IntStream.html#min--) 
[int IntStream#**max**()](https://docs.oracle.com/javase/9/docs/api/java/util/stream/IntStream.html#max--)[long LongStream#**sum**()](https://docs.oracle.com/javase/9/docs/api/java/util/stream/LongStream.html#sum--)
[long LongStream#**min**()](https://docs.oracle.com/javase/9/docs/api/java/util/stream/LongStream.html#min--) 
[long LongStream#**max**()](https://docs.oracle.com/javase/9/docs/api/java/util/stream/LongStream.html#max--)[double DoubleStream#**sum**()](https://docs.oracle.com/javase/9/docs/api/java/util/stream/DoubleStream.html#sum--)
[double DoubleStream#**min**()](https://docs.oracle.com/javase/9/docs/api/java/util/stream/DoubleStream.html#min--)
[double DoubleStream#**max**()](https://docs.oracle.com/javase/9/docs/api/java/util/stream/DoubleStream.html#max--)
```

为了更好地理解`reduce`操作，让我们自己做一个简单的实现:

## 收集者

通过聚合流的元素，收集器在主题上与`reduce`相关。我们可以用这两种方法获得相似的结果，但是它们之间的区别更加微妙。

一个`reduce`操作通过以不可变的方式组合两个值来创建一个新值。然而，收集器使用可变的累积对象。

让我们实现两者的`String`串联:

`reduce`版本创建了许多`String`对象，因为它只能以不可变的方式工作。但是收集器可以利用可变的累积对象来减少实例化。

我们应该选择哪一个取决于我们的需求，考虑实际的预期目的、性能考虑等。如果我们处理不可变值类型，应该使用典型的归约。但是如果我们需要累积到一个可变的数据结构中，收集器可能更有意义。

# 结论

了解我们(功能)工具箱中最重要的工具总是一个好主意。

`map`作为变换应用于一个元素。

`filter`只累加与 a `Predicate<T>`匹配的元素。

`reduce`通过使用不可变值，将所有元素累加到一个值中。

这条推文完美地总结了这一点:

```
**You like my ramblings about Java?
Check out my upcoming book!** [https://belief-driven-design.com/book/](https://belief-driven-design.com/book/)
```

# 资源

*   [地图(高阶函数)](https://en.wikipedia.org/wiki/Map_(higher-order_function))(维基百科)
*   [过滤器(高阶函数)](https://en.wikipedia.org/wiki/Filter_(higher-order_function))(维基百科)
*   [折叠(高阶函数)(维基百科)](https://en.wikipedia.org/wiki/Fold_(higher-order_function))
*   [Java 教程:还原](https://docs.oracle.com/javase/tutorial/collections/streams/reduction.html)(甲骨文)
*   [麻省理工学院 6.005 软件构造](https://web.mit.edu/6.005/www/fa15/classes/25-map-filter-reduce/)(麻省理工学院)

# 相关文章

[](https://medium.com/better-programming/functional-programming-with-java-an-introduction-daa783355731) [## Java 函数式编程:简介

### Java 从来就不是函数式语言，但这并不意味着我们不能尝试

medium.com](https://medium.com/better-programming/functional-programming-with-java-an-introduction-daa783355731) [](https://medium.com/better-programming/functional-programming-with-java-streams-f930e0e4d184) [## Java 函数式编程:流

### 最佳实践概述

medium.com](https://medium.com/better-programming/functional-programming-with-java-streams-f930e0e4d184)