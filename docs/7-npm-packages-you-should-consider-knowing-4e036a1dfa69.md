# 你应该知道的 7 个 NPM 套餐

> 原文：<https://betterprogramming.pub/7-npm-packages-you-should-consider-knowing-4e036a1dfa69>

## 你可能会喜欢这些包裹

![](img/98789d9701db26f0efb136c8929be914.png)

由 [Safar Safarov](https://unsplash.com/@codestorm?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

Npm 代表*节点包管理器*。当您使用 JavaScript 时，您可以使用 npm 将其他人的代码包安装到您的项目中。npm 上有成千上万的软件包。

在本文中，我将讨论基本库。

# 1.日期-fns

我要说的第一个库是 date-fns。日期函数也称为日期函数。这个库是数百个函数的集合。

使用这些功能，您可以给日期添加天数、比较日期、格式化日期等。你可以在这个库中得到任何与`Date`函数相关的东西。

默认情况下，JavaScript 日期不好用。它们没有内置那么多功能。Date-fns 是一个库，它添加了你需要的所有函数。

以下是一些示例代码:

[来源](https://www.npmjs.com/package/date-fns)

[](https://www.npmjs.com/package/date-fns) [## 日期-fns

### 现代 JavaScript 日期实用程序库

www.npmjs.com](https://www.npmjs.com/package/date-fns) 

# 2.Moment.js

另一个日期格式化程序是 [Moment.js](https://momentjs.com/) 。这是一个免费的开源 JavaScript 库，不需要直接使用原生 JavaScript `Date`对象。

根据 [WebFX](https://www.webfx.com/blog/web-design/javascript-dates-moment-js/#:~:text=js?-,Moment.,lot%20easier%20to%20work%20with.) 的说法，“这个库是`Date`对象的包装器(就像 jQuery 是 JavaScript 的包装器一样)，使得对象更容易使用。”

有些人认为 Moment.js 更好，因为它允许您只导入您需要的那部分`Date`函数。

[](https://www.npmjs.com/package/moment) [## 瞬间

### 一个 JavaScript 日期库，用于解析、验证、操作和格式化日期。Moment.js 是一个遗留项目…

www.npmjs.com](https://www.npmjs.com/package/moment) 

# 3.Dotenv

它没有依赖性，这意味着这个库会非常小，因为你只会在开发中使用它。

这很好，因为您不必下载一堆不同的节点模块来处理您的应用程序。它的零依赖模块将环境变量从一个`.env`文件加载到`[process.env](https://nodejs.org/docs/latest/api/process.html#process_process_env)`。

![](img/3e7bef1ebe0085c0faf3a3db002f8462.png)

[来源](https://www.npmjs.com/package/dotenv)

[](https://www.npmjs.com/package/dotenv) [## dotenv

### Dotenv 是一个零依赖模块，它将环境变量从. env 文件加载到 process.env 中。正在存储…

www.npmjs.com](https://www.npmjs.com/package/dotenv) 

# 4.插座。超正析象管(Image Orthicon)

插座。IO 使得在服务器和所有不同的客户端之间进行 WebSocket 实时通信变得轻而易举。

但是这个库到底是做什么的呢？

如果我们去[看文档](https://socket.io/docs/v4/index.html)，我们可以看到我们需要建立一个基础服务器。插座。IO 构建在 WebSockets API 和 Node.js 之上。

它包括:

*   Node.js 服务器。
*   一个用于浏览器的 JavaScript 客户端库。

## 也可以用其他语言实现

*   [Java](https://github.com/socketio/socket.io-client-java)
*   [C++](https://github.com/socketio/socket.io-client-cpp)
*   [Swift](https://github.com/socketio/socket.io-client-swift)
*   [镖](https://github.com/rikulo/socket.io-client-dart)
*   [Python](https://github.com/miguelgrinberg/python-socketio)
*   [。网络](https://github.com/Quobject/SocketIoClientDotNet)

以下是一些示例代码:

[来源](https://www.npmjs.com/package/socket.io)

[](https://www.npmjs.com/package/socket.io) [## socket.io

### 插座。IO 支持基于事件的实时双向通信。它包括:在其他方面的一些实现…

www.npmjs.com](https://www.npmjs.com/package/socket.io) 

# 5.UUID

UUID 被称为“shortid”，非常受欢迎。它主要用于创建简短的非顺序 URL 友好的唯一 id。

它非常安全，因为加密的强随机值创建唯一的 id。UUIDs 通常用于标识需要在系统或网络中唯一的信息。

您不希望数据库集中控制记录的标识。

以下是生成 UUID 的一些示例代码:

[来源](https://www.npmjs.com/package/uuid)

[](https://www.npmjs.com/package/uuid) [## uuid

### 对于 RFC4122 UUIDs 的创建完整-支持 RFC4122 版本 1、3、4 和 5 UUIDs 跨平台-支持…

www.npmjs.com](https://www.npmjs.com/package/uuid) 

# 6.Axios

Axios 是大家最喜欢的`fetch`替代品。它用于创建 web 请求，非常类似于`fetch`，但是它非常容易使用。

如果你去 npm publisher，他们说 Axios 是一个“基于 promise 的浏览器和 Node.js 的 HTTP 客户端”，这意味着 Axios 可以轻松地向 REST 端点发送异步 HTTP 请求并执行 CRUD 操作。

因此，如果你想支持旧的浏览器，Axios 是一个很好的方法。

以下是一些示例代码:

[来源](https://www.npmjs.com/package/axios)

[](https://www.npmjs.com/package/axios) [## axios

### 基于 Promise 的浏览器和 node.js 的 HTTP 客户端

www.npmjs.com](https://www.npmjs.com/package/axios) 

# 7.类名

这是一个简单的小图书馆。它允许您根据指定的条件轻松组合不同的类名。

下面是一些 React 代码示例:

[来源](https://www.npmjs.com/package/classnames)

[](https://www.npmjs.com/package/classnames) [## 类名

### 一个简单的实用程序，用于有条件地将类名连接在一起

www.npmjs.com](https://www.npmjs.com/package/classnames) 

# 结论

我希望这篇文章对你有用。感谢您的阅读。