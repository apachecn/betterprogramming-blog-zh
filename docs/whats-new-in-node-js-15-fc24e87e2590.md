# Node.js 15 的新特性

> 原文：<https://betterprogramming.pub/whats-new-in-node-js-15-fc24e87e2590>

## Node.js 15 新特性的详细信息，包括抛出未处理的拒绝和 V8 8.6 语言特性

![](img/f13595b53e5c7bda966d01d9af7fe222.png)

图片来源:作者

[Node.js 15](https://nodejs.org/en/blog/release/v15.0.0/) 发布于 2020 年 10 月 20 日。它有许多主要特性:

*   抛出未处理的拒绝
*   V8 8.6 语言功能
*   [NPM 7](https://medium.com/better-programming/the-step-by-step-guide-to-understanding-and-adopting-npm-7-914504f7090f)
*   QUIC 的实验支持
*   N-API 版本 7
*   异步本地存储 API 的改进

让我们来探索它们是什么以及如何使用它们。

# 使用 NVM 浏览节点

在[之前的一篇文章](https://medium.com/better-programming/use-nvm-to-manage-node-js-and-npm-versions-2bd0d0875f9f)中，我们提供了关于使用 [NVM(节点版本管理器)](https://github.com/nvm-sh/nvm)管理 Node.js 和 NPM 版本的说明。在我们的环境中，我们安装了 Node.js `12.16.0`和 NPM `6.14.8`。通过运行`nvm install node`，我们安装了 Node.js `15.4.0`和 NPM `7.0.15`。

我们打开了两个窗口，一个设置为 Node.js 12，另一个设置为 Node.js 15。

在`node12`窗口上:

```
$ nvm use 12
Now using node v12.16.0 (npm v6.14.8)
```

在`node15`窗口上:

```
$ nvm use 15
Now using node v15.4.0 (npm v7.0.15)
```

现在我们准备探索。

# 抛出未处理的拒绝

每当一个承诺被拒绝，并且在事件循环的一个循环中没有错误处理程序附加到该承诺时，就会发出`unhandledRejection`事件。从 Node.js 15 开始，`unhandledRejection`的默认模式从`warn`更改为`throw`。在`throw`模式下，如果未设置`unhandledRejection`挂钩，则`unhandledRejection`会作为未捕获的异常出现。

创建一个程序，使承诺被拒绝，并显示一条错误消息:

当您在`node12`窗口上运行此代码时，它会显示一条很长的警告消息:

```
$ node myPromise.js
(node:79104) UnhandledPromiseRejectionWarning: #<Object>
(node:79104) UnhandledPromiseRejectionWarning: Unhandled promise rejection. This error originated either by throwing inside of an async function without a catch block, or by rejecting a promise which was not handled with .catch(). To terminate the node process on unhandled promise rejection, use the CLI flag `--unhandled-rejections=strict` (see [https://nodejs.org/api/cli.html#cli_unhandled_rejections_mode](https://nodejs.org/api/cli.html#cli_unhandled_rejections_mode)). (rejection id: 1)
(node:79104) [DEP0018] DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.Users that have an unhandledRejection hook should see no change in behavior, and it’s still possible to switch modes using the --unhandled-rejections=mode process flag.
```

在`node15`窗口上运行这段代码，它抛出错误`UnhandledPromiseRejection`:

```
$ node myPromise.js
node:internal/process/promises:227
          triggerUncaughtException(err, true /* fromPromise */);
          ^[UnhandledPromiseRejection: This error originated either by throwing inside of an async function without a catch block, or by rejecting a promise which was not handled with .catch(). The promise rejected with the reason "#<Object>".] {
  code: 'ERR_UNHANDLED_REJECTION'
}
```

在下面代码的`then`子句中添加一个错误处理程序(`.catch((`错误`) => console.log(`错误`.error))`也可以)。

现在，代码可以在`node12`和`node15`窗口上正确运行:

```
$ node myPromise.js
The call is rejected with an error
```

最佳实践是为承诺编写一个错误处理程序。但是，会有没有捕捉到错误的情况。设置`[unhandledRejection](https://nodejs.org/api/process.html#process_event_unhandledrejection)` [钩子](https://nodejs.org/api/process.html#process_event_unhandledrejection)来捕捉潜在的错误是个好主意。

`unhandledRejection`钩子对 Node.js 12 和 Node.js 15 都有效。有了那个设定，`unhandledRejection`就可以妥善处理了。

```
$ node myPromise.js
reason is { error: 'The call is rejected with an error' }
promise is Promise { <rejected> { error: 'The call is rejected with an error' } }
```

# V8 8.6 语言功能

V8 JavaScript 引擎已从 8.4 更新到 8.6。除了性能调整和改进，V8 更新还带来了以下语言特性:

*   `Promise.any()`和`AggregateError`(从 V8 8.5 开始)
*   `await setTimeout`和`AbortController`(实验)
*   `String.prototype.replaceAll()`(来自 V8 8.5)
*   逻辑赋值运算符`&&=`、`||=`和`??=`(来自 V8 8.5)

## Promise.any()和 AggregateError

首先，我们来看看现有的`Promise.all()`方法。

`[Promise.all()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)`将可迭代的承诺作为输入，并返回一个承诺，该承诺解析为输入承诺的结果数组。

以下程序对两个已解决的承诺调用`Promise.all()`:

当所有输入的承诺都已解析时，或者如果输入 iterable 不包含承诺，则`Promise.all()`返回的承诺将被解析:

```
$ node myPromise.js
[
  { data: 'The data from 5000 ms delay' },
  { data: 'The data from 100 ms delay' }
]
```

下面的程序对两个被拒绝的承诺调用`Promise.all()`。

`Promise.all()`立即拒绝任何输入的承诺拒绝或非承诺抛出错误，并将拒绝第一个拒绝消息或错误:

```
$ node myPromise.js
{ error: 'The error from 100 ms delay' }
```

`[Promise.any()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/any)`是 Node.js 15 中新增的。这是`[Promise.all()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)`的反义词。它接受一个可迭代的承诺，一旦可迭代承诺中的一个承诺实现，就返回一个承诺，该承诺的值将被解析。

下面的程序对两个已解决的承诺调用`Promise.any()`:

`[Promise.any()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/any)`返回第一个已解决的承诺:

```
$ node myPromise.js
{ data: 'The error from 100 ms delay' }
```

下面的程序对两个被拒绝的承诺调用`Promise.any()`:

如果 iterable 中没有承诺被履行——即所有给定的承诺都被拒绝——返回的承诺将被拒绝，并带有一个`AggregateError`,它是`Error`的一个新子类，将各个错误组合在一起。

```
$ node myPromise.js
[AggregateError: All promises were rejected]
[
  { error: 'The error from 5000 ms delay' },
  { error: 'The error from 100 ms delay' }
]
```

## 等待 setTimeout 和 AbortController

在前面的例子中，我们在 promise 调用中使用了`setTimeout`。`WindowOrWorkerGlobalScope`的`setTimeout`使用回调。然而，`timers/promises`提供了`setTimeout`的一个有前途的版本，可以和 async/await 一起使用。

`[AbortController](https://developer.mozilla.org/en-US/docs/Web/API/AbortController)`是一个 JavaScript 对象，允许我们根据需要中止一个或多个 web 请求。我们以[使用同步](https://medium.com/better-programming/the-power-and-convenience-of-useasync-e4a3e4934143)为主题给出了如何使用`AbortController`的例子。

`await setTimeout`和`AbortController`都是实验特征。

## String.prototype.replaceAll()

首先，我们来看看现有的`String.prototype.replace()`方法。

`[replace()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace)`返回一个新字符串，其中某个模式的部分或全部匹配被替换。模式可以是字符串或正则表达式。替换可以是一个字符串，也可以是每次匹配时调用的函数。

如果模式是一个字符串，只有第一个匹配项会被替换。

```
'20+1+2+3'.replace('+', '-');
```

执行上述语句将产生`“20–1+2+3”`。

为了用`‘-’`替换所有的`‘+'`，必须使用一个正则表达式。

```
'20+1+2+3'.replace(/\+/g, '-');
```

执行上面的语句会产生`“20–1-2-3”`。

`[replaceAll()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replaceAll)`是 Node.js 15 中的新特性，可以避免使用正则表达式。它返回一个新的字符串，其中所有与某个模式匹配的*都被替换掉了。模式可以是字符串或正则表达式，替换可以是字符串或每次匹配都要调用的函数。*

有了`replaceAll()`，我们不必使用正则表达式将所有的`‘+'`替换为`‘-’`。

```
'20+1+2+3'.replaceAll('+', '-');
```

执行上述语句将产生`“20–1-2-3”`。

## 逻辑赋值运算符&&=、||=和？？=

Node.js 15 中增加了一些逻辑赋值操作符。

[逻辑 AND 赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND_assignment) ( `x &&= y`)运算符仅在`x`为真时赋值。`x &&= y`等同于`x && (x = y)`，不等同于`x = x && y`。

[逻辑 OR 赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR_assignment) ( `x ||= y`)运算符仅在`x`为假时赋值。`x ||= y`等同于`x || (x = y)`，不等同于`x = x || y`。

[逻辑无效赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_nullish_assignment) ( `x ??= y`)运算符仅在`x`为无效(`null`或`undefined`)时赋值。`x ??= y`相当于`x ?? (x = y)`，不等同于`x = x ?? y`。

# 其他变化

除了抛出未处理的拒绝和 V8 8.6 语言特性，Node.js 15 还有以下变化:

*   **NPM 7** :有很多变化，包括对等依赖自动安装，包和线程锁文件增强，工作区支持等等。这在[另一篇文章](https://medium.com/better-programming/the-step-by-step-guide-to-understanding-and-adopting-npm-7-914504f7090f)中有记载。
*   **QUIC**:UDP 传输协议的实验性支持，它是 HTTP/3 的底层传输协议。它具有 TLS 1.3、流量控制、纠错、连接迁移和多路复用的内置安全性。
*   **N-API 版本 7** :用于构建原生插件的 API。独立于底层 JavaScript 运行时，并作为 Node.js 本身的一部分进行维护。
*   **异步本地存储 API 的改进**:为大规模应用提供更复杂的日志记录和分析功能。

# 结论

Node.js 15 有很多新的特性和改进，包括突破性的改变。

如果您想了解更新的版本，可以看看下面的文章:

*   [node . js 19 的 6 大特色](/6-major-features-of-node-js-19-b98e28b9670c)
*   [node . js18 的 5 大特色](/5-major-features-of-node-js-18-5f4a164cc9fc)
*   [node . js 17 的 3 大特色](/3-major-features-of-node-js-17-4bee7135df02)
*   [快速浏览 Node.js 16 特性](/a-quick-look-at-the-node-js-16-features-d616e8b2f29)

感谢阅读。我希望这有所帮助。如果你有兴趣，可以看看[我的其他媒体文章](https://jenniferfubook.medium.com/jennifer-fus-web-development-publications-1a887e4454af)。