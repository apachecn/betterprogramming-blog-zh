# 在 Scala 中使用蹦床创建栈安全递归

> 原文：<https://betterprogramming.pub/create-stack-safe-recursion-using-trampolines-in-scala-7c0ecd003fb9>

## 避免函数调用中的堆栈溢出

![](img/857254e4690497e03a563f9949f06a78.png)

照片由[拍摄杰米在](https://unsplash.com/@jsclick?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上看到

当我们想要计算组合值和其他迭代值时，经常使用递归。在函数中使用递归可以减少函数中编写的代码量。然而，很多时候，当您有大量输入时，递归调用可能很难处理。

我们来看下面这个调用斐波那契的例子:

在一个常规的递归调用中，我们可以通过尾递归来优化这个值。通常，在 Scala 中，注释`@tailRec`告诉编译器静态检查你写的当前函数是否是尾递归的。

这样做的问题是，尾递归有时很难构造，一些只有常规代码的函数无法完成尾递归。考虑以尾递归的方式创建斐波那契数列。

这篇文章的灵感来自于 Runar 比雅纳松的《Scala 中的函数式编程》一书。他试图解释`IO`,并给出了一些为我们构造的函数创建一般尾递归值的例子。这种尾递归调用叫做蹦床。

在这篇文章中，我想通过 Fibonacci 的例子来说明为什么它被称为蹦床，以及我们如何构造这个蹦床函数，使所有的函数都是尾递归的。

创建堆栈安全递归的技巧是将递归从堆栈移到堆空间。基本上，我们不是让 JVM 运行一个递归函数，并在每次执行递归时将新的帧推入堆栈，而是希望编写递归，这样我们就可以控制执行——而不是 JVM。

在上面的 Fibonacci 案例中，调用`fib`本身将执行控制权交给了 JVM。它将`fib(n-1)`推到调用框架，因为我们需要计算`fib(n-2)`并评估该值。如果我们简化我们的实现而不是调用一个函数，它构造一个数据类型。

例如，代替调用`fib(n-1)`，我们可以尝试将函数包装成一个数据类型`Suspense(() => Function[Int, Int])`数据类型来保存这个值。本质上，我们是在构造一个数据结构，而不是调用一个函数。

如果我们想用`Suspense`包装我们的函数调用，我们的返回值也需要数据类型。

本质上，函数调用应该是这样的:

我们将有一个`Trampolining`类型的构造函数来包装我们的结果值。

如果我们在每次函数调用时返回`Trampolining`，我们如何将这些结果链接在一起呢？

`fib`函数的原始值的递归值只返回一个`Int`。为了说明我的意思:

当我们做`fib(n-1) + fib(n-2)`时，它是有效的，因为两者都返回一个`Int`，我们可以在上面做加法。但是，如果我们返回一个`Trampolining`，那么如何将`Trampolining[Int]`和`Trampolining[Int]`的结果连锁起来呢？

如果我们想对我们的计算进行排序，这听起来像是一个`map`或`flatMap`。

我们的函数看起来像这样:

但是，我们也具体化了`flatMap`和`map`来构造数据类型，而不是调用函数`f`。我们只是想将它包装到数据中，以便可以在堆中构建它。

因此，我们的数据类型`Trampolining`必须具有`flatMap`和`map`功能。

在我解释如何用`Trampolining`使这个尾部递归之前，让我们看看下面的例子，它展示了如何构建`Trampolining`应该是什么样子:

我们可以按以下方式重写现有的递归函数:

*   如果原始函数返回一个`A`，新函数应该返回一个`Trampolining[A]`
*   原始函数中的每个`return`值都应该被包装在`Return`中
*   我们想要`Suspense`递归调用，这样程序就不会推到我们的堆栈框架
*   我们在递归调用之后做的事情，比如`fib(n-1)`和`fib(n-2)`的加法，都会被包装在`FlatMap`里面。

# 新斐波那契递归调用

我们现在的斐波那契递归调用如下所示:

# 尾部递归的调用

现在把这个做成数据构造器的目的是把程序的控制权委托给程序员。然后我们可以创建一个解释器来解释这个控制流。

解释器是我们在程序中使用尾递归调用的地方。

让我们如下创建我们的解释器:

注意，在`FlatMap`下的`Suspense`中，我们需要用`FlatMap`将它包装起来，以调用`f`函数，一旦函数调用完成，就执行那个`flatMap`。如果你只是创建了`run(x())`，像上面一样，它在得到`a`的值后就不会执行函数了。

# 运行代码

让我们用`fibTailRef(5)`来执行我们的代码，看看内部发生了什么。

调用`fibTailRef(5)`时，转到`else`语句。通过用它的定义替换我们的代码，我们可以得到这样的结果:

图表 1

当我们用解释器`run`执行这个构造函数时，它将进入`FlatMap` - `Suspense`情况，如下所示:

图表 2

它将执行一个`FlatMap(a(), f)`。因此它将如下执行`a()`:

图表 3

此时，我们将评估我们的函数`fibTailRef(4)`，它给出了我们:

图表 4

我们将上述`fibTailRef(4)`的定义代入图 4 中的`run`方法:

图表 5

此时，`FlatMap`案例在`FlatMap`下执行。在这种情况下，我们将函数从`a.flatMap f flatMap f1`重新关联到`a.flatMap(x => f(x).flatMap(f))`，所以它是尾递归的。这样做的原因是因为`a`将保持递归遍历——直到它遇到来自前两个模式匹配案例的基础案例`Return`或`Suspense`。之后，它会调用`f`函数。

这不会改变结果，因为我们利用了单子定律的结合律。

上面显示的图表 5 在被调用后如下所示:

图表 6

这个结构与我们的调用堆栈具有相同的形式，只是它是在堆中创建的。

# 为什么是蹦床？

如您所见，我们的筹码不会越来越多。我们首先调用`run`函数，该函数将其推送到堆栈中。然后，它将通过创建另一个`FlatMap`构造首次上升到`FlatMap`。当所有的`Suspense`值弹出堆栈并进入`fibWithRef(4)`时，它进入`run`函数。然后，它再次转到`fibWithRef(3)`，然后转到`run`。最后，它构建价值观。

我们不停地从`run`和`fibWithRef`来回跳跃——就像蹦床一样。

# 结论

我们创建的`Trampolining`数据构造器适用于很多递归场景。我们将 return 语句的值包装到`Trampolining`。我们将`Return`数据构造函数包装在常规递归调用返回的基本情况值上。最后，我们对想要对值执行的操作执行`flatMap`。

现在这个已经被广泛使用了，因为`Cats` 有一个`Eval` 单子来以堆栈安全的方式构造值。

最后，如果你有兴趣了解更多关于蹦床的知识，这里有一些参考资料供你参考:

*   [带有自由单子的无栈 Scala】](http://blog.higher-order.com/assets/trampolines.pdf)
*   [Scala GitHub 中的递归和蹦床](https://gist.github.com/eamelink/4466932a11d8d92a6b76e80364062250)
*   [Scala 中蹦床是如何工作的](https://free.cofree.io/2017/08/24/trampoline/)
*   [olek sii Avramenko 著《Scala 中的蹦床和堆栈安全》](https://medium.com/@olxc/trampolining-and-stack-safety-in-scala-d8e86474ddfa)

*最初发表于*[*https://edward-huang.com*](https://edward-huang.com/functional-programming/programming/scala/optimization/2021/01/17/common-pattern-of-creating-stack-safe-recursion/)*。*