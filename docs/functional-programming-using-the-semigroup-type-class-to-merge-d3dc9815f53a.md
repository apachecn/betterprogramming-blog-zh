# 函数式编程:使用“半群”类型的类来合并事物

> 原文：<https://betterprogramming.pub/functional-programming-using-the-semigroup-type-class-to-merge-d3dc9815f53a>

## 探索“半群”类型类及其用例

![](img/468c290f2c4c3d9f44b3f97b90ca2801.png)

西蒙·艾布拉姆斯在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

这将是一个包含几篇文章的系列，在其中我试图解释和展示一些函数式编程模式的用例。我们将使用 TypeScript 并使用函数式编程库`[fp-ts](https://github.com/gcanti/fp-ts)`。这些示例和解释受到了来自`fp-ts`作者的[伟大文章系列](https://dev.to/gcanti/getting-started-with-fp-ts-ord-5f1e)的启发。

这是我们系列的第 3 部分，这篇文章是关于`Semigroup`类型类的。我们将探索什么是`Semigroup`类型类以及我们可以用它来做什么。

## 其他部分

*   **第一部分:**`[Eq](/better-programming/functional-programming-using-the-eq-type-class-to-check-for-equality-b7e9b989063a)`[类型类](/better-programming/functional-programming-using-the-eq-type-class-to-check-for-equality-b7e9b989063a)
*   **第二部分:**`[Ord](https://medium.com/@cedricdose/functional-programming-using-the-ord-type-class-to-order-things-17c3bfdc20ce)`[类型类](https://medium.com/@cedricdose/functional-programming-using-the-ord-type-class-to-order-things-17c3bfdc20ce)
*   **第三部分:**本文

# “半群”型类

首先我们来看看什么是`Semigroup`以及它有什么作用。请阅读以下要点:

太多了！但是别担心——我们会无话不谈的。

我们做的第一件事是用 TypeScript 接口定义`Semigroup`类型类。正如你所看到的，要成为`Semigroup`类型类的实例，需要定义一个`concat`方法。

这个`concat`方法接受两个相同类型的参数，并返回该类型的单个值。因此我们可以看到这个`concat`方法将两个类型为`A`的值组合/合并/连接成一个类型为`A`的值——例如，它接受两个字符串并返回另一个字符串。

我不知道你怎么想，但我认为这听起来对很多事情都很有用。我们将在本文后面看到一个更有趣的例子。

## 实现“半群”实例

在上面的要点中，接下来发生的是我们为乘法运算下的`number`类型实现一个`Semigroup`实例，这意味着我们用一个`concat`方法创建一个对象，该方法接受两个数并将它们相乘。我们也可以做加法。唯一的区别是，我们将两个数相加，而不是在`concat`方法中相乘。

我们还为类型`string`定义了一个`Semigroup`实例。这里我们将传递给`concat`方法的两个字符串相加(连接)在一起，构建一个新的字符串。

我们在`concat`方法中执行的前面的操作都是`associative`，这意味着我们不必将表达式括起来，只需编写`x * y * z`。`Associativity`是一个对于`Semigroup`实例的`concat`方法必须成立的法则。

## 实现“半群”实例的其他方式

`Associativity`能成问题。有时对于一个类型没有关联操作。令人高兴的是，有两种方法可以为一个类型创建一个`Semigroup`实例。

一种方法是用只返回第一个或最后一个输入的`concat`方法创建一个`Semigroup`实例。看看要点中的`getFirstSemigroup`和`getLastSemigroup`功能。

有了这个`getFirst`和`getLast`逻辑，我们也可以很容易地从一个已经定义了`Ord`实例的类型中创建一个`Semigroup`实例。我们可以通过在`concat`方法中返回最小值或最大值来实现。

第二种方法是创建一个所谓的类型为`A`的自由`Semigroup` 。这意味着我们为类型`A`创建一个`Semigroup`实例，方法是将值包装在一个单例列表`[A]`中，然后为类型`Array<A>`创建一个`Semigroup`实例，其中`concat`是`(x, y) => x.concat(y)`。`x.concat`指内置的 JS 数组方法。

## 实现选项的“半群”实例

将两个数字或字符串合并在一起非常容易，但是如果我们想要合并像`Option<A>`这样的上下文中的值呢？为此我们需要一些特殊的逻辑:

为类似于`Option<A>`的东西实现一个`Semigroup`实例也并不难。

我们只需要在`concat`方法中定义一些额外的逻辑。在这里，我们只是检查我们的`concat`方法中的两个值是否都是*而不是* `none`，如果是，我们从我们的`Option<A>`中取出值，并将其应用于我们传递到`getApplySemigroup`的`Semigroup`实例中的`concat`方法。

## 使用“折叠”合并值数组

现在我们对什么是“s”有了一种感觉，并且知道我们可以将两个值合并/组合/连接在一起。但是有时我们想要将两个以上的值合并在一起。我们如何才能做到这一点？

幸运的是，有一个函数可以做到这一点。它叫`fold`，和内置的 JS 数组方法`reduce`很像。让我们来看看:

所以我们可以看到，`fold`接受一个`Semigroup<A>`并返回一个函数，该函数接受一个初始值`A`和一个数组`A` ( `Array<A>`)并返回一个`A`。所以它将一个`Array<A>`折叠/缩减成一个单一的值`A`。在引擎盖下，`fold`也使用`Array.prototype.reduce`(`reducer`是传入`Semigroup`实例的`concat`方法)。

太酷了！我们现在可以映射一个数组，并将其合并/简化为一个值。当我们稍后看到一个更有趣的例子时，这就更酷了。

## 实现对象的“半群”实例

我们已经看到了一些如何为某些类型创建`Semigroup`实例的例子，但是我们还没有看到如何为`object`实现`Semigroup`实例。所以让我们开始吧:

`semigroupSong`的第一个定义是用更手动的方式创建的。
我们通过定义一个`concat`方法来为我们的`Song`类型创建一个`Semigroup`实例，该方法接受两个`Song`类型的对象，并将它们与我们为每个键定义的规则/函数组合在一起。

例如，我们通过过滤较大的数字来保留最近的年份，从而合并关键字`releaseYear`。

我们已经自己编写了将键合并在一起的规则/函数，但是我们也可以从现有的`Semigroup`实例中使用`concat`方法。就像我们的`semigroupProduct`，它的`concat`方法通过相乘将两个值合并在一起。

`semigroupSong`的第二个定义是通过使用`fp-ts`中的一些函数创建的。它遵循与之前大致相同的逻辑，但是这一次我们没有定义一个`concat`方法，只是为对象的每个键定义/使用完整的`Semigroup`实例，而不仅仅是一个函数。

在底层，它也只是将输入应用于我们为对象的键定义的`Semigroup`实例的`concat`方法。

这个例子的有趣之处在于，我们现在可以为每个键使用定制的合并逻辑来合并对象。这可能真的很有用——例如，当我们有两个来自不同数据源的`Song`并且想要将它们组合/合并成一个，但是对于每个键应该保留什么值有一些规则。

类型类对于定义类型的自定义合并逻辑非常有用。

我希望你喜欢这个关于`Semigroup`类型的简短介绍。下一篇文章将是关于`Monoid`类型类的。