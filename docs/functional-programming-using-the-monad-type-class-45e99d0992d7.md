# 函数式编程:使用 Monad 类型类

> 原文：<https://betterprogramming.pub/functional-programming-using-the-monad-type-class-45e99d0992d7>

## 探索 Monad 类型类及其用例

![](img/e1886242366e69dd94f78afa15e27b23.png)

[Fatos Bytyqi](https://unsplash.com/@fatosi?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

这将是一个包含几篇文章的系列，在这里我试图解释和展示一些函数式编程模式的用例。

我们将使用 TypeScript 并使用函数式编程库 [fp-ts](https://github.com/gcanti/fp-ts) 。这些例子和解释受到了来自`fp-ts`作者的[伟大文章系列](https://dev.to/gcanti/getting-started-with-fp-ts-ord-5f1e)的启发。

这是我们系列的第 7 部分，这篇文章是关于`Monad type class`。我们将探索什么是`Monads`以及我们可以用它们做什么。

# 嵌套上下文的问题是

到目前为止，我们已经看到了如何使用来自`Functor type class`的`map()`将`pure unary`函数提升到`Functor`“世界”,以及如何使用来自`Apply type class`的`ap()`来提升`pure n-ary`函数。

`Functors`允许我们像`(a: A) => B`一样提升功能。
`Apply`允许我们像`(a: A) => (b: B) => C`一样提升功能。

但是如果我们有一个类似于`(a: A) => M<B>`的函数呢(`M`是`effect`的占位符，例如`Option`或`Array`)？如果我们将这个函数与`map()`一起使用，我们将得到一个嵌套的上下文:`M<M<B>>`。

这里有一个简单的例子来说明这个问题:

# 解决嵌套上下文问题

为了修复我们的嵌套上下文问题，我们需要某种东西能够以某种方式`flatten`或`unwrap`内部值，这样我们就可以将`M<M<B>>`转换为`M<B>`。

我们需要一个`flatten()`函数:

这个`flatten()`函数正是我们想要的。它打开/展平嵌套的上下文。

# 进入单子类型类

嵌套上下文是一个如此常见的问题，以至于有一个特殊的类型类用一个`flatMap()`方法扩展了我们的`Applicative type class`，顾名思义，这个方法在`effect`上`maps`，然后`flattens`结果。它结合了`map()`和`flatten()`的功能。

我们说的类型类就是`Monad type class`。

下面是`Option effect`的`Monad type class`实现示例:

我们现在能够`map`覆盖`effect`和`flatten`结果，所以我们不会以嵌套的上下文结束。

如你所见，我们现在可以使用`Monad type class`来提升和使用类似`(a: A) => M<B>`的功能。

`Monad`总是被描绘成复杂的事物，但是如果你从`Fucntors`到`Apply`再到`Applicatives`，它就不再那么神秘了。它基本上只是对`Functors`的一点扩展，可以从嵌套的上下文中取出内部值。

到目前为止，我们已经有了一个丰富的工具包来处理和处理各种效果。

# 参考

fp-ts 库:[https://github.com/gcanti/fp-ts](https://github.com/gcanti/fp-ts)