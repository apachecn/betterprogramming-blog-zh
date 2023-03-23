# ForEach 的 JavaScript 异步/等待陷阱

> 原文：<https://betterprogramming.pub/javascript-async-await-pitfalls-with-foreach-d55dddc42d0>

## 构建无错误的应用程序

![](img/eabd554247defbf4f38afff4fa3cb798.png)

艾伦·德拉克鲁兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

JavaScript 中一个常见的陷阱是将`async/await`与`forEach`结合使用。让我们看一个简单的异步函数(`asyncPrint`)将一个值打印到控制台的例子。假设我们想通过`forEach`对数组中的每个值顺序调用它:

我们可能会得到以下输出:

```
1
2
3
```

然而，它不是这样工作的。我们可以通过在打印前随机延迟执行`asyncPrint`来指出问题。如果您运行该代码片段几次，您会发现输出以任意顺序出现:

# 有什么问题？

`forEach`期望同步功能。这意味着它将*而不是* 等待我们传递的异步函数。如果我们不这样假设，我们可能会认为该循环等同于以下代码:

```
await asyncPrint(1);
await asyncPrint(2);
await asyncPrint(3);
```

但是现在我们知道了`forEach`是如何工作的，我们可以看到这个循环更类似于:

```
asyncPrint(1);
asyncPrint(2);
asyncPrint(3);
```

# 如何解决

但是我们如何才能实现我们最初希望代码做的事情呢？虽然这个问题有几种解决方案，但最简单的是使用`for..of` 语句:

这将产生我们想要的输出。

# 结论

虽然有文件证明`Array.prototype.forEach()`确实需要同步函数，但是如果您不知道它，它仍然会导致错误。问题是，你可以很容易地传递一个异步函数，你不会得到任何警告。因此，您很可能会通过它，并假设您的代码会工作，但它不会。

既然您已经了解了这一点，那么确保只传递异步函数，如果您明确地不希望它们被顺序调用的话。请注意，并不是每个阅读您代码的开发人员都能理解您的意图。

# 参考

*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Array/forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)
*   [https://tc39.es/ecma262/#sec-array.prototype.foreach](https://tc39.es/ecma262/#sec-array.prototype.foreach)