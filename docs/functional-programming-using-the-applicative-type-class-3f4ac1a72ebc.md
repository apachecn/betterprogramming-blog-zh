# 函数式编程:使用应用类型类

> 原文：<https://betterprogramming.pub/functional-programming-using-the-applicative-type-class-3f4ac1a72ebc>

## 探索应用类型类及其用例

![](img/889447db0ee852f5a812deeb1da8db19.png)

[车窗](https://unsplash.com/@windows?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[挡泥板](https://unsplash.com/photos/EaB4Ml7C7fE?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上拍照

这是一系列文章的一部分，在这些文章中，我试图解释和展示一些函数式编程模式的用例。我们将使用 TypeScript 并使用函数式编程库 [fp-ts](https://github.com/gcanti/fp-ts) 。

这些例子和解释的灵感来自于`fp-ts`作者的伟大文章系列，你可以在这里阅读[。](https://dev.to/gcanti/getting-started-with-fp-ts-ord-5f1e)

这篇文章是关于`Applicative type class`的。我们将探索什么是`Applicatives`以及我们可以用它们做什么。

# 函子的问题是

在我之前关于`Functors`的文章中，我们看到它们允许我们用`effectful`函数、`(a: A => F<B>`组合`pure`函数、`(a: A) => B`。

但是有一个限制。`Functors`只允许我们用`unary effectful`函数组合`unary`函数(只有一个参数的函数)。如果我们想用`unary effectful`函数组合`binary`或`n-ary`函数(带有两个(`binary`或更多(`n-ary`)参数的函数)该怎么办？

这就是`Apply type class`的用武之地。

# 应用类型类

正如我们所见，`Apply`用另一个叫做`ap()`的方法扩展了我们的`Functor type class`。

`ap()`是一个可以解开`effect F`内部函数的方法(记住`effect`是类似于`Option`、`Either`等的东西)。)然后它可以将一个也包装在`effect F`中的值应用于该函数。

这听起来很像来自`Functors`的`map()`。唯一的区别是参数顺序被重新安排(首先是函数，然后是值)，并且函数也被包装在一个`effect F`中。在`ap()`方法的实现中，我们还使用了来自`Functor type class`的`map()`方法。

*   地图:`<A, B>(fa: F<A>, f: (a: A) => B) => F<B>`
*   美联社:`<A, B>(fab: F<(a: A) => B>, fa: F<A>) => F<B>`

那么，`ap()`如何帮助我们用`effectful`函数组合`pure n-ary`函数呢？

老实说，现在还没有。但是请耐心听我说——我们会谈到它的！

# Currying

如果我们想用`ap()`将`pure n-ary`函数和`effectful`函数组合起来，我们需要一个叫做`currying`的东西。

引用维基百科上关于[阿谀奉承的文章:](https://en.wikipedia.org/wiki/Currying)

> “Currying 是一种将带有多个参数的函数的求值转化为一系列函数的求值的技术，每个函数只有一个参数。例如，一个带有两个参数的函数，一个来自`B`，一个来自`C`，并在`D`中产生输出，通过 currying 转换成一个带有一个来自`C`的参数的函数，并产生从`B`到`C`的输出函数

这里有一个例子，我们可以用 currying 将一个`binary`函数重写为一个`unary`函数:

# 用 Apply 构造 N 元函数

也许你已经意识到为什么我们需要处理我们的`n-ary`函数，以便与`ap()`一起使用。

我们已经看到，`ap()`可以解开一个在`effect F`内部的函数，并向它应用一个也在`effect F`内部的值。

因此，如果我们获取一个`binary`函数并对其进行处理(让我们从上面获取`addCurried()`函数)，我们可以做`map(F<5>, addCurried)`，它将返回`F<(y) => 5 + y>`。其中在函数结束时，数字`5`被分配给`x`。然后，我们可以使用`ap()`将第二个参数`y`应用于`addCurried()`函数。所以，如果我们做了`ap(F<(y) => 5 + y>, F<2>)`，就会得到`F<7>`的返回。

在这里你可以看到`Apply type class`在工作。它给了我们用`effectful`函数组合`pure n-ary`函数的能力。

我们通过组合`map()`和`ap()`的功能来实现这一点。
首先，我们使用`map()`来提升一个`pure unary`函数来处理一个`effect`中的值。因为我们使用`map()`处理了`binary`函数和`partially applied`函数，所以我们现在在`effect`中有了一个`partially applied`函数，我们可以将它作为第一个参数传递给`ap()`。

之后，我们使用`ap()`从`effect`中打开`partially applied`函数，然后在`ap()`方法中使用`map()`将第二个/最后一个参数应用到我们的函数中，然后执行该函数并在`effect`中返回结果。

按照这种逻辑，我们可以用任何`arity`组合任何函数。对于一个`ternary`函数，我们可以这样做:

在上面的例子中，我们使用了像`O.none`或`O.some()`这样的东西。这些是来自`fp-ts`的函数。它们允许我们轻松地创建新的`effects`实例——在本例中是`Option effect`。我用它们来简化例子。

# 应用类型类

我们谈到了`Apply type class`，但还没有谈到`Applicative type class`。

我们已经在前面的要点中使用了`Applicatives`。当我们使用`O.none`和`O.some()`时，我们使用了一个`Applicative type class`实例。我们从`fp-ts`中导入了这个功能。

让我们来看看`Applicative type class`:

`Applicative type class`用`of()`方法扩展了`Apply type class`，该方法接受`A`类型的东西并返回一个新的`effect F<A>`。

因此，`of()`将类型`A`的值提升为类型`F<A>`的值。

这非常有用，因为现在我们也可以对`ap()`所需的参数进行`of(A)`。实际上，这正是我们在使用`fp-ts`、`O.none`和`O.some()`中的函数时所做的。

您可以像这样实现它:

你看，没有什么神奇或过于复杂的事情发生——它实际上非常简单。

# 用有效函数构成纯 N 元函数

为了用一个`effectful`函数组合一个`pure n-ary`函数，我们只需要将`pure n-ary`函数组合成`lift`函数。

为此，我们需要创建接受`pure`函数并返回接受`effects`函数的`lift`函数:

这里我们创建了两个`lift`函数。两者都是为了`Option effect`。`liftA2`用于`arity`为 2 的`pure`功能，而`liftA3`用于`arity`为 3 的`pure`功能。我们可以为任何`arity`和任何`effect`创建`lift`函数。

如果您阅读来自`lift`函数的代码，您可以看到我们使用`ap()`和`map()`方法来处理`n-ary`函数。

我们现在可以用`Apply type class`将`pure n-ary`函数与`effectful`函数组合起来。此外，我们现在可以用`Applicative type class`将类型`A`的值提升为类型`F<A>`的值。

但是不要太激动——如果我们想要组合两个函数，都是*`effectful`呢？*

*幸运的是，这个问题有一个解决方案:`Monads`。我们将在下一篇文章中讨论它们！*

# *参考*

*   *fp-ts 库:[https://github.com/gcanti/fp-ts](https://github.com/gcanti/fp-ts)*