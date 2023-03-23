# JavaScript 承诺的最佳实践

> 原文：<https://betterprogramming.pub/javascript-best-practices-promises-45928fbfebe2>

## 用承诺清理代码

![](img/5d228b3a2a18f4442ee0b5dbedbe1346.png)

照片由[马太·亨利](https://unsplash.com/@matthewhenry?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/patterns?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

编写 JavaScript 代码的一些方法比其他方法更好。

在本文中，我们将研究使用最新 JavaScript promise 特性的最佳实践。

# 将非承诺异步代码转换为承诺

我们可以用`util.promisify()`方法将非承诺的异步代码转换成承诺。这让我们可以用带有表单签名的异步回调函数转换许多函数，表单签名为`(err, res)`，其中`err`是一个有错误的对象，`res`有异步代码的结果。

任何定义了承诺形式的函数也可以使用这个实用程序返回承诺形式。只要将承诺版本存储为符号属性`util.promisify.custom`的值，我们就可以得到承诺版本。

例如，由于`setTimeout`在 Node.js 的标准库中有一个基于 promise 的版本，我们可以将其转换为 promise，如下所示:

```
const util = require('util');
const sleep = util.promisify(setTimeout);(async () => {
  await sleep(1000);
  console.log('slept');
})()
```

现在我们有了一个`sleep`函数，它可以在我们想要的时间暂停执行，而不必嵌套回调。

# 异步链接

在老方法中，我们用`then`方法链接承诺。

例如，我们写道:

```
promise1
  .then((res) => {
    //...
    return promise2
  })
  .then((res) => {
    //...
    return promise3
  })
  .then((res) => {
    //...
  })
```

我们可以用`async`和`await`来清理，如下所示:

```
(async () => {
  const val1 = await promise1;
  //...
  const val2 = await promise2;
  //...
  const val3 = await promise3;
  //...
})();
```

这要干净得多，因为 resolve promise 值被分配给左侧的常量或变量。

# 使用 Catch 捕获错误

与同步代码一样，我们必须捕捉错误并优雅地处理它们。

为了用 promise 代码做到这一点，我们可以调用`catch`方法。例如:

```
promise1
  .then((res) => {
    //...
    return promise2
  })
  .then((res) => {
    //...
    return promise3
  })
  .then((res) => {
    //...
  })
  .catch((err) => {
    //...
  })
```

如果我们使用`async`和`await`，那么我们就像使用同步代码一样使用`try...catch`，如下所示:

```
(async () => {
  try {
    const val1 = await promise1;
    //...
    const val2 = await promise2;
    //...
    const val3 = await promise3;
    //...
  } catch (ex) {
    //...
  }
})();
```

两个例子中的代码`catch`将在第一个承诺被拒绝时运行。

# 使用 Finally 运行无论承诺的结果如何都应该运行的代码

如果我们需要运行应该运行的代码，而不管 promise 链的结果如何，那么我们应该对 async 和 await 代码使用`finally`方法或`finally`块。

例如，我们可以如下运行`finally`中的清理代码:

```
promise1
  .then((res) => {
    //...
    return promise2
  })
  .then((res) => {
    //...
    return promise3
  })
  .then((res) => {
    //...
  })
  .finally((err) => {
    //...
  })
```

或者我们可以写:

```
(async () => {
  try {
    const val1 = await promise1;
    //...
    const val2 = await promise2;
    //...
    const val3 = await promise3;
    //...
  } catch (ex) {
    console.log(ex);
  } finally {
    //...
  }
})();
```

# 使用 Promise.all()的多个异步调用

如果我们想同时运行多个不相关的承诺，那么我们应该使用`Promise.all`。

例如，我们可以一次运行它们，如下所示:

```
Promise.all([
    promise1,
    promise2,
    promise3,
  ])
  .then((res) => {
    //...  
  })
```

如果我们用`async`和`await`，我们可以写:

```
(async () => {
  const [val1, val2, val3] = await Promise.all([
    promise1,
    promise2,
    promise3,
  ])
})();
```

在这两个例子中，所有三个承诺的解析值要么在`res`中，要么被分配给左边的一个数组(在第二个例子中)。

这样，它们会同时运行，而不是等待每个问题解决。

# 摘要

*   我们可以通过使用承诺来清理我们的异步代码。
*   通过使用`util.promisify`方法，可以使用节点应用程序将异步代码转换为承诺。
*   如果我们想链承诺，之前一定要添加`catch`方法或块来优雅地处理错误。
*   `finally`无论承诺的结果如何，方法或块都可以用来运行一直运行的代码。
*   对于同时履行不相关的承诺来说是很棒的。