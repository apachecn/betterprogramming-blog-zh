# 为什么函数式程序员更喜欢 For-comprehensive 而不是命令式代码块

> 原文：<https://betterprogramming.pub/why-functional-programmers-prefer-for-comprehension-over-imperative-code-blocks-7f2cebc34d15>

## 简而言之:可读性

![](img/95d4ed3cdbba58604ff1da137ec4b493.png)

照片由 [AZGAN MjESHTRI](https://unsplash.com/@azganmjeshtri?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

"为什么你把所有的东西都包装成一个效果，然后在这里放一个森林？"—另一个团队的一位高级工程师对我的第二次代码评审发表了评论。我查看了一下代码，首先出现在我脑海中的是，“因为它是可读的。”

可读性是很主观的。来自命令式编程领域并习惯于 Java 8 API 流式语法的人会说，用 for-comprehensive 对程序排序会使其更难阅读。另一方面，函数式程序员会认为 Java 8 API 类似流的语法更难跟踪代码的效果。因此，我试图找到一个更客观的观点来解释为什么它是可读的。

# 看起来像伪代码

For-comprehension 与 Haskell monad comprehension 非常相似。

理解是`flatMap`的语法糖。

`flatMap`代表顺序计算，是 Monad 的主要特质。因此，设计者 Scala 使用单子进行集合操作。

Scala 中的单子理解被设计成类似于一般 C/伪代码语言中的命令式`for`循环。为什么？所以它看起来像是命令式的顺序副作用代码块，就像 C 风格的语言。用于赋值的左数组在命令式伪代码中是典型的。因此，它创造了更好的可读性。

然而，Scala 和 Haskell 在理解语法上有所不同——它能做的不仅仅是执行两个一元运算`join`和`bind`(或者`map`和`flatMap`)。

在 Scala 中，没有`yield`的`for`理解翻译成`foreach`。`foreach`是产生副作用的命令式迭代。一个`yield`可以有一个保护，`yield bar if baz`，它转化为过滤元素。

仅仅这个原因并不能给出一个好的理由来解释为什么你更喜欢`for`理解而不是一般的 Java 代码块。然而，因为 for-comprehension 等价于 monad comprehension，所以您可以确保您的代码按顺序运行。

# 实施顺序操作

除非你使用`IO`，否则在你的函数中使用多重[效果](https://edward-huang.com/functional-programming/scala/monad/2020/06/21/what-is-effect-or-effectful-mean-in-functional-programming/)很难在不理解的情况下进行调试。

让我们举一个例子，如果您有一个函数，它对多个 API 调用进行排序:

从上面的代码中，您可以看到您用`flatMap`对多个 API 调用进行了排序。但是，您正在进行并行调用，而不是顺序调用。

未来是渴望的。这意味着一旦你实例化了这个值，它将触发一个线程并执行未来的内容。

与将每个效果包装在 for-comprehension 中相比，它强制任何效果按顺序执行。

使用 for-comprehension 和 effects 的另一个好处是，另一个开发人员可以很容易地快速浏览代码库并理解您的程序的意图，而无需了解未来如何工作。您不需要猜测这个模块是顺序运行还是并行运行。

上面的代码看起来非常简单，因为我们只是在做简单的 API 获取调用。然而，如果我们有一个嵌套效应会发生什么呢？比如调用一个会返回`Future[Option[Int]]`或者`Future[Future[Future[_]]]`的 API？

# 使用嵌套效果增加可读性

如果不理解的话，你会看到这样的东西:

然后，如果有更多的顺序调用，上面的代码行将会向右移动—就像回调地狱一样。

对于 for-comprehension，它有助于减少嵌套调用效果的数量。

我们使用 monad transformer 上的`cats.data`集合，并使用 for-comprehension 来计算多个嵌套的情感调用。for-comprehension 有助于减少嵌套调用的数量，使其看起来像一个顺序调用。

这种面向铁路的编程。使用面向铁路的编程，您可以在一个地方分离所有效果的所有错误处理——保持主要逻辑像伪代码一样。

# 这都是基于你的风格指南

在 PR 中说服理解性代码块相对于常规代码块的好处，因为它完全基于你团队的风格指南。

如果所有开发人员都认为编写常规代码块比理解更具可读性，那么编写代码块是更可取的。

以我为例，另一个开发者也评论了，在他的评论上“加 1”。但是，我并不完全同意这个解释。该团队更喜欢用命令式代码块风格编写代码，而不是用`for`理解。

我最终对首选的风格指南进行了修改。

# 来源

*   [编程语言 Scala 为什么把一元合成命名为“为了理解”？—软件工程栈交换](https://softwareengineering.stackexchange.com/questions/307565/why-does-scala-name-monadic-composition-as-for-comprehension)

*最初发表于*[T5【https://edward-huang.com】](https://edward-huang.com/scala/functional-programming/2021/11/30/why-do-functional-programmers-prefer-for-comprehension-over-imperative-code-block/)*。*