# TypeScript 的新顶级正在等待

> 原文：<https://betterprogramming.pub/typescripts-new-top-level-await-cc6beadbde8>

## 我们不再需要异步函数包装器了

![](img/1a78ada505040005cb063833bbd01351.png)

Joshua Aragon 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

如果你想在 JavaScript 内部使用`async` / `await`，一般来说，你必须先定义一个`async`函数，然后才能使用`await`。

在本文中，我想向您展示最终使用顶级的`await`而不是将其包装在一个`async`函数之间是可能的。

**注:** *从 Typescript 3.8 开始发布👍以及所有* [*主流浏览器支持顶级恭候*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await#top_level_await) *。*

# async/await 是如何工作的？

在以前的一篇文章中，我写了早期使用 XHR 直到现在使用 Fetch API 进行 Ajax 调用的历史。

[](https://medium.com/better-programming/from-xhr-to-fetch-with-async-await-on-the-finish-line-b021de1d226b) [## 从 XHR 到终点的异步/等待取货

### 看看 JavaScript 中请求的发展和历史

medium.com](https://medium.com/better-programming/from-xhr-to-fetch-with-async-await-on-the-finish-line-b021de1d226b) 

承诺是`async` / `await`必不可少的一部分。没有它们，我们无法使用它们。

JavaScript 中的大多数现代环境(如 HTTP 请求)是异步的，许多现代 API 返回承诺。

虽然这在使操作非阻塞方面有很多好处，但它使某些用于加载文件或外部内容的代码可读性较差。

```
fetch("...")
    .then(response => response.json())
    .then(data => { console.log(data) });
```

人们习惯于在 JavaScript 中使用带有`async`函数的承诺来使用`await`。

但这会在定义之后马上执行函数，感觉怪怪的。它忽略了使用函数的意义。

> 我在一个不和谐服务器上聚集了几个来自世界各地的有抱负的开发者，如果你想加入，请随意。

# 顶级等待

最好使用`await`而不要将其包装在`async`函数中。

随着 [TypeScript 3.8](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-8.html#top-level-await) 的发布，我们收到了这个特性。考虑到它只在 Chrome 中被本地支持。

要使用它，我们必须创建一个模块。当它通过`export`导出东西时，它就是一个模块。

```
const response = await fetch("...");
const data = await response.json();
console.log(data);export {};
```

如果你问我，这段代码可读性更好。因为我们不必创建一个`async`函数，所以也节省了一点代码。

*在*[*MDN Web Docs*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules)*阅读更多关于 JavaScript 模块的内容。*

# 类型脚本配置

要在 TypeScript 中使用顶级的`await`，必须将`target`编译器选项设置为`es2017`或更高。必须将`module`选项设置为`esnext`或`system`。

# 结论

我认为 TypeScript 团队在语言中内置顶级`await`是非常棒的。

你怎么想呢?你要用它吗？请在评论中让我知道。

# 谢谢！

![](img/e02317848f6e08e98764dcc3057acf80.png)

读完这个故事后，我希望你学到了一些新的东西，或者受到启发去创造一些新的东西！🤗

如果我给你留下了问题或一些要说的话作为回应，向下滚动并给我键入一条消息。如果你想保密，请在 Twitter @DevByRayRay 上给我发个 DM。我的 DM 永远是开放的😁

## [**通过电子邮件获取我的文章点击这里**](https://byrayray.medium.com/subscribe) **|** [**购买 5 美元中等会员**](https://byrayray.medium.com/subscribe)

# 阅读更多

![RayRay](img/992af170033696163d6cc0269218aedd.png)

[雷雷](https://byrayray.medium.com/?source=post_page-----cc6beadbde8--------------------------------)

## 荒诞的故事

[View list](https://byrayray.medium.com/list/angular-stories-24674407532a?source=post_page-----cc6beadbde8--------------------------------)6 stories![](img/b94f2b7d2929c90566cd2dd6f657a751.png)![](img/02b73423a62d73b113af9fdf9629c79f.png)![Stacked books](img/b02a2f57c0093e04ab1d11d3a55f35ea.png)![RayRay](img/992af170033696163d6cc0269218aedd.png)

[雷雷](https://byrayray.medium.com/?source=post_page-----cc6beadbde8--------------------------------)

## 最新的 JavaScript 和 TypeScript 故事

[View list](https://byrayray.medium.com/list/latest-javascript-typescript-stories-0358ad941491?source=post_page-----cc6beadbde8--------------------------------)14 stories![](img/c93ca03b33796c40dcc47873de2697c2.png)![](img/86f37efa11855f6f0f0f62984c37f696.png)![](img/ddbaa6d0bea676316247e82043d60b63.png)