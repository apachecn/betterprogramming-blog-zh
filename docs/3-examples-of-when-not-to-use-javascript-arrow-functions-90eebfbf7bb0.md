# 何时不使用 JavaScript 箭头函数的 3 个示例

> 原文：<https://betterprogramming.pub/3-examples-of-when-not-to-use-javascript-arrow-functions-90eebfbf7bb0>

## 没有放之四海而皆准的语法

![](img/0668c0d211b9392f2681e0980a1f24bc.png)

照片由[缺口编号](https://unsplash.com/@jannerboy62?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[防溅板](https://unsplash.com/s/photos/arrow?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上拍摄

# ES6 箭头功能

你知道他们，你爱他们，你可能一直在使用他们！作为 2015 年 ECMAScript 6 更新的一部分，arrow 函数大受欢迎，这是有充分理由的。Arrow 函数语法是非常棒的语法糖，它消除了对以下内容的需求:

*   return 关键字(对于单行函数)
*   function 关键字
*   波形括号

箭头函数还降低了 JavaScript 函数作用域中的一些复杂性，以及`this`关键字，因为有时您真正需要的只是一个匿名函数。

尽管如此，arrow 函数并不是一个适合您在编写 JavaScript 函数时遇到的所有需求的万能解决方案。让我们深入探讨一下箭头函数不是正确答案的几种情况。

# 对象方法

假设您想要创建一个绑定到对象的方法。

在这个例子中，如果我们调用`mario.oneUp()`，我们期望`mario.lives`的值从 3 增加到 4。然而，正如目前所写的，无论调用多少次`oneUp()`，值`lives`都将保持不变。

这是为什么呢？答案是`this`！

正如 MDN 所说:

> 箭头函数没有自己的`this`。使用封闭词法范围的`this`值；箭头函数遵循正常的变量查找规则。因此，在搜索当前作用域中不存在的`this`时，一个 arrow 函数最终从它的封闭作用域中找到了`this`。

在我们的例子中，封闭范围是`window`对象。调用`oneUp()`会要求程序增加`window`对象中`lives`的值。不存在这样的值，所以代码不起作用。

相反，我们希望使用传统的函数语法，这将把函数的`this`绑定到调用该函数的特定对象:

# 对象原型

以下是我们将在本例中使用的 JavaScript 代码片段:

我们在第 15 行的函数调用会产生如下结果:

```
true
*undefined*
```

我们定义了`speak()`原型函数，并为新的`Robot`对象传入了一个标语，那么为什么这段代码的结果是未定义的呢？

《T2》揭示了原因。如你所见，当我们要求控制台评估是否`(this === window)`时，它返回`true`。这为我们在前面的对象方法示例中讨论的内容提供了证据。

当使用需要上下文的函数时，我们必须使用常规函数语法，以便正确绑定`this`:

# 动态语境

这是我们的最后一个例子:

到目前为止，您可能已经意识到这段代码不能工作，以及为什么它不能工作。给你个提示:又和`this`有关。

Arrow 函数语法在函数声明时静态地绑定上下文**，这与我们在使用事件处理程序或事件监听器时试图实现的相反，事件处理程序或事件监听器本来就是动态的**。****

当通过事件处理程序或监听器操作 DOM 时，触发的事件指向属于目标元素的`this`。

对于在全局执行上下文中定义的箭头函数，`this`将指向`window`。所以在上面的代码中，`this.classList`会对`window.classList`求值，产生一个`TypeError`。

# 就这些了，伙计们！

我希望这些例子容易理解。如果没有，我建议阅读 JavaScript 中的`this`,帮助你理解何时使用或不使用箭头函数。

一如既往，感谢阅读。