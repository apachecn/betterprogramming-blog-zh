# Node.js 18 的 5 大特性

> 原文：<https://betterprogramming.pub/5-major-features-of-node-js-18-5f4a164cc9fc>

## Node.js 18 新特性的详细信息，包括实验性的 fetch API 和 test runner，以及 V8 JavaScript engine 10.1 特性

![](img/fb5896fb2005f21ca18b91045c4033c0.png)

凯西·霍纳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Node.js 主版本每六个月更新一次。新版本变成了为期六个月的`Current`版本，这给了库作者时间来添加对他们的支持。

六个月后，奇数版本(如 17)变得不受支持，偶数版本(如 16)转移到`Active LTS`(长期支持)状态，并准备好供一般使用。`LTS`发布通常保证关键缺陷将在总共 30 个月内得到修复。生产应用程序应该只使用`Active LTS`或`Maintenance LTS`版本。

[Node.js 18](https://nodejs.org/en/blog/release/v18.0.0/) 发布于 2022 年 4 月 19 日。它变成了`Current`版本。它有 5 个主要特点:

*   实验提取 API
*   Web 流 API
*   HTTP 超时
*   实验测试转轮
*   V8 JavaScript 引擎已更新至 V8 10.1

让我们来探索它们是什么以及如何使用它们。

# 使用 NVM 浏览节点

在[之前的一篇文章](/how-to-use-nvm-to-manage-node-js-17-and-npm-8-2da8bf3ca5e9)中，我们提供了关于使用 [NVM(节点版本管理器)](https://github.com/nvm-sh/nvm)管理 Node.js 和 NPM 版本的说明。

运行命令安装节点 18.0.0:

在任何窗口上，运行命令以使用节点 18:

现在我们准备探索:

# 实验提取 API

fetch API 启动从网络获取资源的过程，返回一个承诺，一旦响应可用，该承诺就被实现。

下面是获取语法:

```
function fetch(input: RequestInfo, init?: RequestInit): Promise<Response>
```

`input`是 URL 字符串或请求对象。`init`为可选参数，是包含`method`、`headers`、`body`、`mode`、`credentials`、`cache`、`redirect`、`referrer`、`referrerPolicy`、`integrity`、`keepalive`、`signal`自定义设置的对象。

这里有一个例子，`fetchExample.js`:

在第 2 行，`fetch`被调用到`https://catfact.ninja/fact`，它获得随机的 cat 事实。

`fetch`仅在遇到网络错误时拒绝。它不拒绝 HTTP 错误。因此，响应需要检查`response.ok`以了解获取是否成功。如果失败，`response.status`和`response.statusText`提供更多信息(第 7 行)。

如果成功，初始的`fetch`调用仅检索以下信息，包括`headers`。

要获得主体消息，必须从传入流中读取主体数据，并将其解析为 JSON(第 4 行`fetchExample.js`)。因为从 TCP 流中读取是异步的，所以`.json()`操作也以异步结束。

fetch API 从 2015 年就开始提供了，除了 Internet Explorer，所有主流浏览器都支持它。但是 Node.js 17 及更低版本不支持`fetch`。

幸运的是，Node.js 18 中添加了这种支持，尽管这是一个实验性的特性。

在 Node.js 18 中，fetch API 基于 [undici](https://undici.nodejs.org/#/) ，这是一个 HTTP/1.1 客户端，为 Node.js 从头开始编写。

可以使用`--no-experimental-fetch`命令行标志禁用获取 API。

除了`fetch`，这些类型`FormData`、`Headers`、`Request`和`Response`也被全局化。全局对象是内置对象，是 JavaScript 语言本身的一部分，可以全局访问。

# Web 流 API

以前，我们必须下载视频、图像或任何其他资源的整个文件，等待它被反序列化为合适的格式，然后在完全接收后处理整个文件。

有了 [Web Streams API](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) ，我们现在可以开始以编程方式访问通过网络接收的数据流，并按块处理它们，而不需要生成缓冲区、字符串或 blob。此外，我们可以检测流何时开始或结束，将流链接在一起，根据需要处理错误和取消流，并对流的读取速度做出反应。

Node.js 18 展示了 Web Streams API 在全局范围内的实验性实现。

这里有一个`ReadableStream`和`ReadableStreamDefaultController`的例子。`ReadableStream`代表可读的数据流，`ReadableStreamDefaultController`是`ReadableStream`的状态和内部队列的控制器。

在第 2 行，`fetch`被调用到`https://catfact.ninja/fact`，它获得随机的 cat 事实。

在第 4 行，控制台日志显示`response.body`是一个`ReadableStream`。

从可读流中获取一个读取器(第 9 行)，并返回一个新的可读流，该流对原始流进行处理(第 17–65 行)。要创建一个`ReadableStream`，需要设置三个可选的回调函数，`start`、`cancel`和`pull`。

我们的可读流实现了`start`回调(第 18–64 行)，它接收一个类型为`ReadableStreamDefaultController`的`controller`(第 18 行)。控制器可以将数据放入队列(第 40 行)并关闭可读流(第 35 行)。这个代码片段不改变任何数据，除了记录`done`状态和块值(第 43 行)，以及最终的`done`状态(第 33 行)。

在第 67 行接收到流之后，它用流内容创建一个`Response`(第 72–74 行)。

收到第 76 行的响应文本后，它会在控制台上记录消息。

这个代码片段展示了一个简单的流处理。然而，Web Streams API 支持许多高级的流处理。

Node.js 18 使以下流 API 全球可用:

*   `ReadableStream`
*   `ReadableStreamDefaultReader`
*   `ReadableStreamBYOBReader`
*   `ReadableStreamBYOBRequest`
*   `ReadableByteStreamController`
*   `ReadableStreamDefaultController`
*   `TransformStream`
*   `TransformStreamDefaultController`
*   `WritableStream`
*   `WritableStreamDefaultWriter`
*   `WritableStreamDefaultController`
*   `ByteLengthQueuingStrategy`
*   `CountQueuingStrategy`
*   `TextEncoderStream`
*   `TextDecoderStream`
*   `CompressionStream`
*   `DecompressionStream`

# HTTP 超时

`server.headersTimeout`限制解析器等待从客户端接收完整 HTTP 头的时间(毫秒)。

`server.requestTimeout`设置从客户端接收整个请求的超时值(毫秒)。

如果这些超时过期，服务器以状态 408 响应，而不将请求转发给请求监听器，然后关闭连接。

以下是在[创建生产就绪型 React 应用的实践指南](https://javascript.plainenglish.io/a-hands-on-guide-for-creating-a-production-ready-react-app-864ad98e7497)中使用和描述的代码:

我们添加第 15 行来显示`server.headersTimeout`的值。

我们还添加了第 16 行来显示`server.requestTimeout`的值。

对于 node.js 17，`server.headersTimeout`默认设置为`60000` (1 分钟)，`server.requestTimeout`默认设置为`0`。

对于 node.js 18，`server.headersTimeout`默认还是`60000` (1 分钟)，默认`server.requestTimeout`设置为`300000` (5 分钟)。

从 node.js 18 开始，两个超时都必须设置为非零值，以防止在部署服务器时前面没有反向代理的情况下出现潜在的拒绝服务攻击。

# 实验测试转轮

测试跑步者模块仍然是实验性的。我们可以导入`node:test`模块来编写单元测试，并以 [Test Anything Protocol (TAP)格式](https://testanything.org/)报告结果。我们展示几个例子来看看它是如何工作的。在某种程度上，它类似于 JavaScript 测试框架 Jest。

## 两个同步测试

以下是两个同步测试:

第一次测试通过是因为`1 = 1`。第二次试验失败是因为`1 ≠ 2`。

## 两个异步测试

以下是两个异步测试:

第一次测试通过是因为`1 = 1`。第二次试验失败是因为`1 ≠ 2`。

## 两个异步测试作为两个子测试

对于`test()`方法，我们可以添加子测试。我们将它们写成两个子测试，而不是两个异步测试。`await`用于确保两个子测试都已完成。

运行测试:

有趣的是，测试结果显示有 1 个测试(第 33 行)，它失败了(第 35 行)。这是因为任何子测试失败都会导致父测试失败。

# V8 JavaScript 引擎已更新至 V8 10.1

V8 10.1 中有许多新功能。除了改进类字段和私有类方法的性能之外，还有以下三个新特性:

*   `findLast`和`findLastIndex`数组方法
*   对`Intl.Locale` API 的改进
*   `Intl.supportedValuesOf`功能

## findLast 和 findLastIndex 数组方法

数组有查找满足某个条件的元素的方法。除了现有的从头查找元素或索引的方法外，还增加了从末尾查找元素或索引的`findLast`和`findLastIndex`。

## 对 Intl 的改进。区域设置 API

对于`Intl.Locale` API，对象中增加了七个新属性:`calendars`、`collations`、`hourCycles`、`numberingSystems`、`timeZones`、`textInfo`和`weekInfo`。

这里有一个地区的例子，`en-us`(美国英语):

这里是另一个地区的例子，`zh-cn`(中国的中文):

## 函数的 Intl.supportedValuesOf

添加了一个新函数`Intl.supportedValuesOf(code)`，为 Intl APIs 返回 V8 中支持的标识符数组。`code`参数可以是`calendar`、`collation`、`currency`、`numberingSystem`、`timeZone`或`unit`。

以下代码显示了支持的标识符:

# 结论

Node.js 18 有许多新特性和改进。在 node.js 19 发布之前都是`Current`版本。

如果您想了解其他版本的特性，请阅读以下文章:

*   [node . js 19 的 6 大特色](/6-major-features-of-node-js-19-b98e28b9670c)
*   [node . js 17 的 3 大特色](/3-major-features-of-node-js-17-4bee7135df02)
*   [快速浏览 Node.js 16 特性](/a-quick-look-at-the-node-js-16-features-d616e8b2f29)
*   [node . js 15 新增功能](/whats-new-in-node-js-15-fc24e87e2590)

感谢阅读。

```
**Want to Connect?**If you are interested, check out [my directory of web development articles](https://jenniferfubook.medium.com/jennifer-fus-web-development-publications-1a887e4454af).
```