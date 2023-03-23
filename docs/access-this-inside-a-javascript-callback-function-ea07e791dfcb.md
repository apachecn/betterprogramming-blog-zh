# 如何在 JavaScript 回调函数中访问“this”

> 原文：<https://betterprogramming.pub/access-this-inside-a-javascript-callback-function-ea07e791dfcb>

## 让我们一劳永逸地解决这个令人困惑的关键词

![](img/18fc89f1bc75f9e623acfdd3d78072be.png)

照片由[江户努格罗霍](https://unsplash.com/@edonugroho?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

关键字`this`让许多 JavaScript 程序员感到困惑，尤其是那些来自其他语言的程序员。一个常见的问题是如何在回调函数中访问正确的`this`。

考虑下面的代码片段:

代码没有产生所需的输出。这里的问题是什么？为什么`data`按计划行事，而`this.data`却不是？

问题是`this`的值取决于函数在哪里执行。这叫做*运行时绑定*。其他变量的值取决于函数的定义位置。

这个例子中常见的误解来自于这样一个事实，即许多人认为`this`具有定义回调函数时的值。事实是，它的值取决于调用回调函数的上下文。从这个意义上来说,`this`关键字是特殊的。它对其他变量以及来自其他语言(如 Java)的`this`关键字表现不同。

# 那我们怎么才能访问它呢？

## 1.“自我”模式

一个常用的模式是创建一个名为`self`的变量，并在定义函数的范围内将`this`的值赋给它:

通过声明一个名为`self`的新变量(任何其他有效的变量名也可以)，并给它赋值`this`，您就实现了想要的行为。

## 2.显式设置“此”

处理我们问题的另一种方法是为函数显式设置`this`:

`bind`实际上创建了一个新函数，其中的`this`永久绑定到您传递给`bind`的任何值。这是解决你的问题的一种固有方式。

## 3.箭头功能

箭头函数不仅仅拥有比匿名函数表达式更简洁的语法。它们也像处理任何变量一样处理`this`关键字。这意味着`this`将保持封闭词法上下文的值。这并不意味着它获得了该值的副本。它可以像闭包内的任何其他变量一样被其他函数改变。

# 我应该使用哪种方法？

这在很大程度上取决于具体情况。我个人认为没有理由使用`self`。人们应该记住，JavaScript 中的`this`行为不是偶然的，而是有意的。它可以是有用的，并使功能非常灵活。所以不要简单地把`var self = this;`放在你所有函数的开头来对抗这个意图。尝试使用`this`关键字作为调用你的函数的对象的引用。

使用`bind`和使用箭头功能都有它们的用例。箭头函数总是匿名的。所以如果你想有一个命名函数，你应该使用`bind`。如果匿名函数对你来说很好，并且你想要非常简洁的代码，你可以使用箭头函数。请注意，对于箭头函数，您没有自由更改函数的`this`绑定。

没有单一的最佳解决方案。最终，这是一个固执己见的决定。只要确保你知道你为什么选择你的方法。

# 参考

*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Operators/this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)
*   [https://262.ecma-international.org/11.0/#sec-this-keyword](https://262.ecma-international.org/11.0/#sec-this-keyword)