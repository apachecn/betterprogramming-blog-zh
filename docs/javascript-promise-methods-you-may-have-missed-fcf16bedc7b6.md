# 你可能错过的承诺方法

> 原文：<https://betterprogramming.pub/javascript-promise-methods-you-may-have-missed-fcf16bedc7b6>

## 组合承诺和处理竞争条件

![](img/72fc536b9f070c9c05e36e0ab61478b2.png)

照片由[马太·亨利](https://unsplash.com/@matthewhenry?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

承诺是开发 JavaScript 应用的核心。因此，有许多承诺方法可用，允许我们组合多个承诺并从中获得各种结果。

在这篇文章中，我们将看看其中的一些以及如何使用它们。

# 通过`'Promise.all()'`进行分叉和合并计算

我们可以使用`Promise.all`并行运行多个承诺。它接受一个包含一个或多个承诺的 iterable 对象，并使用所有承诺结果的数组进行解析。它返回一个承诺，该承诺解析为一个数组，其中包含每个承诺的解析结果。

如果拒绝任何承诺，则拒绝返回的承诺，同时拒绝第一个承诺的原因。

例如，我们可以如下使用它:

```
(async () => {
  const [res1, res2] = await Promise.all([
    Promise.resolve(1),
    Promise.resolve(2),
  ])
  console.log(res1, res2);
})()
```

上面的代码有一个包含两个承诺的数组作为`Promise.all`的参数。

然后，我们可以使用析构赋值语法从数组中获取解析后的值。

因此，我们从控制台日志中记录了`1 2`。

在运行`Promise.all`之前，我们还可以将数组数据映射到承诺，如下所示:

```
(async () => {
  const promises = [1, 2].map(n => Promise.resolve(n));
  const [res1, res2] = await Promise.all(promises)
  console.log(res1, res2);
})()
```

这为我们从零开始创建 promise 数组节省了一些输入。

我们可以用`try...catch`捕捉错误，如下所示:

```
(async () => {
  try {
    const promises = [1, 2].map(n => Promise.resolve(n));
    const [res1, res2] = await Promise.all(promises)
    console.log(res1, res2);
  } catch (ex) {
    console.log(ex);
  }
})()
```

# 通过`'Promise.race()'`暂停

`Promise.race`也接受一个数组，但是它返回一个承诺，这个承诺是用承诺组中第一个承诺的解析值来解析的。

如果承诺数组为空，那么`Promise.race()`永远不会解决。

例如，我们可以如下使用它:

```
(async () => {
  const promises = [1, 2].map(n => Promise.resolve(n));
  const res = await Promise.race(promises)
  console.log(res);
})()
```

由于`Promise.resolve(1)`首先解析，因此`res`为`1`。

# ' Promise.allSettled()'

`Promise.all()`如果任何承诺在此之前被拒绝，则不会运行其他承诺。为了等待它们全部运行并获得所有承诺的结果，不管它们是被解决还是被拒绝，我们必须使用`allSettled`方法。

例如，我们可以如下使用它:

```
(async () => {
  const res = await Promise.allSettled([
    Promise.resolve(1),
    Promise.reject(2),
  ])
})()
```

`res`是:

```
[
  {
    "status": "fulfilled",
    "value": 1
  },
  {
    "status": "rejected",
    "reason": 2
  }
]
```

我们看到了每一个承诺的`status`——不管是实现了还是被拒绝了。

我们也看到承诺的价值，以及承诺被拒绝的原因。

# 结论

有多个承诺，让我们在组中运行承诺。我们可以运行`Promise.all`和`Promise.allSettled`。`allSettled`等待返回所有承诺结果。

`Promise.race`以已解决的第一个承诺的已解决值进行解决。