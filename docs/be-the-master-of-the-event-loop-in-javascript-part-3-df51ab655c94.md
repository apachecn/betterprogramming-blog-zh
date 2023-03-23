# 成为 JavaScript 中事件循环的主人(第 3 部分)

> 原文：<https://betterprogramming.pub/be-the-master-of-the-event-loop-in-javascript-part-3-df51ab655c94>

## XMLHttpRequest、fetch 和 Axios

![](img/500499e65030d3a88c42ff2944f1818b.png)

照片由 [Aaron Andrew Ang](https://unsplash.com/@aaronandrewang?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这是 JavaScript 事件循环的最后一个系列。如果你没有看过我之前的帖子，我建议你先看看那些。

*   第 1 部分— [宏任务、微任务、执行上下文、事件队列和 rAF](https://medium.com/better-programming/be-the-master-of-the-event-loop-in-javascript-part-1-6804cdf6608f)
*   第 2 部分— [事件冒泡、捕获和委托](https://medium.com/better-programming/be-the-master-of-the-event-loop-in-javascript-part-2-54637d49889f)

在这篇文章中，我将讨论 JavaScript 中的异步函数。

*   XMLHttpRequest
*   取得
*   Axios

# XMLHttpRequest

JavaScript 允许您以异步方式获取数据。最古老、最经典的方法是使用`XMLHttpRequest`。这是一个构造函数，允许你向服务器发送一个 HTTP 请求。

```
const oReq = new XMLHttpReqeust();
```

现在`oReq`拥有了 *XMLHttpRequest* 对象。要发出 HTTP 请求，您应该首先打开请求。

```
oReq.open('GET', 'https://jsonplaceholder.typicode.com/todos/1');
```

XHR 方法不区分大小写。所以，你甚至可以把它写成“geT”，因为在这个过程中它会被大写。然后，您可以将请求发送到服务器。

```
oReq.send();
```

为了接收请求的响应，您应该将一个回调函数附加到 *XMLHttpRequest* 对象上。

```
oReq.addEventListener('load', function () {
  console.log(this.responeText);
});// Result
{
  "userId": 1,
  "id": 1,
  "title": "delectus aut autem",
  "completed": false
}
```

请注意，您应该使用普通函数而不是箭头函数，因为普通函数的`this`绑定到 XMLHttpRequest，而箭头函数绑定到`window`。XMLHttpRequest 的`responseText`在`e`的原型链中不存在。

```
'responseText' in e === false
```

但是，你不觉得很奇怪吗？我在以前的文章中解释过 JavaScript 中有几种类型的任务。但是为什么叫异步函数呢？

在关于 *XMLHttpRequest* 发送的 [WHATWG 文档中是这样描述的:](https://xhr.spec.whatwg.org/#the-send()-method)

1.  *设置发送()标志。*
2.  如果同步标志未置位，则:
    …
    -处理网络任务源上的任务队列。
    -如果自上次调用这些步骤后已经过了大约 50 毫秒，则终止这些步骤。

我们应该关注**任务队列**和**50 毫秒**。这听起来不像是我们一起调查过的事情吗？是的。这是一个*超时*！所以我们知道 *XMLHttpRequest* 实际上使用了 *setTimeout* 来执行。如果 *XMLHttpRequest* 使用 *setTimeout* ，那么它应该被无限*承诺*阻塞，因为它们是微任务。

我在 Codepen 中运行了这个例子；记录`this.responseText`的总时间约为 114，000 毫秒。这意味着即使 *XMLHttpRequest* 是一个异步函数，仍然有办法阻止它的执行，所以您应该将代码放在正确的位置。

# 取得

*fetch* 是一个较新的 API，它具有 *XMLHttpRequest* 所没有的额外特性。它返回一个*承诺*，该承诺解析对该请求的响应，不管它是否成功。

用*取出*:

*   缓存 API 可用于请求和响应对象。
*   *获取*不会收到跨站 cookies。
*   *获取*不会发送 cookies。
*   fetch 中的*承诺*不会拒绝 HTTP 错误状态。

只有 *XMLHttpRequest* 可以:

*   中止请求。
*   报告进展。

另一个区别是 IE 不支持 fetch，因为它使用了 IE 也不支持的 *Promise* 。所以如果你应该支持旧的浏览器，你应该使用 *XMLHttpRequest* 。

MDN 说有一个允许你中止请求的界面，但它说这是一项实验性的技术。它不支持 IE，但部分支持 Safari。从[这里](https://developer.mozilla.org/en-US/docs/Web/API/AbortController)查看。

*fetch* 给你一些更方便的方法来检查你的响应状态。

`res.json()`将响应解析为 JSON 对象。如果你使用 *Promise，*你应该再次返回`res.json()`来获得下一个`.then`链中正确的 JSON 对象。如果用`async`和`await`，可以简单的加上`await`。

```
// Promise
fetch(url)
  .then(res => {
    return res.json();
  })
  .then(data => {
    console.log(data);
  });// async & await
const res = await fetch(url);
const data = await res.json();
console.log(data);
```

在 *fetch* 里面，可以看到是怎么描述的。

要在*请求*上排队获取任务以运行操作，请运行以下步骤:

1.  如果请求的客户端为空，则终止这些步骤。
2.  使用网络任务源，将任务排队以在请求的客户端的负责事件循环上运行操作。

我们应该看到的是“任务排队”。在这个上下文中，“将任务排队”意味着将任务添加到任务队列中。由于*获取*返回一个*承诺*，所以这是一个微任务。但是你可以在`then`得到结果。有很多微任务在运行怎么办？

就像我们在 *XMLHttpRequest* 中试验的一样， *fetch* 也被阻塞了许多毫秒，直到前面的微任务完全执行完。

# Axios

Axios 是一个适用于浏览器和节点的现代 HTTP 请求库。它拥有来自用户的超过 67K 颗星。它吸取了 *XMLHttpRequest* 和 *fetch* 的精华。

所以，使用 *Axios* 的方法其实很简单容易。这与如何使用*获取*非常相似。您将一个 URL 作为第一个参数传递给 Axios，但是相反，您应该使用正确的 HTTP 方法，比如 *Axios.get* 。

```
// Promise 
axios.get('/user?ID=12345')
  .then(function (response) {
    // handle success
    console.log(response);
  })
  .catch(function (error) {
    // handle error
    console.log(error);
  })
  .finally(function () {
    // always executed
  });// async & await
async function getUser() {
  try {
    const response = await axios.get('/user?ID=12345');
    console.log(response);
  } catch (error) {
    console.error(error);
  }
}
```

# 结论

JavaScript 事件是一个非常复杂和困难的话题，但是如果你每天都使用 JavaScript，你应该很了解它。我尽力解释了我所知道的 JavaScript 事件。我希望我至少能对你的理解有一点点帮助！

# 本系列的更多内容

*   [阅读成为 JavaScript 中事件循环的主人(第 2 部分)](https://medium.com/better-programming/be-the-master-of-the-event-loop-in-javascript-part-2-54637d49889f)
*   [阅读成为 JavaScript 中事件循环的主人(第 3 部分)](https://medium.com/better-programming/be-the-master-of-the-event-loop-in-javascript-part-3-df51ab655c94)

# 资源

*   [XMLHttpRequest — MDN](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)
*   [XMLHttpRequest 接口— WhatWG](https://xhr.spec.whatwg.org/#interface-xmlhttprequest)
*   [Rest API 示例站点— JsonPlaceHolder](https://jsonplaceholder.typicode.com/)
*   [获取— MDN](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
*   [获取 API 与 XMLHttpRequest-stack overflow](https://stackoverflow.com/questions/35549547/fetch-api-vs-xmlhttprequest)
*   [流产控制员— MDN](https://developer.mozilla.org/en-US/docs/Web/API/AbortController)
*   [fetch — JavaScript.info](https://javascript.info/fetch)
*   [Axios — Github](https://github.com/axios/axios)