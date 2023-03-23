# JavaScript 中的函数式编程和管道函数

> 原文：<https://betterprogramming.pub/functional-programming-and-the-pipe-function-in-javascript-c92833052057>

## 什么是管道，我们如何使用它？

![](img/486056de52bf3df7f4b4c9da4b98514e.png)

约翰·汤纳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

当你想做一件事，但是在一个函数中很复杂，你怎么解决这个问题？如果你想把功能分成很多块，但又想做得更优雅，该怎么办？

[TC39 GitHub 上有一个有趣的提议——](https://github.com/tc39/proposal-pipeline-operator)[提议——管道——运营商](https://github.com/tc39/proposal-pipeline-operator) [第一阶段](https://github.com/tc39/proposal-pipeline-operator)。虽然它仍然是悬而未决的，因为它提到的，建议的想法是很有趣的看看。

在这篇文章中，我将谈论管道和管道，这对于函数式编程是很重要的，以及如何编写它们。

# 阅读前

这篇文章的前提是你了解 JavaScript 基础知识和`Array.prototype`中的一些内置方法。

如果您不熟悉这些，我建议您阅读关于它们的文档。而且，我制作管道函数的方式可能与 JavaScript 世界中的其他管道不同。但核心概念不会如此不同。

*   [在 MDN 中减少](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)。
*   [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)中的箭头功能。

另外，我将向不了解它的人解释`pipe`以及如何一步一步地创建它。

# JavaScript 中管道的定义

所以，首先，让我们谈谈 JavaScript 中的管道是什么。听到“烟斗”你会想到什么？是的，你说得对。天花板上的一堆管道用来输气什么的，或者地下的水管。

这些管道的作用是作为资源从 A 到 b 的通道。除了作为资源通过的空间之外，它对资源没有任何作用。

JavaScript 中管道函数的想法来自于现实世界中的管道。[什么是](https://whatis.techtarget.com/definition/pipe)解释什么是管道如下:

> “管道只是单向通信。基本上，管道将一个参数(如一个进程的输出)传递给另一个进程，后者接受它作为输入。

# 纯函数

对于 pipe 来说，这是一个非常重要的概念。什么是纯函数？纯函数是做以下事情的函数。

1.  使用相同的输入返回相同的输出。
2.  不改变其功能范围之外的任何值。

但是为什么纯函数对 pipe 很重要呢？正如我在上面解释的，管道将一个给定的值传递给下一个管道。但是如果函数可以返回不同的值，那么管道中的下一个函数就不能保证稳定的结果。

# 不带管道

好的。让我们看一个简单的例子。

我们有两个函数，`addFour`和`minusFive`。我们可以很容易地从每个函数中得到一个回报。当我们想同时加减数字时，我们可以这样做:

```
addFour(minusFive(0)); // -1
```

然后，乘以 10。

```
multiplyByTen(addFour(minusFive(0))); // -10
```

但是因为我们不喜欢负数，所以我们想把它改成正数。

```
Math.abs(multiplyByTen(addFour(minusFive(0)))); 10
```

你怎么看待这个过程？这看起来不是很乱吗？尤其是在语句的末尾有如此多的右括号。

当然，它不会导致任何编程错误或问题。但是这可能会降低代码的可读性。

# 如何写管道

要让管道工作，您应该让函数连续运行。在`Array`类中，有一个非常有用的方法，`Array.prototype.reduce`。`reduce`所做的是循环遍历一个数组，并产生一个紧凑的结果。

例如，如果你想得到所有学生的总分，你可以这样写你的函数，如下所示:

```
const scores = [90, 100, 40, 50, 10];
let total = 0;scores.forEach(score => total += score); 
console.log(total); // 290
```

这个例子使用了`forEach`，因为`map`或`filter`产生了一个新的数组。但是这个例子有一个问题，因为`total`必须用`let`声明，因为`total`已经在`forEach`的循环中被改变了。

然而，相比之下，`reduce`返回一个通过循环计算的值。

```
const scores = [90, 100, 40, 50, 10];
const total = scores.reduce((acc, cur) => acc + cur, 0);
console.log(total); // 290
```

如果你没有得到这个区别，请查看[文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)。

## 第一步。将所有我们想要入队的函数放入 reduce 中

```
const pipe = (funcs) => {
  return funcs.reduce((acc, cur) => {
    ...
  });
}
```

好了，现在怎么办？`func.reduce`的每个参数必须返回下一个函数需要的值。

从下一个函数的角度来看，应该传入它的参数应该来自于前面对`reduce`的调用。在`reduce`中，`acc`是返回值，`cur`是数组的当前值。

很好。然后，为了更好的可读性，让我们更改参数的名称。

```
funcs.reduce((**res, func**) => {
  ...
});
```

## 第二步。填充身体部位

现在你知道每个返回值都将是下一个函数的参数。为了实现这一点，我们应该运行带有参数`res`的函数。

```
funcs.reduce((res, func) => {
  **return func(res);**
});
```

但是，这还是挺奇怪的。为什么？即使这些函数可以使用它们需要的参数正确执行，如果第一个函数也想要使用参数呢？

在这个例子中，由于`reduce`的`initialValue`不存在，数组的第一个元素将被用作`initialValue`，这将是一个函数，它将从执行中被省略。

然后，数组的第二个元素将被用作循环的第一个`cur`。

所以，你需要为数组中的第一个函数设置`initialValue`，但是作为什么呢？您没有将任何值放入`pipe`。

## 第三步。设置`InitialValue`

我们将取该函数的值。

```
const pipe = (funcs, **v**) => {
  return funcs.reduce((res, func) => {
    return func(res);
  }, **v**);
};
```

现在，让我们试着运行这个函数，看看它是否像预期的那样工作。

```
const addFive = v => v + 5;
const identity = v => v;console.log(pipe([
  addFive,
  identity
], 5)); 
// 10
```

有用！但是，实际上有一个问题，因为`pipe`只接受一个数组。所以，你不能像下面这样运行`pipe`函数:

```
pipe(addFive, 5);
//   ~~~
// Uncaught TypeEror: funcs.reduce is not a function
```

## 第四步。一般化

看起来如果`pipe`也能有一个单一的功能就更好了。此时，我将使用 rest 参数。

rest 参数的作用是允许函数将任何参数作为数组。点击此处查看[其余参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)。

```
// Rest parameter must be last formal parameter !!
const pipe = (v, **...funcs**) => {
  return funcs.reduce((res, func) => {
    return func(res);
  }, **v**);
};
```

现在，让我们看看它是否有效。

```
const subtract = v => v - 5;console.log(pipe(10, subtract)); // 5
```

它工作了，但是现在为第一个调用的第一个参数传递到`pipe`中的值应该是第一个参数。因为根据 rest 参数的规则，我们改变了参数的顺序。

我不喜欢这样。那么，我们该如何解决这个问题呢？

我们将使用闭包。闭包是一个函数，它返回对已经执行完的外部函数的引用。如果你不熟悉 closure，你可能想看看我以前的帖子。

[](https://medium.com/better-programming/execution-context-lexical-environment-and-closures-in-javascript-b57c979341a5) [## JavaScript 中的执行上下文、词汇环境和闭包

### 你应该知道的高级 JavaScript 概念

medium.com](https://medium.com/better-programming/execution-context-lexical-environment-and-closures-in-javascript-b57c979341a5) 

所以，最后，`pipe`可以这样修改:

```
const pipe = (**...**funcs) => **v** => {
  return funcs.reduce((res, func) => {
    return func(res);
  }, **v**);
};pipe(add)(5) // 10
```

现在，`pipe`返回另一个采用`v`的函数。直到返回函数取`v`，它才完成`reduce`。作为最后一步，让我们确认它是否正常工作。

## 最后一步:测试

```
const minusFive = v => v - 5;
const addFour = v => v + 4;
const multiplyByTen = v => v * 10;
const identity = v => v;const res = pipe(
  minusFive,
  addFour,
  multiplyByTen,
  Math.abs,
  identity
)(0);console.log(res); // 10\. it works !
```

# 结论

`pipe`是函数式编程中非常有用的函数之一。它降低了阅读代码的复杂性，相反，增加了可读性。

需要记住的重要一点是，将与`pipe`一起使用的每个函数都应该是一个纯函数。

# 资源

*   [TC39 管道 GitHub 上的操作员](https://github.com/tc39/proposal-pipeline-operator)
*   [编程中的管道—什么是](https://whatis.techtarget.com/definition/pipe)
*   [MDN 中的其余参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)