# JavaScript Promise.all 的力量和局限性

> 原文：<https://betterprogramming.pub/the-power-and-limitations-of-javascript-promise-all-6e1b53520e50>

## 编写异步 JavaScript 时并行的力量——以及要注意的陷阱！

![](img/1f67d2199ef081c1f65159d408aad9ea.png)

Alberto Barrera 在 [Unsplash](https://unsplash.com/s/photos/promises?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

在许多 web 开发项目中，重要的功能依赖于异步代码——无论是向 API 发出 HTTPS 请求、查询和更新数据库，还是管理像读取大文件这样的时间敏感任务。

但是你能确保你的异步代码尽可能高效地运行吗？

当我刚接触异步 JavaScript 时，我经常`await`每个承诺，却没有意识到我在代码中制造了不必要的瓶颈。一旦明白了`Promise.all`的一些好处，我就经历了一个过度使用的阶段。在特定的场景中，我的代码变得不一致，因为我试图并行地做一些相互影响的事情，并且最先发生的任务是最快的！

在本文中，我们将探究并行的威力，同时也要注意过度使用`Promise.all`的危险。

# 如何承诺。所有作品

我们从一个基本的例子开始。首先，我们将创建一个从`1`到`100`的数字数组:

```
const nums = new Array(100).fill(1).map((num, i) => i + 1);
```

接下来，我们将创建一个需要 50 毫秒完成的异步函数。为了简化示例，我们将依靠`useTimeout`来模拟 API 调用，而不是实际发出 HTTP 请求:

```
const asyncDouble= num => {
  return new Promise((resolve, reject) => setTimeout(() => {
    return resolve(num * 2);
  }, 50));
}
```

我们的异步函数接受一个数，并将其加倍。现在让我们假设我们想为数组中的每一项调用这个函数。

如果这是同步代码，我们可能会选择`map`:

```
const doubledNums = nums.map(num => num * 2);
```

但是如果我们使用我们的`asyncDouble`函数来尝试，会发生什么呢？

```
const doubledNums = nums.map(async (num) => await asyncDouble(num));
```

尽管我们已经添加了`async`和`await`关键字，但还是有一个问题。我们登录到控制台的`doubledNums`充满了未兑现的承诺！那是因为`map`不是用来处理承诺的。

另一种选择是使用`for`循环，就像这样:

```
const doubledNums = [];for (const num of nums) {
  doubledNums.push(await asyncDouble(num));
}
```

现在我们得到了我们想要的结果，但是对`promiseFunc`的每个调用只有在前一个调用解决之后才发生。需要五秒钟才能得到最终结果。如果我们需要查询 100 万个值，那将需要将近 14 个小时！

这就是`Promise.all`的用武之地。它以并行方式触发每个承诺，并且仅在所有其他承诺都已解决时才解决:

```
const doubledNums = await Promise.all(nums.map(num => asyncDouble(num)));
```

现在，并行运行，这需要大约 50 毫秒——与单个`promiseFunc`实例的时间长度相同。与依次履行每个承诺相比，你节省了很多时间。

# 我们一次能解决多少承诺？

我们能把`Promise.all`推多远，一次能触发多少承诺？当然，这取决于一系列因素，如处理能力和承诺的复杂性。

假设我们有处理能力，并且我们的承诺可以并行运行，那么有一个超过 200 万个承诺的硬限制。

如果我们深入研究 Chrome 和 Node.js 底层的 JavaScript 引擎 V8 的代码，我们可以看到长度为`2**21`或更长的数组会触发`TooManyElementsInPromiseAll`错误。因此，长度 2，097，150 是可以接受的，而 2，097，151 会引发错误。

我们中很少有人会在这个极限附近工作。然而，这并不意味着我们总是可以并行运行承诺。一个更常见的问题是尝试并行运行相互依赖的承诺，而不是担心规模。

# 承诺的局限性是什么？

最后，警告一句。当我还是一个经验不足的开发人员时，我因为过于自由地使用`Promise.all`而在尝试优化数据库 CRUD 操作时遇到了困难。即使一个函数不直接依赖于另一个函数的结果，它们仍然可能以执行顺序很重要的方式影响相同的字段。

让我们想象一个简单的 MongoDB 数据库，其中包含比萨饼类型、它们的价格(以美分计)以及它们在菜单上的顺序:

```
[
  {
    type: "[Quattro formaggi](https://en.wikipedia.org/wiki/Pizza_quattro_formaggi)",
    price: 1200,
    order: 0,
  },
  {
    type: "[H](https://en.wikipedia.org/wiki/Pizza_quattro_formaggi)awaiian",
    price: 1000,
    order: 1,
 },
  {
    type: "[M](https://en.wikipedia.org/wiki/Pizza_quattro_formaggi)argherita",
    price: 800,
    order: 2,
  },
]
```

我们希望使用三个异步函数来更新我们的定价:

*   全面提价 50%的方案。
*   另一个将 5 美元的折扣应用于夏威夷披萨。
*   第三个更新菜单顺序，从最便宜的披萨到最贵的披萨。

如果我们尝试异步运行它们会发生什么？

```
await Promise.all([
  inflatePrices(),
  discountHawaiian(),
  sortByPrice(),
]);
```

我们不能保证这些函数中的一个总是最快的，而另一个总是最慢的，这意味着承诺可以以任何顺序执行。因为这些函数相互依赖，这就给我们的最终结果带来了一致性。

如果`inflatePrices`在`discountHawaiian`执行之前执行，我们的夏威夷披萨将花费 10.00 美元。但是如果函数以相反的顺序执行，它将花费 7.50 美元。根据`sortByPrice`的说法，考虑到最终价格，这款披萨的订购可能正确，也可能不正确。

在这种情况下，更好的做法是容忍代码的性能有一个可忽略的降低，并有一个一致的、可重复的结果，如下所示:

```
await inflatePrices();
await discountHawaiian();
await sortByPrice();
```

找出相互依赖的函数并不总是那么容易。出于这个原因，对使用`Promise.all` 过度优化保持警惕是很重要的——尤其是如果您没有完全理解您的异步函数可能正在做的所有事情。

但是当你知道你可以安全地并行解决承诺的时候，`Promise.all`是一个非常有用的工具来帮助你加速你的代码！