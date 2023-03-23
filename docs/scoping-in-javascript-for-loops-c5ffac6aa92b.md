# JavaScript For 循环中的作用域

> 原文：<https://betterprogramming.pub/scoping-in-javascript-for-loops-c5ffac6aa92b>

## 让 vs. var 进入你的循环

![](img/ba4e3ffb24b2bdd00ef9f491c145b14d.png)

米卡·科尔霍宁在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我偶然发现了两个代码片段，这让我想到了 for 循环中的作用域和一个可能的误解。第一个看起来如下:

你可以不时看到它来演示事件循环，闭包的概念，或者作为一个面试问题。该代码的输出是:

```
3
3
3
```

变量`i`在调用`console.log()`的匿名函数的闭包中被捕获，并通过`setTimeout()`被异步调用。因此调用被添加到事件循环中，在调用时，捕获的变量`i`已经增加到 3。

到目前为止，一切顺利。但是下面的代码呢？

输出是:

```
0
1
2
```

那么`let`在这里改变了什么呢？您可能有理由认为这与作用域有关。我们知道由于 JavaScript 中的提升，`var`关键字只支持函数作用域。`let`另一方面，允许块范围的变量。但是`for`循环的作用域究竟是如何工作的呢？

一个常见的误解是该循环有一个作用域，因此它等同于下面的`while`循环:

请注意循环周围立即调用的函数表达式(IIFE ),它覆盖了整个循环。然而，上面的`while`循环仍然会给出一个`3 3 3`的输出，所以之前显示的`for`循环不能等同于它。原因是`for`循环对于每次迭代都有自己的作用域。如果我们使用 Babel 将`for`循环(使用`let`)从上向下传输到 ES5，我们可以通过查看生成的代码很好地看到这一点:

在第 8 行中，我们可以看到每个循环迭代都是通过调用一个带有循环变量`i`的函数来表示的。因此`i`的一个副本在`_loop()`的功能范围内，这意味着它不会受到第 7 行原`i`的后续更改的影响。

我希望我能给你一些见解，对你的面试、日常工作或者只是娱乐有用。

# 参考

*   [MDN Web 文档:用于语句](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for)