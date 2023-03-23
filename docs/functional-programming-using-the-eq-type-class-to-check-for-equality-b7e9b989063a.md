# 函数式编程:使用 Eq 类型类检查相等性

> 原文：<https://betterprogramming.pub/functional-programming-using-the-eq-type-class-to-check-for-equality-b7e9b989063a>

## 探索 Eq 类型类及其用例

![](img/eda822505ebcef17179707ecf0831a37.png)

照片由[émile Perron](https://unsplash.com/@emilep?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/functional-programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

这将是一个包含几篇文章的系列，在这里我试图解释和展示一些函数式编程模式的用例。

我们将使用 TypeScript 并使用函数式编程库`[fp-ts](https://github.com/gcanti/fp-ts)`。这些例子和解释受到了来自`fp-ts`作者的[伟大文章系列](https://dev.to/gcanti/getting-started-with-fp-ts-setoid-39f3)的启发。

第一篇是关于`Eq type class`的。我们将探索`Eq type class`是什么，我们可以用它来做什么。

# 情商类型类

请阅读以下要点。我们将在这篇文章中讨论它。

在这个例子中，我们通过创建一个新的`Eq type class` → `Eq<Artist>`实例来比较两个`Artists`。

我们做的第一件事是用一个接口定义`Eq type class`。对于要成为`Eq type class`成员的东西，它必须实现一个`equals`方法。这个`equals`方法必须接受两个相同类型的东西(例如，两个数字)，如果`x`和`y`相等，则返回一个`boolean` → `true`，如果不相等，则返回`false`。

然后我们继续为`number`类型创建一个`Eq`实例。这实际上就像用一个`equals`方法返回一个对象一样简单，该方法通过执行`x === y`来检查作为参数传递的两个数字(`x`和`y`)是否相等。我们现在可以通过做`eqNumber.equals(1, 1)`来比较两个数字。

之后是`contramap`函数的定义。`contramap`接受一个`(b: B) => A`类型的函数。在我们的例子中，`B`的类型是`Artist`，而`A`的类型是`number`，这导致→ `(artist: Artist) => number`。然后它返回一个新函数，该函数接受类型为`Eq<A>`的东西，并返回一个`Eq<B>`。翻译成我们的例子→ `(eqNumber: Eq<number>) => Eq<Artist>`。

因此`contramap`允许我们通过定义一个简单的函数来轻松地创建`Eq type class`的新实例，该函数接受我们想要为其创建`Eq`实例的类型的对象。然后，这个函数应该返回某种类型的东西，在这种类型中，我们已经为类型`number`定义了一个`Eq`实例。

`contramap((artist: Artist) => artist.artistId)(eqNumber)`返回类型`Eq<Artist>`的新`Eq`实例。

最后，我们使用新创建的`Eq<Artist>`实例来比较两个`Artists`。

我们可以更进一步，做这样的事情:

这里我们用之前定义的`Eq<Artist>`来定义`Eq type class` → `Eq<Album>`的一个新实例，它检查两个`Album`是否由同一艺术家制作。

`Eq type class`对于定义类型的定制等式逻辑非常有用。

我希望你喜欢这个关于`Eq type class`的简短介绍。下一篇文章将是关于`Ord type class`。

# 参考

fp-ts 库→[https://github.com/gcanti/fp-ts](https://github.com/gcanti/fp-ts)