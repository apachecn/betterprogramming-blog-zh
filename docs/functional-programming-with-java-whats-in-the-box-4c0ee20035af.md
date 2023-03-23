# Java 函数式编程:盒子里是什么

> 原文：<https://betterprogramming.pub/functional-programming-with-java-whats-in-the-box-4c0ee20035af>

## JDK 中可用的不同种类的*功能接口*

![](img/3e75f5999faad1959e83e9c4fab4726d.png)

[Erda Estremera](https://unsplash.com/@erdaest?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/box?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在[的前一部分](https://medium.com/better-programming/functional-programming-with-java-an-introduction-daa783355731)中，我们学习了 Java 的功能特性。这一次，我们将回顾自 Java 8 引入 lambdas 以来 JDK 中包含的 [*函数接口*](https://medium.com/@benweidig/best-of-java-8-e5aa8cbed673#f751) 。

包`[java.util.function](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html)`提供了 43 个功能接口，包含了创建非常复杂的功能代码所需的所有构件。

# 四大

我们可能最常用的四个功能界面是:

*   `T Supplier<T>#get()` —不接受参数，但返回一个对象。对一个简单的 POJO-Getter 的方法引用被称为`Supplier`。
*   `void Consumer<T>#accept(T t)` —接受单个参数，不返回任何内容。每个 POJO-Setter 都有资格成为一个`Consumer`。
*   `R Function<T, R>#apply(T t)` —接受单个参数并返回一个对象。
*   `boolean Predicate<T>#test(T t)` —接受对象并返回`boolean`原语的专用函数。

仅凭这四点，我们就可以进行大量的函数式编程:

# 函数 Arity

Lambdas 通常使用相同的类型作为参数和返回类型。

由于 Java 是一种冗长的语言，我们不想一直写参数化类型。为了避免这种情况，Java 提供了更专用的接口和更简单的通用签名:

```
 Arity | Specialized       | Super-Interface
-------+-------------------+-------------------
   1   | UnaryOperator<T>  | Function<T,T>
   2   | BiConsumer<T,U>   | -
   2   | BiFunction<T,U,R> | -
   2   | BinaryOperator<T> | BiFunction<T,T,T>
   2   | BiPredicate<T,U>  | -
```

请注意，不要使用`...Operator<T>`作为方法中的参数，我们应该更喜欢使用它的超接口，以使方法更加灵活。

# 原始类型

在[项目 Valhalla](https://openjdk.java.net/projects/valhalla/) 和通用专门化( [JEP-218](https://openjdk.java.net/jeps/218) )可用之前，我们不能使用原语作为参数化类型。

尽管自动装箱/取消装箱在编译时“自动地”工作，但是增加的内存占用和可能的性能影响会抵消更简洁和高性能代码的最初目标。

就像原语包装器一样，Java 也为原语类型提供了多个专门的函数接口。

## 数字原语

数字原语`int`、`long`和`double`都有自己专门的功能接口。下面是给`int`的:

*   `[IntConsumer](https://docs.oracle.com/javase/8/docs/api/java/util/function/IntConsumer.html)`
*   一个类似于 T6 的接口，接受一个对象类型和一个参数。它应该有副作用，会影响被消费的对象参数。
*   `[IntSupplier](https://docs.oracle.com/javase/8/docs/api/java/util/function/IntSupplier.html)`
*   `[IntPredicate](https://docs.oracle.com/javase/8/docs/api/java/util/function/IntPredicate.html)`
*   `[IntFunction<R>](https://docs.oracle.com/javase/8/docs/api/java/util/function/IntFunction.html)`
*   `[IntUnaryOperator](https://docs.oracle.com/javase/8/docs/api/java/util/function/IntUnaryOperator.html)`
*   `[IntBinaryOperator](https://docs.oracle.com/javase/8/docs/api/java/util/function/IntBinaryOperator.html)`
*   `[ToIntFunction<T>](https://docs.oracle.com/javase/8/docs/api/java/util/function/ToIntFunction.html)` —接受对象类型参数并产生一个`int`值。
*   `[ToIntBiFunction<T,U>](https://docs.oracle.com/javase/8/docs/api/java/util/function/ToIntBiFunction.html)` —接受两个对象类型参数并产生一个`int`值。

所有这些接口都存在于另外两种类型`long`和`double`中，它们都有相应的类型名称。一些基本类型的缺失并不太糟糕，通过强制转换，它们可以很容易地被现有的类型所替代。

## 转换函数

以前的函数接口要么接受要么返回原始类型。但有时我们想接受*和*返回一个原语。Java 在这种情况下也为您提供了保护:

*   `[IntToLongFunction](https://docs.oracle.com/javase/8/docs/api/java/util/function/IntToLongFunction.html)`
*   `[IntToDoubleFunction](https://docs.oracle.com/javase/8/docs/api/java/util/function/IntToDoubleFunction.html)`
*   `[LongToIntFunction](https://docs.oracle.com/javase/8/docs/api/java/util/function/LongToIntFunction.html)`
*   `[LongToDoubleFunction](https://docs.oracle.com/javase/8/docs/api/java/util/function/LongToDoubleFunction.html)`
*   `[DoubleToIntFunction](https://docs.oracle.com/javase/8/docs/api/java/util/function/DoubleToIntFunction.html)`
*   `[DoubleToLongFunction](https://docs.oracle.com/javase/8/docs/api/java/util/function/DoubleToLongFunction.html)`

## 布尔代数学体系的

`boolean`原语不像数字原语类型那样受欢迎。只有一个功能接口是显式前缀:

*   `[BooleanSupplier](https://docs.oracle.com/javase/8/docs/api/java/util/function/BooleanSupplier.html)`

但是`[Predicate<T>](https://docs.oracle.com/javase/8/docs/api/java/util/function/Predicate.html)`和它的原语对应物可以被看作是返回`boolean`原语的专用函数接口。

# 函数接口上的默认方法

为了创建非常复杂的表达式，或者简化 lambda 的创建，JDK 提供的许多函数接口都有默认方法。它们通常被设计成流畅的界面。

这里有两个例子，查看本文中提到的[功能接口](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html)可以找到更多。

## 比较仪

一个简单的函数接口，用于比较两个相同类型的对象。让我们看一个简单的例子:

```
Comparator<MyBean> lambda =
    (lhs, rhs) -> lhs.getProperty().compareTo(rhs.getProperty());
```

多亏了默认方法`[Comparator.comparing(Function<T,U>)](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html#comparing-java.util.function.Function-)`，我们可以简化代码:

```
Comparator<MyBean> lambda =
    Comparator.comparing(MyBean::getProperty);
```

更简单，它可以很好地内联到`[Stream#sorted(Comparator<? super T> comparator)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#sorted-java.util.Comparator-)`中。

## 述语

另一个带有默认方法的功能接口是已经提到过的`Predicate<T>`。它提供了一切，是构建多标准谓词链的基本构件:

*   `[and(Predicate<T> other)](https://docs.oracle.com/javase/8/docs/api/java/util/function/Predicate.html#and-java.util.function.Predicate-)`
*   `[or(Predicate<T> other)](https://docs.oracle.com/javase/8/docs/api/java/util/function/Predicate.html#or-java.util.function.Predicate-)`
*   `[negate()](https://docs.oracle.com/javase/8/docs/api/java/util/function/Predicate.html#negate--)`

# 结论

JDK 为我们提供了许多不同的功能界面。由于 Java 的双重类型系统区分对象类型和原语，如果我们想有效地处理它们，就需要原语的专用接口。

很多函数接口的 fluent 接口提供了很多部件来构建复杂的表达式。特别是结合[流 API](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html) 和方法引用，我们可以精心制作相当简洁易读的代码。

# 资源

*   `[java.util.function](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html)` [包](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html)(甲骨文)
*   [功能接口](https://docs.oracle.com/javase/8/docs/api/java/lang/FunctionalInterface.html)(甲骨文)
*   [Java 8 中的函数接口](https://www.baeldung.com/java-8-functional-interfaces) (Baeldung)

[](https://medium.com/better-programming/functional-programming-with-java-immutability-8dc748e85f9e) [## Java 函数式编程:不变性

### 具有不可变状态的更好的数据结构

medium.com](https://medium.com/better-programming/functional-programming-with-java-immutability-8dc748e85f9e) [](https://medium.com/better-programming/functional-programming-with-java-streams-f930e0e4d184) [## Java 函数式编程:流

### 最佳实践概述

medium.com](https://medium.com/better-programming/functional-programming-with-java-streams-f930e0e4d184) [](https://medium.com/better-programming/functional-programming-with-java-an-introduction-daa783355731) [## Java 函数式编程:简介

### Java 从来就不是函数式语言，但这并不意味着我们不能尝试。

medium.com](https://medium.com/better-programming/functional-programming-with-java-an-introduction-daa783355731) [](https://medium.com/@benweidig/best-of-java-8-e5aa8cbed673) [## 最佳:Java 8

### Java 8 是一个非常大的更新，有很多很棒的新特性！他们中的许多人相互合作，所以让我们…

medium.com](https://medium.com/@benweidig/best-of-java-8-e5aa8cbed673)