# 如何在 JavaScript 中迭代字符串

> 原文：<https://betterprogramming.pub/how-to-iterate-through-strings-in-javascript-65c51bb3ace5>

## 了解 JavaScript 中最流行的字符串迭代技术

![](img/fd085696abe16f23d0a1954c97b583ec.png)

照片由 [Yurii Stupen](https://unsplash.com/@yurii_stupen?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

对于 JavaScript 中的许多事情，并没有单一的方法来实现它们。像迭代字符串中的每个字符这样简单的事情就是其中之一。让我们探索一些方法，并讨论它们的优缺点。

在我们开始之前，我们需要回到一个更基本的问题。

# 如何访问字符串中的单个字符

如今，在 ECMAScript 2015 (ES6)中，有两种访问单个字符的方式:

## charAt()

string 对象的这种方法已经存在了一段时间，可以看作是经典的方法。即使是最老的浏览器也支持它。

## 括号符号

第二种方法是通过括号符号访问字符:

ECMAScript 2015 中引入了这种方法，似乎更加方便。此外，它允许您将字符串视为类似数组的结构。这启用了一些迭代方法，我们很快就会看到。

## 哪种方法更可取？

`charAt()`的缺点可能是可读性。不过，它兼容 IE7 等老浏览器。反对括号符号的另一个理由是它不能用于给字符赋值:

这可能会令人困惑，尤其是因为不会有警告。

在我个人看来，括号批注更方便书写和阅读。兼容性问题应该通过 transpiling 来解决，而不是不使用所需的功能。

# 迭代字符串的流行方法

下面列出的技术并不完整。它将展示一些最常用的方法。

为了演示字符串中单个字符的处理，我们将使用以下函数:

## for 循环

经典方法——简单的`for`循环:

虽然读写起来有点麻烦，但这是已知最快的方法。

## 为了…的

该语句是 ECMAScript 2015 中引入的，可用于 iterable 对象。如果你不关心循环体中的当前索引，那么写起来比经典的`for`循环更方便。

## forEach()

这是一个`for`循环的功能版本。许多人更喜欢它而不是`for…of`和`for`循环，因为它是一个高阶函数，并且它有助于坚持利用不变性的编程风格(参见参考资料中的 Airbnb 风格指南)。

一个缺点是需要在迭代之前将字符串转换成数组。如果性能在您的用例中真的很重要(通常不是)，它可能不是您的第一选择。

# 结论

与 JavaScript 中的许多技术一样，决定使用哪一种主要是品味的问题。但是，要注意字符串到数组转换的速度影响以及括号符号的兼容性问题。

我建议你选择最可读的技术，如果你有性能问题，只关心速度优化，并通过传输解决兼容性问题。

# 参考

*   [Airbnb JavaScript 风格指南:迭代器和生成器](https://github.com/airbnb/javascript#iterators-and-generators)
*   [MDN 网络文档:字符访问](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String#Character_access)