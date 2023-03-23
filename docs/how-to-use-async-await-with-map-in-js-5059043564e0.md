# 如何使用 Async/await？JavaScript 中的地图

> 原文：<https://betterprogramming.pub/how-to-use-async-await-with-map-in-js-5059043564e0>

## 并且知道`Promise.all`是否比“for of”快

![](img/3b6846914cd1c908f4ada0d395643eb9.png)

保罗·史密斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在某种程度上，您可能想知道如何在像`.map`或`.forEach`这样的方法中使用异步函数。在这个简短的指南中，您将看到最常见的错误是什么以及如何解决它们。

为此，我们将在`index.ts`文件中包含以下基本代码:

```
const usernames: string[] = ["jordanrjdev", "anonymous123", "channelyy"];const simulateFetchData = (username: string): Promise<string> => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(`${username} is a valid username`);
    }, 1000);
  });
}
```

如你所见，我们有一个用户名数组和一个函数，它接受一个参数并返回一个字符串。

现在我们将迭代用户名数组，用 map 方法获得每个用户的模拟数据:

```
const dataUsers = usernames.map(async (username) => {
   return await simulateFetchData(username);
});
console.log(dataUsers);
```

但是当执行此操作时，我们将在控制台中看到以下结果:

```
[ Promise { <pending> }, Promise { <pending> }, Promise { <pending> } ]
```

因此，为了解决这个问题，我们有两个选择，即使用`Promise.all`或使用`for of`:

# 对于...来说

我们将使用一个`for of`来解决这个非常常见的错误，它会让我们在寻找最合适的解决方案时浪费大量时间。

```
const getWithForOf = async() => {
   console.time("for of");
   const data = []
   for (const username of usernames) {
     let dataUser = await simulateFetchData(username);
     data.push(dataUser);
   }
   console.timeEnd("for of");
}
getWithForOf();
```

使用这个选项，代码将按顺序执行，因此您可以等待每个调用。这将有助于我们在进行下一次迭代之前解决每一次迭代。

请记住，如果我们进行 N 次迭代，每次迭代需要 1 秒钟来解决。在我们的例子中，这意味着执行完这部分代码总共需要 3 秒钟。由于`console.time`，我们可以在控制台输出中看到这一点:

```
for of: 3.012s
```

# 承诺。所有

现在我们将使用`promise.all`方法来解决我们的问题，所以我们将有下面的代码:

```
const getWithPromiseAll = async() => {
   console.time("promise all");
   let data = await Promise.all(usernames.map(async (username) => {
     return await simulateFetchData(username);
   }))
   console.timeEnd("promise all");
}getWithPromiseAll();
```

如您所见，我们有一个接收承诺数组的 Promise.all 方法，请记住以下代码:

```
usernames.map(async (username) => {return await simulateFetchData(username);})
```

返回一个承诺数组，这正是我们所需要的，所以我们将它传递给`Promise.all`来解决它们。

此方法将导致并行解析所有异步代码。

所以与`for of`不同，让我们看看在控制台中执行这个功能需要多长时间:

```
promise all: 980.3000000119209ms
```

因此，如果我们有 N 个异步函数，它们将被执行和解析，而无需在它们之间等待，这在某些特定情况下会派上用场。

> *有时出于性能原因，我们需要与* `*Promise.all*` *并行执行我们的承诺，其他时候我们需要与* `*for of*` *循环顺序执行。重要的是你了解每一个之间的区别，以及如何适应你的需要。*

您可以在这个代码沙盒存储库中看到完整的代码

感谢阅读。