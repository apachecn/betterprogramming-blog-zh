# 快速浏览 Node.js 16 特性

> 原文：<https://betterprogramming.pub/a-quick-look-at-the-node-js-16-features-d616e8b2f29>

## Node.js 16 新特性的细节，包括稳定的定时器承诺 API，以及 V8 JavaScript engine 9.0 特性

![](img/9e11bad4308822e3d4fe95d93ef58638.png)

照片由[纳丁·沙巴纳](https://unsplash.com/@nadineshaabana?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Node.js 主版本每六个月更新一次。新版本变成了为期六个月的`Current`版本，这给了库作者时间来添加对他们的支持。六个月之后，奇数版本，比如 15，变得不受支持，偶数版本，比如 16，移动到`Active LTS`(长期支持)状态，并准备好供一般使用。`LTS`发布通常保证关键缺陷将在总共 30 个月内得到修复。生产应用程序应该只使用`Active LTS`或`Maintenance LTS`版本。

[Node.js 16](https://nodejs.org/en/blog/release/v16.0.0/) 发布于 2021 年 4 月 20 日。其当前状态为`Active LTS`。它有许多主要特性:

*   稳定的计时器承诺 API
*   V8 JavaScript 引擎更新至 V8 9.0
*   工具链和编译器升级

让我们来探索它们是什么以及如何使用它们。

# 使用 NVM 浏览节点

在[之前的一篇文章](/how-to-use-nvm-to-manage-node-js-17-and-npm-8-2da8bf3ca5e9)中，我们提供了关于使用 [NVM(节点版本管理器)](https://github.com/nvm-sh/nvm)管理 Node.js 和 NPM 版本的说明。

运行命令安装节点 16.0.0:

在任何窗口上，运行命令以使用节点 16:

现在我们准备探索:

# 稳定的计时器承诺 API

timers promises API 提供了一组返回 promise 对象的计时器函数。这些功能是在 Node.js 15 中作为实验性特性添加的，它们在这个版本中成为稳定的特性。

定时器承诺 API 包括三个 API:`setTimeout`、`setImmediate`和`setInterval`。

## 设置超时

`setTimeout`(第 3 行)在履行承诺前延迟若干毫秒。

[我们已经解释过 ES 模块使用](/what-might-be-coming-in-npm-9-6985cf2678a6) `[.mjs](/what-might-be-coming-in-npm-9-6985cf2678a6)` [扩展](/what-might-be-coming-in-npm-9-6985cf2678a6)。执行代码:

虽然承诺本身会立即执行，但承诺会在 2 秒后如期实现。

## `setImmediate`

`setImmediate`(第三行)立即履行诺言。

执行代码:

很明显，承诺马上兑现。

## 设置间隔

`setInterval`(第 6 行)返回一个异步迭代器，它以延迟的毫秒为间隔生成值。

执行代码:

`for`循环执行了 10 次。每次包括第一次都延迟`100ms`。

# V8 JavaScript 引擎更新至 V8 9.0

在 Node.js 16 中，V8 JavaScript 引擎从 Node.js 15 中的 8.6 版本更新为 [V8 9.0](https://v8.dev/blog/v8-release-90) 。以下是主要特征:

*   正则表达式匹配索引
*   更快的超级属性访问
*   `for (async of`禁止

## 正则表达式匹配索引

正则表达式匹配索引是 ES2022 中的新特性。它提供正则表达式的捕获字符串的开始和结束索引。

正则表达式使用`d`作为标志来生成子串匹配的索引(第 1 行)。

它打印出如下生成的索引:

`[0, 8]`是匹配，`[0, 4]`是第一捕获组，`[4, 8]`是第二捕获组。

## 更快的超级属性访问

访问`super`属性，例如`super.something`，已经通过使用内嵌缓存系统和优化的代码生成在涡扇中进行了优化。随着这些变化，`super`属性访问现在更接近于常规属性访问。

## ``` for(不允许```的异步

令牌序列`for (async of`不明确。它可以是`for (async of => {};;)`或`for (async of foo)`的前缀。因此，这是不允许的。

同时，`for await (async of`仍然是允许的，因为没有`for (await (;;)`，因此没有歧义。

# 工具链和编译器升级

Node.js 通常为许多不同的平台提供预构建的二进制文件。Node.js 16 增加了一些内容:

*   苹果芯片的预构建二进制文件。
*   macOS 安装程序(`.pkg`)以“fat”(多架构)二进制文件的形式提供。
*   为英特尔(`darwin-x64`)和 ARM ( `darwin-arm64`)架构提供单独的 tarballs。

# 结论

Node.js 16 有许多新特性和改进。它目前的状态是活跃的 LTS。

如果您想了解其他版本的特性，请阅读以下文章:

*   [node . js19 的 6 大特色](/6-major-features-of-node-js-19-b98e28b9670c)
*   [node . js 18 的 5 大特色](/5-major-features-of-node-js-18-5f4a164cc9fc)
*   [node . js 17 的 3 大特色](/3-major-features-of-node-js-17-4bee7135df02)
*   [node . js 15 的新功能](/whats-new-in-node-js-15-fc24e87e2590)

感谢阅读。我希望这有所帮助。如果你有兴趣，可以看看[我的其他媒体文章](https://jenniferfubook.medium.com/jennifer-fus-web-development-publications-1a887e4454af)。