# 重构提取到异步/等待

> 原文：<https://betterprogramming.pub/promises-with-async-await-605645a6c0e8>

## 清理您的代码，使您的 API 请求更容易理解和调试

![](img/5f5eee488e494bbc15995b1a87f6a683.png)

戴夫·弗朗西斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

`Async/await`是过去几年中添加到 [JavaScript](https://hackernoon.com/tagged/JavaScript?ref=hackernoon.com) 中的最具革命性的特性之一。它提供了一个直观的替代品来代替承诺有时会出现的语法混乱。

优秀的老式[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 没有任何问题，它在默认情况下可用于发出 ajax 请求等等。

> “ [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 提供了一个 JavaScript 接口，用于访问和操作 HTTP 管道的各个部分，比如请求和响应。它还提供了一个全局`[fetch()](https://developer.mozilla.org/en-US/docs/Web/API/GlobalFetch/fetch)`方法，该方法提供了一种简单、逻辑的方式来通过网络异步获取资源。”— [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)

一个基本的`fetch`请求很容易建立。看一下下面的代码:

# 异步/等待 101

`Async/await`是一种相对较新的(所谓 ECMAScript 2017 JavaScript edition 的一部分)编写异步代码的方式。以前异步代码的替代方法是回调和承诺。Async/await 实际上只是建立在承诺之上的语法糖，它使异步代码看起来和行为更像同步代码。一些人认为这使得发现异步代码更加困难，可能需要一些时间来适应。

需要注意的是，`await`关键字不能在`async`函数之外使用。

## 接受异步/等待的理由

*   它使代码简洁明了。
*   它允许我们避免嵌套代码。
*   使用`try/catch`可以处理同步和异步错误。
*   调试要容易得多(我确信我不用告诉你去想办法在`fetch`函数中把调试器放在哪里)。

## 通过三个简单的步骤将 fetch 转换为 async/await

下面是我们将为这个示例转换的函数:

识别异步函数并将`async`放在它的前面:

```
async function fetchAlbums() {
...
}
```

确定函数中所有不同的承诺，并在每个承诺前添加`await`。将它们分配给变量:

```
const res = await fetch(apiUrl)
const json = await res.json()
```

重构其余部分，使其看起来同步:

```
async function fetchAlbums() {
    const response =  await fetch(apiUrl)
    const json = await response.json()
    console.log(json)
}
```

使用`async/await`不是为使用`function`关键字的函数保留的。我们也可以将它用作箭头函数，尽管语法略有不同:

```
const fetchAlbums = async () => {
    const response = await fetch(apiUrl)
    const json = response.json()
    console.log(json)
}
```

它也可以是一个匿名函数，如下例所示，我们的异步函数是传递给 Google auth 配置中的`passport.use` 函数的参数之一。此处与`fetch`配合使用:

用`async/await`重构后: