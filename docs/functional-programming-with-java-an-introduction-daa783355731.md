# Java 函数式编程:简介

> 原文：<https://betterprogramming.pub/functional-programming-with-java-an-introduction-daa783355731>

## Java 从来就不是函数式语言，但这并不意味着我们不能尝试

![](img/321bc0be2d02cb6f5e19f46f6598a1de.png)

威廉·戴尼奥在 [Unsplash](https://unsplash.com/s/photos/try?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Java 被设计成一种通用编程语言[](https://en.wikipedia.org/wiki/General-purpose_language)**，其核心是基于类的面向对象*。*

*随着 2014 年版本 8 的发布，一种更加函数化的编程风格变得可行。这个故事试图揭示函数式编程的深度。*

# *什么是函数式编程？*

*函数式编程(FP)范式是从 1936 年[阿隆佐·邱奇](https://en.wikipedia.org/wiki/Alonzo_Church)发明的*λ演算*演变而来的，“一种数学逻辑中的形式系统，用于基于函数抽象和应用使用变量绑定和替换来表达计算”([维基百科](https://en.wikipedia.org/wiki/Lambda_calculus))。*

> *一个科学流派或学科的哲学和理论框架，在此框架内，理论、定律、概括和为支持它们而进行的实验得以形成
> –[韦氏词典](https://www.merriam-webster.com/dictionary/paradigm)*

*借助声明式编程风格，FP 试图将我们的代码绑定在纯数学函数中，以构建可求值的表达式，而不是语句。*

# *函数式编程的核心概念*

*对函数式编程所基于的支柱做一个简短的概述，这样我们可以稍后检查 Java 是否适合。*

## *纯函数*

*一个纯函数有两个基本属性:*

*   *同样的输入产生同样的输出。*
*   *没有副作用，例如，影响全局状态或更改参数值。*

*这两个属性保证了纯函数可以安全地在任何环境中使用，甚至是以并行/并发的方式。*

## *对透明性有关的*

*由于不变输入参数的结果是可预测的，我们可以用返回值替换曾经运行过的函数。这叫做 [*参照透明*](https://en.wikipedia.org/wiki/Referential_transparency) 。*

*这是纯粹的*T2 表达式的结果，可以作为一种叫做 [*记忆化*](https://en.wikipedia.org/wiki/Memoization) 或“函数调用缓存”的优化技术来使用，以避免对已经求值的表达式进行不必要的重新计算。**

## *不变*

*改变数据结构会产生意想不到的副作用，我们必须对此进行检查。有了不变性，我们可以保持数据结构的可预测性和无副作用，并且更容易推理。*

*数据结构在初始化后不应该被修改。要改变/改变一个数据结构，我们必须创建一个包含改变值的新变量。*

*就像纯函数一样，我们可以安全地使用任何数据结构而没有副作用，即使在并行/并发环境中，也不会因为不同步的访问或对超出范围的状态的更改而产生不确定的结果或问题。*

## *递归*

*类似循环的自调用函数，多次执行相同的表达式，直到达到结束条件，并返回“非自函数调用”。这很重要，否则我们很容易以无限递归结束，直到我们得到一个`java.lang.StackOverflowError`。*

## *一阶和高阶*

*像任何其他值一样，函数应该是一等公民，所以它们需要能够被赋给变量，被其他函数用作参数，并且能够被函数返回。*

*高阶函数是通过接受一个或多个函数作为参数，或返回一个函数作为其结果，或两者兼有来定义的。这是下一个支柱所必需的。*

## *操作组合*

*纯函数的组合创建了一个更复杂的表达式。通过这种方式，初始函数可以是小的、有主题的、可重用的，但是组合的函数将在数据结构中执行更完整的任务。*

> *“在数学中，函数合成是取两个函数 ***f*** 和 **g** 并产生一个函数 ***h*** 使得***h(x)= g(f(x))*** *–[维基](https://en.wikipedia.org/wiki/Function_composition)**

# **用 Java 进行函数式编程**

**添加了 [lambdas](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html) 使得使用更像函数的编程风格成为可能。尤其是[流](https://jcp.org/en/jsr/detail?id=335) 和[选项](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)大量使用。**

**关于选项的更多信息，您可以查看我的文章 [*用 Java 选项更好地处理空值*](https://medium.com/better-programming/better-null-handling-with-java-optionals-4793a8566710) *。***

**但是新的特性能对抗 FP 的实际范例吗？或者仅仅是[匿名类](https://en.wikipedia.org/wiki/Syntactic_sugar)[等的](https://docs.oracle.com/javase/tutorial/java/javaOO/anonymousclasses.html)语法糖？**

## **句法糖**

**lambda 函数的一般语法是:`(<argument-list>) -> <body>`。从 Java 的角度来看，它由一个[函数接口](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)来表示，这个函数接口也可以由一个匿名类来表示。**

```
**// LAMBDA EXPRESSIONPredicate<String> lambda = (input) -> input != null; // ANONYMOUS CLASSPredicate<String> anonymous = new Predicate<String>() { @Override
    public boolean test(String t) {
        return t != null;
    }
};**
```

**两个谓词做的是同样的事情，所以看起来真的很好的语法糖。但是有趣的问题是编译器和 JVM 在幕后做什么。用一个简单的`main`方法包装，结果是这样的:**

```
**// ANONYMOUS CLASS
0: new           #2 // class Anonymous$1
3: dup
4: invokespecial #3 // Method Anonymous$1."<init>":()V
7: astore_1
8: return // LAMBDA
0: invokedynamic #2,  0 // InvokeDynamic #0:test:()Ljava/util/function/Predicate;
5: astore_1
6: return**
```

**匿名版本创建了一个匿名类`Anonymous$1`的新对象，产生了三个操作码。我们忽略了`7: astore_1`，因为 lambda 也需要[保存对局部变量](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.astore_n)的引用。**

**lambda 版本只需要一个操作码，新引入的操作码`[invokedynamic](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/multiple-language-support.html#invokedynamic)`。**

**当然，仅仅比较操作码计数是不公平的，因为在`invokedynamic`调用背后发生了很多事情。我们在字节码中看不到 JVM 实际上是如何处理 lambda 的。**

**操作码`invokedynamic`实际上是为 Java 7 创建的，以支持 JVM 更多的动态方法调用，改进动态语言，如 [Groovy](http://groovy-lang.org/) 或 [JRuby](https://www.jruby.org/) 。**

**JVM 不是在编译时链接动态方法，如 lambdas，而是在第一次执行之前将动态[调用点](https://docs.oracle.com/javase/7/docs/api/java/lang/invoke/CallSite.html)与实际的目标方法链接起来。**

**第一次调用时使用一个[引导方法](https://docs.oracle.com/javase/7/docs/api/java/lang/invoke/package-summary.html#jvm_mods)来链接它并返回一个方法句柄。这有点像我们在代码中使用反射，但是更安全，并且直接由 JVM 执行。**

**创建 lambda 有不同的策略(动态代理、匿名内部类、`MethodHandle`)，但是决策是由 JVM 在运行时做出的，而不是在编译时静态做出的。并且 bootstrap 方法只被调用一次。**

**正如我们所看到的，这不仅仅是*的语法糖。Lambda 表达式使 JVM 能够以新的方式优化我们的函数式代码，甚至允许 JVM 自行重用 lambda。***

# **功能核心概念和 Java**

**关于使用 lambdas 和匿名类的技术差异就说到这里，让我们看看 FP 范例是如何支持的。**

## **纯函数**

**检查。相同的输入导致相同的输出。没有副作用。**

```
**double circumference(double radius) {
    return 2.0 * Math.PI * radius;
}**
```

## **对透明性有关的**

**理论上，参照透明是可能的。我们有纯函数，我们可以缓存函数调用。**

**但是没有这样做的语言构造或助手。JVM 可能会优化调用、内联结果等。，但不像其他语言中的 [Clojure](https://clojuredocs.org/clojure.core/memoize) 那么容易。**

## **不变**

**在 JDK 有很多不可变的和事实上不可变的类:**

*   **`String`(仅事实上，hashCode 是延迟计算的)。**
*   **值类型包装器，如`Integer`、`Boolean`、`Long`。**
*   **`java.time`班级。**
*   **使用 [Java 9](http://openjdk.java.net/jeps/269) 的不可变集合。**
*   **…**

**我们可以让自己的数据结构不可变，或者手动，或者通过使用像 [Immutables](https://immutables.github.io/) 这样的库。但它仍然是一个实现细节，而不是像其他编程语言那样的语言特性。**

## **递归**

**检查。虽然 Java 编译器不支持[尾调用优化](https://en.wikipedia.org/wiki/Tail_call)。**

```
**long factorial(long n) {
    // Additional checks omitted... // End condition
    if (n == 1) {
        return 1;
    } // Recursive call
    return n * factorial(n-1);
}**
```

## **一阶和高阶**

**检查。但是 Java 一直是一种冗长的语言，所以它不像其他语言那样优雅。**

```
**// FIRST-CLASS
Supplier<String> lambda = myObject::toString;// HIGHER-ORDER
Supplier<String> higherOrder(Supplier<String> fn) {
    String result = fn.get();
    return () -> result;
}**
```

## **操作组合**

**检查。接口`[java.util.function.Function](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html)`提供了功能组合的方法:**

```
**Function<Integer, Integer> square = (input) -> input * input;
Function<Integer, Integer> multiplyByTen = (input) -> input * 10;// COMPOSE: argument will be run firstFunction<Integer, Integer> multiplyByTenAndSquare = square.compose(multiplyByTen);// ANDTHEN: argument will run lastFunction<Integer, Integer> squareAndMultiplyByTen = square.andThen(multiplyByTen);**
```

# **Java 是函数式语言吗？**

**不完全是。但这从来不是它的意图。**

**尽管 Java 表面上符合许多函数式编程范例，但它本质上并不是一种函数式语言。**

**它仍然是一种基于类的面向对象的通用编程语言，但是现在支持函数构造。然而，lambdas 和更像函数的编程风格是我们工具带的一个有价值的补充。**

**面向对象与函数式。不是非此即彼。**

**大多数函数概念必须遵守惯例和开发人员规范，而不是由编译器强制执行，而不是依赖语言特性。**

**在适当的情况下，我们仍然应该采用功能范式为我们的代码库提供的核心概念和约束。**

*   **不变性:总是一个很好的想法，不仅仅是对于函数式编程。多亏了第三方库，我们可以使用不可变的数据结构，而不需要太多额外的代码。**
*   **没有非法状态:设计你的状态总是合法的。避免空值、异常和锁定/同步。**
*   **泛型:处理正确的类型，不依赖于`Object`或`instanceof`，有助于在编译时识别不正确的用法。**
*   **没有魔力:任何不可预测的行为或副作用都与功能范式相反。如果可能的话，应该避免使用利用带有反射和/或代理的运行时愚蠢行为的库。**

**底线是，我们应该知道所有的概念以及如何使用它们。而且知道什么时候将它们应用到我们的代码中是合适的。**

**希望 Java 不断进化到一个更具功能性的领域，以更少的冗长和更简洁的代码提供更多的优势。**

**下一部分将强调不同类型的可用[功能接口](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html#package.description)以及如何使用它们。**

# **与其他 JVM 函数式语言的比较**

**为了给你一个更好的概述，这里有一些运行在 JVM 上的其他语言，包括函数原理。**

## **斯卡拉**

**[Scala](https://www.scala-lang.org/) 和 Java 一样，是一种通用语言，而不是专门的函数式编程语言。**

**但是与 Java 不同，它从一开始就被设计成函数式编程，具有语言级的特性，比如不变性。并且在两个方向上都有很好的 Java 互操作。**

## **Clojure**

**Clojure 是 JVM 上的一种方言，一种真正的函数式编程语言。**

**作为 Java 开发人员，Scala 很容易理解，而 Clojure 则是另一种野兽。代码是数据([同象性](https://en.wikipedia.org/wiki/Homoiconicity))，一切都是不可变的，数据结构是持久的，空处理是正确的，内置记忆，以及许多许多的括号。**

**一个缺点，至少在我看来，是如果你想从 Java 中使用 Clojure 的话，Java 互操作。反过来也很好。**

## **科特林**

**[Kotlin](https://kotlinlang.org/) 是另一种通用语言，函数式编程根植于其中。好的 Java interop，安卓一级语言。绝对值得一试。**

# **资源**

*   **[λ表达式](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)**
*   **[功能编程](https://en.wikipedia.org/wiki/Functional_programming)**
*   **[调用的动态](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/multiple-language-support.html#invokedynamic) — Oracle 文档**

**[](https://medium.com/better-programming/functional-programming-with-java-whats-in-the-box-4c0ee20035af) [## Java 函数式编程:盒子里是什么

### JDK 中可用的不同种类的功能接口

medium.com](https://medium.com/better-programming/functional-programming-with-java-whats-in-the-box-4c0ee20035af) [](https://medium.com/better-programming/functional-programming-with-java-streams-f930e0e4d184) [## Java 函数式编程:流

### 最佳实践概述

medium.com](https://medium.com/better-programming/functional-programming-with-java-streams-f930e0e4d184) [](https://medium.com/better-programming/functional-programming-with-java-immutability-8dc748e85f9e) [## Java 函数式编程:不变性

### 具有不可变状态的更好的数据结构

medium.com](https://medium.com/better-programming/functional-programming-with-java-immutability-8dc748e85f9e)**