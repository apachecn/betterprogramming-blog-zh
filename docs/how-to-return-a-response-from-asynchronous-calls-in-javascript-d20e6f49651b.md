# 如何在 JavaScript 中从异步调用返回响应

> 原文：<https://betterprogramming.pub/how-to-return-a-response-from-asynchronous-calls-in-javascript-d20e6f49651b>

## 在异步函数中利用承诺

![](img/b10704a671422195e2515a7c80f8c1ff.png)

照片由[安杰洛在](https://unsplash.com/@angeloabear?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 Abear 拍摄。

不熟悉异步编程的人通常会对以下情况感到困惑:

虽然这个例子展示了 AJAX 调用的一个特定用例，但它代表了一个更一般的模式:同步返回异步函数的结果。虽然在 JavaScript 中将异步调用转换成同步调用是不可能的，但是我将向您展示如何在不破坏异步编程背后的思想的情况下解决您的问题。

# 异步函数的类型

在我们下结论之前，我们应该快速看一下最常见的异步函数类型。它们的不同之处在于它们提供异步收集的值的方式(或者仅仅是它们如何完成它们应该做的事情)。有三种突出的方法可以做到这一点:

## 1.复试

一个函数可能会将回调函数作为参数，并在完成时用要求的值调用这个函数。这方面的一个例子是前面显示的`ajaxCall`函数，它将回调函数作为其第一个参数。每当`ajaxCall`完成它的 HTTP 请求时，这个回调函数就会被调用。此外，回调函数将请求的结果作为其第一个参数。这就是你获得价值的方式。

## 2.承诺

ECMAScript 6 (ES2015)带来了承诺。承诺是从异步函数立即(同步)返回的对象，使您能够跟踪该函数的状态。承诺可以处于以下状态之一:

*   `pending`:(初始)既不履行也不拒绝
*   `fulfilled`:操作成功完成。
*   `rejected`:操作失败。

承诺值得拥有自己的文章。所以如果你想了解更多，可以查看一下 [Promise API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 。

## 3.异步/等待

`[async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)`是 Promise API 的语法糖。它更容易阅读，看起来更同步。您可以通过使用它来防止长的和嵌套的承诺链。

# 返回结果

我答应告诉你如何解决图示的问题。最干净的解决方法是返回一个承诺，而不是直接的结果。这将使函数的调用者能够在结果到达时立即处理它。我们将在这里使用`async/await`。

先说试镜。我们将再次查看我们示例的修改版本:

这里发生了什么事？我们正在回报一个承诺。如上所述，这是一个立即返回的对象，可用于跟踪异步调用的状态。每当我们调用`resolve`函数时，我们发出异步调用已经完成的信号(承诺已经*解决*)。我们在这里不处理错误的情况。然而，要指出错误，只需调用`reject`函数即可。`async/await`语法是处理承诺的一种简洁方式。如你所见，我们可以简单地`await`由`myFunction`返回的承诺。这读起来几乎像同步代码。

有一点需要考虑:你只能在声明为`async`的函数中使用`await`。因此，如果你在程序的全局执行上下文中，你需要将你的代码包装成一个异步的[生命](https://developer.mozilla.org/en-US/docs/Glossary/IIFE):

## 承诺呢？

如果你的异步函数已经使用了承诺，那只会变得更容易:

这是因为`async`函数只是一个返回承诺的函数。

# 结论

不可能真正将异步函数转换成同步函数。然而，你不需要。使用`async/await`，你可以用一种读起来几乎像同步代码的方式组织你的代码，并且你不会失去异步代码提供的灵活性。

如果您发现自己处于想要始终同步您的异步代码的情况，我建议您更熟悉异步编程的概念，因为您可能没有最佳地使用它。

# 参考

*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Statements/async _ function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)