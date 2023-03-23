# Java 函数式编程:不变性

> 原文：<https://betterprogramming.pub/functional-programming-with-java-immutability-8dc748e85f9e>

## 具有不可变状态的更好的数据结构

![](img/79e7ebf21a9b4146b8ffa6b73ddfb7ab.png)

[Zoltan·塔斯](https://unsplash.com/@zoltantasi?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/rock?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

正如在[的第一篇文章](https://medium.com/better-programming/functional-programming-with-java-an-introduction-daa783355731)中所阐述的，不变性是函数式编程的核心概念之一。真正的函数式编程语言通过设计在语言层面上支持它。但是在 Java 和大多数非函数式语言中，我们需要自己在代码级设计和实现它。

不变性背后的基本思想很简单:如果我们想改变一个数据结构，我们需要创建一个新的副本，而不是改变原始的数据结构。

# 为什么不变性很重要

为什么我们要采取额外的步骤来改变一个值呢？因为使用不可变的数据结构，我们获得了很多优势:

*   数据不会“在我们背后”改变
*   一经核实，将无限期有效。
*   没有隐藏的副作用。
*   没有“半初始化”的对象，通过不同的方法直到完成。这将解耦方法，有希望使它们成为纯粹的函数。
*   线程安全:不再有[竞争条件](https://en.wikipedia.org/wiki/Race_condition#Computing)。如果一个数据结构从不改变，我们可以安全地在多线程中使用它。
*   更好的缓存性能。
*   可能的优化技术，如[参考透明度](https://medium.com/better-programming/functional-programming-with-java-an-introduction-daa783355731#984c)和[记忆化](https://en.wikipedia.org/wiki/Memoization)。

# Java 不变性的状态

从设计上来说，Java 不是一种具有不可变性的语言，但是它为我们提供了自己创建不可变数据结构所需的所有部分。

## 内置不可变类型

JDK 中有各种不可变类型。这里有一些你可能已经遇到过的:

*   原始包装器(`[java.lang.Integer](https://docs.oracle.com/javase/7/docs/api/java/lang/Integer.html)`、`[java.lang.Boolean](https://docs.oracle.com/javase/7/docs/api/java/lang/Boolean.html)`等)。)
*   `java.lang.String`(除了一个[懒惰计算的](https://github.com/AdoptOpenJDK/openjdk-jdk9/blob/master/jdk/src/java.base/share/classes/java/lang/String.java#L1512)哈希码)
*   数学类型(`[java.math.BigInteger](https://docs.oracle.com/javase/7/docs/api/java/math/BigInteger.html)`、`[java.math.BigDecimal](https://docs.oracle.com/javase/7/docs/api/java/math/BigDecimal.html)`)
*   枚举
*   `[java.util.Locale](https://docs.oracle.com/javase/7/docs/api/java/util/Locale.html)`
*   `[java.util.UUID](https://docs.oracle.com/javase/7/docs/api/java/util/UUID.html)`
*   [Java 8 日期/时间 API](https://docs.oracle.com/javase/8/docs/api/java/time/package-summary.html)

## “最终”关键字

`[final](https://en.wikipedia.org/wiki/Final_(Java))`用于定义一个只能赋值一次的变量。起初这听起来像是不变性，但实际上不是。

不是创建一个不可变的数据结构，只有对它的引用是不可变的。这只能确保一个变量总是指向内存中的同一个点，它对内存内容本身没有任何影响。

## Java 14

2020 年 3 月，JDK 14 将发布[记录](https://openjdk.java.net/jeps/359)预览:浅不可变数据的“数据持有者”。希望我们可以用记录替换许多 JavaBean 类型，尽管与传统类相比有多种限制。它们将使记录使用起来更安全，但我们可能不得不重新思考如何构建我们的数据结构。

对于包括我自己在内的许多开发人员来说，Java 14 是遥远的未来。我用的是 Java 8，希望很快会是 Java 9。因此，现在让我们看看实现不变性的其他选择。

# 如何变得不可改变

有两种方法可以有效地创建不可变的数据结构而不需要 Java 14 记录:我们自己做，或者使用第三方框架。

## DIY 永恒

考虑一个典型的 JavaBean:

JavaBeans 被设计成带有 getters 和 setters 的 T2，因此它们可以用于各种场景。许多框架，如 ORM 或 GUI 设计器，都是基于反射的 T4。它们分析类来识别 getter 和 setter，通过反射直接使用字段，因此大多与不可变设计不兼容。

另一个陷阱可能是副作用。设置器可能不只是设置一个值，它们可能跟踪一个“脏状态”或设置多个值。这不是一个好的实践，但它总是发生。

这种特殊的 JavaBean 设计并不是必须的或固定的规则。这是我们经常使用的惯例，因为[习惯驱动开发](https://medium.com/better-programming/habit-driven-development-and-finding-your-own-style-32786e1eb8c8)，而不是因为我们实际上需要那个特定的设计。

让我们打破传统的 bean 设计，让它成为不可变的:

这真的很简单，我们得到了更短、更简洁的代码。现在我们的数据结构一旦初始化就不会发生意外的变化。

除非我们添加更多可变类型的字段…

不可变数据结构的每个字段都必须是不可变的。它的类型的每个字段也必须是不可变的。对于单一的可变类型，甚至可能是深度嵌套的，不可变的所有好处都将被破坏。

集合也是有问题的类型。不可修改的集合从 Java 7 开始就存在了。在 Java 9 中，增加了易于使用的[工厂方法](https://docs.oracle.com/en/java/javase/11/core/creating-immutable-lists-sets-and-maps.html#GUID-DD066F67-9C9B-444E-A3CB-820503735951)。但是，就像`final`一样，这只意味着集合本身不可修改，而不是包含的对象不可修改。所以请确保只在其中保存已经不可变的数据结构。

## 构建器模式

我们确保所有的字段都是不可变的，不管它们嵌套得有多深。但是我们还有一个问题:*如何构建数据结构*。

所有字段都必须在初始化时设置，所以我们需要一个包含所有字段的构造函数。但是如果我们不需要设置所有的字段呢？我们应该提供多个构造函数还是静态构造方法？我们如何确保设置了所有必需的字段并且结果对象是有效的？

通过使用 [*构建器模式*](https://en.wikipedia.org/wiki/Builder_pattern) 。

我们需要一个额外的类来封装构建不可变数据结构的复杂过程。通过将创建过程与表示分离，我们获得了对数据结构组装过程的精细控制，甚至可以添加验证。但这也意味着我们引入了一个可变的构建器，所以我们可以有一个不可变的数据结构。

假设一个更复杂的用户类型:

字段`active`和`lastLogin`是可选的——默认情况下，用户在明确声明之前是不活动的，并且从未登录过。要么在每次创建用户时提供参数，要么添加额外的构造函数来匹配不同的参数组合。

但是类型越复杂，我们需要的构造函数就越多。相反，我们创造了一个`builder`:

现在，我们可以使用流畅的 API 来构建用户:

或者我们可以分多个步骤来构建:

为了使我们的构建器更好，我们可以向方法或构造器添加验证—例如空检查，或电子邮件/密码验证—并抛出一个适当的异常:

通过将 builder 类作为静态嵌套类[直接放入相应的类型中来增加封装性是一个很好的做法。这样我们就可以一直使用类型名`Builder`:](https://docs.oracle.com/javase/tutorial/java/javaOO/nested.html)

我们成功地创建了一个不可变类型和一个相应的构建器。但是这是一大堆样板文件，每次我们引入一个新的类型时都很麻烦。我们写的每一段代码都可能引入错误。

这就是为什么使用第三方框架是一种真正的解脱，省去了键入所有代码，并提供了许多方便的方法和验证。

# 第三方框架

我们可以使用第三方框架，专注于数据结构的设计和建模，而不是自己编写所有代码。有了第三方框架，生成的代码不容易出错，得到的数据结构也更简洁。

## 不变的

正如项目“[不变的](https://immutables.github.io/)”所描述的那样:

> *Java 注释处理器生成简单、安全和一致的值对象。不要重复自己，试试 Immutables，这个领域最全面的工具！*

创建一个不可变类型就像创建一个抽象类型一样简单，要么是一个`abstract class`要么是一个`interface`，然后添加正确的注释:

注释处理器在幕后生成实际的实现，包括:

*   序列化支持。
*   生成器类。
*   需求验证。
*   方便复制的方法等。
*   `equals`、`hashCode`和`toString`

让我们用提供的构建器创建一个不可变的用户:

由于使用了`[@Value.Default](https://immutables.github.io/immutable.html#default-attributes)`注释和`Optional`类型，我们在调用`build()`时自动得到验证。如果没有满足所有的要求，抛出一个`IllegalStateException`。

框架提供的另一个便利是添加助手方法来创建不可变的副本，而无需重置所有值:

或者我们可以找一个新的建筑商合作:

这只是冰山一角，功能列表很全面:

*   [严格构建器](https://immutables.github.io/immutable.html#strict-builder):每个构建器只允许使用一次构建器方法。
*   [派生属性](https://immutables.github.io/immutable.html#derived-attributes):从其他属性派生的只读值。
*   [前提条件](https://immutables.github.io/immutable.html#precondition-check-method):增加前提条件检查，如交叉验证。
*   [辅助属性](https://immutables.github.io/immutable.html#auxiliary-attributes):从`equals`、`hashCode`、`toString`中排除一个属性。
*   [风格定制](https://immutables.github.io/style.html):可以定制生成代码的很多方面。
*   [JSON 支持](https://immutables.github.io/json.html) : [杰克森](https://github.com/FasterXML/jackson)和[芭乐](https://github.com/google/guava)。
*   …以及更多

## 龙目岛项目

[Project Lombok](https://projectlombok.org) 是一个全面的工具，试图减少常见 Java 样板代码的数量，如[getter 和 setter](https://projectlombok.org/features/GetterSetter)、 [null checks](https://projectlombok.org/features/NonNull) 、 [equals/hashCode](https://projectlombok.org/features/EqualsAndHashCode) 或 [toString](https://projectlombok.org/features/ToString) 。

当然还有[不变的](https://projectlombok.org/features/Value):

注释`[@Value](https://projectlombok.org/features/Value)`相当于使用这些不言自明的注释:

*   `[@Getter](https://projectlombok.org/features/GetterSetter)`
*   `@FieldDefaults(makeFinal = true, level = AccessLevel.PRIVATE)`
*   `[@AllArgsConstructor](https://projectlombok.org/features/constructor)`
*   `[@EqualsAndHashCode](https://projectlombok.org/features/EqualsAndHashCode)`
*   `[@ToString](https://projectlombok.org/features/ToString)`

唯一缺少的是`[@Builder](https://projectlombok.org/features/Builder)`，它给我们的例子增加了`User.builder()`。

龙目岛项目是减少样板文件的好工具。但是对于构建灵活的不可变数据结构，我会推荐[不可变](https://immutables.github.io/)。两个项目有不同的目标，我认为一个名为“Immutables”的项目在不变性方面有更好的特性是不言而喻的！

# 结论

不变性对于许多种类的软件项目都是一个好主意，不仅仅是没有内置支持的语言。即使对于数据存储，它也能提供优势，例如版本控制系统 Git。它使用不可变提交来确保完整性。

但是这并不意味着每个问题都可以用不可变的数据结构来解决。可变状态本身并不是一件坏事。我们只需要确定何时使用它，并意识到改变状态的陷阱。

一个缺点是，至少在一开始，要让你的代码库适应新的数据结构。改变它们并不是一个简单的替代。但它有助于我们创建一个更可预测的数据流，具有容易观察到的状态变化。

## 我们现实世界的例子

我们决定开始使用不变量来简化会话管理。对于深度嵌套的非不可变数据结构，检测“脏”会话可能很困难。并且为每个请求保留每个会话会产生大量不必要的开销。

多亏了我们新的会话数据结构，它只包含不可变类型作为字段，检测已更改的会话要简单得多:如果字段被更新，会话就是脏的。不再背着我们修改嵌套的对象。

但是我们并没有止步于会话管理。我们已经开始用不变量替换越来越多的类型，以消除许多微妙的错误，比如无效的状态或竞争条件。如果一个数据结构在它的生命周期中没有太大的变化，我们试图使它不可变。

```
**You like my ramblings about Java? Check out my upcoming book!**
[https://belief-driven-design.com/book/](https://belief-driven-design.com/book/)
```

# 资源

*   [Java 教程](https://docs.oracle.com/javase/tutorial/essential/concurrency/immutable.html)
*   [爪哇记录(预览)](https://openjdk.java.net/jeps/359) (JEP 359)

## 图书馆

*   [一成不变的](https://immutables.github.io/)
*   [龙目岛项目](https://projectlombok.org/)

## 文章

*   [不变量介绍](https://www.baeldung.com/immutables) (Baeldung)
*   [龙目岛项目介绍](https://www.baeldung.com/intro-to-project-lombok) (Baeldung)
*   [为什么不变量是更好的对象，以及如何实现它们](https://reflectoring.io/java-immutables/)(反射)

[](https://medium.com/better-programming/functional-programming-with-java-streams-f930e0e4d184) [## Java 函数式编程:流

### 最佳实践概述

medium.com](https://medium.com/better-programming/functional-programming-with-java-streams-f930e0e4d184) [](https://medium.com/better-programming/functional-programming-with-java-whats-in-the-box-4c0ee20035af) [## Java 函数式编程:盒子里是什么

### JDK 中可用的不同种类的功能接口

medium.com](https://medium.com/better-programming/functional-programming-with-java-whats-in-the-box-4c0ee20035af) [](https://medium.com/better-programming/functional-programming-with-java-an-introduction-daa783355731) [## Java 函数式编程:简介

### Java 从来就不是函数式语言，但这并不意味着我们不能尝试

medium.com](https://medium.com/better-programming/functional-programming-with-java-an-introduction-daa783355731)