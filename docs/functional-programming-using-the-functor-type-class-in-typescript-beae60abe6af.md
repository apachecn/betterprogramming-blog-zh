# 函数式编程:在 TypeScript 中使用仿函数类型类

> 原文：<https://betterprogramming.pub/functional-programming-using-the-functor-type-class-in-typescript-beae60abe6af>

## 探索函子类型类及其用例

![](img/9397cabc0cbe5cd278869a3e3305cb19.png)

由 [Joshua Aragon](https://unsplash.com/@goshua13?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

这将是一个包含几篇文章的系列，在这些文章中，我试图解释和展示一些函数式编程模式的用例。

我们将使用 TypeScript 并使用函数式编程库 [fp-ts](https://github.com/gcanti/fp-ts) 。这些例子和解释受到了`fp-ts`作者的[伟大文章系列](https://dev.to/gcanti/getting-started-with-fp-ts-ord-5f1e)的启发。

这是我们系列的第 5 部分，这篇文章是关于`Functors`。我们将探索什么是`Functors`以及我们可以用它们做什么。

# 关于函数式编程

本质上，函数式编程完全是关于组合的——更确切地说，是函数组合。

## 作文

什么是作文？

组合是将两个或更多不太复杂的事物组合成更复杂的事物的过程。应用于函数，是将两个或两个以上的简单函数变成更复杂函数的过程。

我们用两个函数(`f`和`g`)来构建一个更复杂的函数(`h`)，它结合了`f`和`g`。我们组成`f`和`g` → `h = g` ∘ `f`(读作:在`f`之后执行`g`)。等效的编程如下所示:

```
const h = (x) => g(f(x))
```

当一个代码示例只不过是将来自`f()`的返回值作为输入值传递给`g()`时，它看起来不应该那么可怕。

在这里，我们有一个具体的例子，即使它不是那么有用。我们编写了`add()`和`say()`来构建一个更复杂的函数(`sayAddResult()`，它将`add()`和`say()`的功能组合成一个函数。

我们可以说我们的`add()`函数将我们的输入`A`转换为`B`(我们接受两个数字`A`并返回一个新数字`B`)。此外，我们可以说我们的`say()`函数接受一个`B`并返回一个`C`(我们接受一个数字`B`并返回一个新的字符串`C`)。所以基本上，我们用我们的两个函数先从`A`到`B`，然后从`B`到`C`。

一定有办法直接从`A`到`C`，你说呢？

你是对的。有，我们实际上已经看到了。这是我们的`sayAddResult()`功能。它从我们的`A`(两个数字)直接到我们的`C`(字符串)。因为我们组合了两个函数(`add()`和`say()`)，所以我们构建了一个直接从`A`到`C`的新函数。

听起来棒极了！所以我们可以组合/组成任何我们想要的功能？

可悲的是，事情没那么简单。为了能够组合函数，它们的输入和输出类型必须匹配。在我们的例子中，我们编写了`add()`和`say()`，它们的输入和输出类型匹配。`add()`返回类型为`number`的东西，而`say()`接受类型为`number`的东西。如果`add()`返回另一个类型或者`say()`接受另一个类型，我们就无法合成它们。

例如，如果一个函数的输出类型是一个`boolean`，而另一个函数的输入类型是一个`object`，我们就不能真正地组合它们。我们必须使用某种中间函数来排列我们的类型。

函数式编程经常发生的是函数返回效果。例如，`(a: A) => F<B>`，其中`F`是某种类型的构造函数。类型构造函数基本上是一个函数，它接受一个或多个类型作为参数(`n-ary type operator`)，并返回另一个类型。因此，`F`的例子可以是`Option`、`Either`、`Task`或`Array`。所以`(a: A) => F<B>`可能类似于`(a: string) => Option<number>`。如果我们想用类型签名`(a: A) => F<B>` & `(b: B) => C`组合两个函数，我们将没有办法，因为它们的输入和输出类型不匹配。

但是请不要放弃！有一个英雄可以帮助我们摆脱困境。万能的`Functor`！

# 函子

`Functors`可以帮助我们解决这样一个问题:我们想要构造一个函数，用一个纯函数返回某种效果。在我们的上下文中，纯函数意味着它只是一个不返回结果而是返回原始值的函数(例如，pure: `(a: number) => number`和 effective:`(a: number) => Option<number>`)。

如您所见，我们需要某种方法来转换`say()`函数，使其具有`(x: O.Option<number>) => O.Option<string>`而不是`(x: number) => string`的类型签名。我们必须以某种方式将纯函数(不返回效果的函数)引入效果世界，这样我们就可以再次使用我们的基本函数组合。

在这里，我们有两个例子来说明我们如何能够`lift`一个纯函数到世界的`Arrays`或`Options`:

在`lift`函数中，我们只需解开`Array`或`Option`中的值，然后将它应用于我们想要提升的纯函数。

## 那么到底什么是函子呢？

一个`Functor`就像一个`Semigroup`、`Monoid`、`Ord`或者`Eq`——一个`type class`。和其他的一样，它需要定义某些东西。对于`Functors`，是一个可以把一个函数`(a: A) => B`提升到`(a: F<A>) => F<B>`的方法。
这个`lift`功能更俗称`map`。`map`类似于`lift`，但是参数被重新排列:

*   电梯:`<A, B>(f: (a: A) => B) => ((fa: F<A>) => F<B>)`
*   地图:`<A, B>(fa: F<A>, f: (a: A) => B) => F<B>`

所以`Functors`允许我们将纯函数提升到它们的“世界”中

## 我们自己的仿函数实例

为了创建一个`Functor`实例，您必须定义一个`map`方法，将函数`(a: A) => B`映射/提升到它的上下文`(a: F<A>) => F<B>`:

我们刚刚定义了一个`map`方法，将一个纯函数提升到`Option`上下文，现在我们有了一个`Option`的`Functor`实例。

`map()`方法允许我们获取一个纯函数，该函数接受类似于`number`类型的东西，并将其应用于`effect`(类似于`Option`、`Either`等)。).

`map()`方法与 JavaScript 中内置的`Array`方法非常相似(`Array.prototype.map`)。JavaScript 中的一个`Array`实际上也是`Functor`类型类的一个实例。

下面是我们在上面的要点中所做的:我们采用了纯函数`double()`，它接受了类型`number`的内容，并应用了类型为`Option<number>`的`O.some(4)`中的值。我们用来自`Functor`实例的`map()`方法完成了所有这些工作。

# 用有效函数合成纯函数

为了将一个纯函数与一个有效函数组合起来，我们只需要将纯函数与“世界”联系起来。

为此，我们需要创建一个`lift`函数，它接收一个纯函数并返回一个接收`effect`的函数。

我们现在可以用一个纯函数组合一个有效函数。但是我们仍然有一个限制。我们想要提升的`pure`函数必须是`unary`(取一个参数)。如果我们想使用一个接受两个或更多参数的纯函数，我们仍然会被卡住。

幸运的是，我们的问题有一个解决方案:`Applicative Functors`。我们将在下一篇文章中讨论它们。