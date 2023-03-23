# 如何在 Node 中构建实时聊天应用程序？Js 使用 Express、Mongoose 和 Socket。木卫一

> 原文：<https://betterprogramming.pub/simple-chat-application-in-node-js-using-express-mongoose-and-socket-io-ee62d94f5804>

## 创建您自己的自定义聊天应用程序的详细演练

![](img/ad3e24f5dea96783f8bdc89dfb30b1a3.png)

在本教程中，我们将使用 Node.js 平台构建一个**实时聊天应用程序**，该应用程序无需任何页面刷新即可向接收者发送和显示消息。我们将使用 JavaScript framework Express.js 以及 Mongoose 和 Socket.io 库来实现这一点。

在开始之前，让我们快速浏览一下 Node.js 的基础知识

# **Node.js**

[Node.js](https://en.wikipedia.org/wiki/Node.js) 是一个开源、跨平台的 JavaScript 运行时环境，在浏览器之外执行 JavaScript 代码。使用 Node 最重要的好处是我们可以将 JavaScript 同时作为前端和后端语言使用。

正如我们所知，JavaScript 主要用于客户端脚本，其中脚本嵌入在网页的 HTML 中，并由用户 web 浏览器中的 JavaScript 引擎在客户端运行。

Node.js 允许开发人员使用 JavaScript 编写命令行工具和服务器端脚本，即在页面被发送到用户的 web 浏览器之前，在服务器端运行脚本来生成动态网页内容。

尽管 Node 是单线程的，但在使用异步函数时，它仍然更快。例如，在从磁盘读取文件或等待 HTTP 请求完成时，Node 可以处理其他事情。异步行为可以使用回调来实现。此外，JavaScript 可以很好地处理 JSON 和非 SQL 数据库。

你可以在这里下载节点[。](https://nodejs.org/en/download/)

## **NPM 模块**

Node.js 允许在应用程序中包含库的模块。这些模块可以是用户定义的模块，也可以是第三方模块。

可以使用以下命令安装第三方模块:

```
npm install module_name
```

并且可以使用 **require()** 函数使用已安装的模块:

```
var module = require(‘module_name’)
```

在节点应用程序中，我们将使用 package.json 文件来维护模块版本。此文件可以通过以下命令创建:

```
npm init
```

软件包必须按如下方式安装:

```
npm install -s module_name
```

有许多框架可以作为模块添加到我们的节点应用程序中。这些将根据需要进一步解释。

# **构建简单的聊天应用**

我们的应用程序必须允许多个用户一起聊天。消息应该在不刷新页面的情况下更新。为了简单起见，我们将避免认证。

我们可以从创建一个新的项目目录并移入其中开始。然后，我们可以使用以下命令启动我们的项目:

```
npm init
```

这将提示我们输入项目的详细信息。

此后，将创建一个 package.json 文件:

我们的应用程序目录现在已经设置好了。

我们需要创建的第一件事是服务器。为了实现这一点，我们将使用一个名为 **Express 的框架。**

## **快递. js**

Express.js，或简称为 Express，是 Node.js 的 web 应用框架。Express [为 web 和移动应用](https://expressjs.com/)提供了一组强大的功能，以及一个基本 web 应用功能的薄层，而不会模糊 Node.js 的功能。

使用以下命令安装 Express.js:

```
npm install -s express
```

在 package.json 文件中，将添加一个新行:

```
dependencies”: {
 “express”: “⁴.16.3”
 }
```

接下来，我们将创建一个 **server.js** 文件。

在这个文件中，我们需要 require Express 并从 Express 的一个实例中创建一个对变量的引用。HTML、CSS 或 JavaScript 等静态内容可以使用 Express.js 提供:

```
var express = require(‘express’);var app = express();
```

我们可以使用代码开始监听端口:

```
var server = app.listen(3000, () => {
 console.log(‘server is running on port’, server.address().port);
});
```

现在我们需要创建一个 HTML 文件，index.html，显示我们的用户界面。我已经添加了 bootstrap 和 JQuery CDN。

请注意，空的 *<脚本></脚本>* 标签将是我们编写客户端 JavaScript 代码的地方。

为了告诉 Express，我们将使用一个静态文件。我们将在 **server.js:** 中添加新的一行

```
app.use(express.static(__dirname));
```

我们可以使用以下命令运行 server.js:

```
node ./server.js
```

或者使用一个名为 **nodemon，**的包，以便自动检测代码中所做的更改。使用以下命令下载 nodemon:

```
npm install -g nodemon
```

g 代表全局，因此它在所有项目中都是可访问的。

使用以下命令运行代码:

```
nodemon ./server.js
```

如果您转到 localhost:3000，我们可以看到索引文件:

![](img/61c62d26782753c0a7842fe0b082617b.png)

index.html

现在我们的服务器已经启动并运行，我们需要创建我们的数据库。对于这个应用程序，我们将有一个非 SQL 数据库，并将使用 **MongoDB** ，这是我在[*mlab.com*](https://mlab.com/)设置的。我们的数据库将包含一个名为 messages 的集合，包含字段 name 和 message **。**

![](img/626155c7d32be88586d87aad21a607b2.png)

为了将这个数据库连接到应用程序，我们将使用另一个名为**mongose**的包。

## **猫鼬**

Mongoose 是一个 MongoDB 对象建模工具，设计用于在异步环境中工作。可以使用以下命令安装 Mongoose:

```
npm install -s mongoose
```

在 server.js 中，我们需要 Mongoose:

```
var mongoose = require(‘mongoose’);
```

我们将分配一个变量，mlab 数据库的 URL:

```
var dbUrl = ‘mongodb://username:pass@ds257981.mlab.com:57981/simple-chat’
```

Mongoose 将使用连接方法连接到 mlab 数据库:

```
mongoose.connect(dbUrl , (err) => { 
   console.log(‘mongodb connected’,err);
})
```

我们将把我们的消息模型定义为:

```
var Message = mongoose.model(‘Message’,{ name : String, message : String})
```

我们现在可以实现聊天逻辑，但在此之前，还需要添加一个包。

## **主体解析器**

Body-Parser 提取传入请求流的整个主体部分，并在 req.body 上公开它。

使用以下命令安装它:

```
npm install -s body-parser
```

将以下代码添加到 **server.js:**

```
var bodyParser = require(‘body-parser’)
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: false}))
```

## **路由**

路由是指应用程序的端点(URIs)如何响应客户端请求。使用与 HTTP 方法对应的 Express app 对象的方法定义路由:app.get()处理 get 请求，app.post()处理 post 请求。

这些路由方法[指定了一个回调函数](https://expressjs.com/en/guide/routing.html)(有时称为“处理函数”)，当应用程序接收到对指定路由(端点)和 HTTP 方法的请求时调用这个函数。换句话说，应用程序“监听”与指定的路由和方法匹配的请求，当它检测到匹配时，它调用指定的回调函数。

现在我们需要为我们的聊天创建两条消息路径。

server.js 内部:

*   **Get** 将从数据库中获取所有消息:

*   **Post** 将用户创建的新消息发布到数据库:

为了将这些路由连接到前端，我们需要在 index.html 的客户端脚本标记中添加以下代码:

这里，sendMessage 用于调用消息的 post 路由，并保存用户发送的消息。当用户单击“发送”按钮时，会创建该消息。

类似地， **getMessage** 用于调用消息的 get 路由。这将获得保存在数据库中的所有消息，并将被追加到 messages div 中。

![](img/fc8e1c523b5bff539ceae1300bf4e6bc.png)

现在唯一的问题是客户端没有办法知道服务器是否更新了。因此，每次我们发布消息时，我们都需要刷新页面来查看新消息。

为了解决这个问题，我们可以添加一个推送通知系统，从服务器向客户端发送消息。在 Node.js 中我们使用 socket.io。

## **Socket.io**

插座。IO 是一个用于实时 web 应用程序的 JavaScript 库。[它支持 web 客户端和服务器之间的实时双向通信](https://www.tutorialspoint.com/socket.io/socket.io_overview.htm)，由两部分组成:在浏览器中运行的客户端库和 Node.js 的服务器端库。Socket.io 支持基于事件的实时双向通信。

要安装 socket.io:

```
npm install -s socket.io
```

我们还需要一个 HTTP 包来让 Socket.io 工作:

```
npm install -s http
```

将以下代码添加到 server.js 中:

```
var http = require(‘http’).Server(app);
var io = require(‘socket.io’)(http);
```

并创建一个连接:

```
io.on(‘connection’, () =>{
 console.log(‘a user is connected’)
})
```

在 index.html 的**、**中添加以下标记:

```
<script src=”/socket.io/socket.io.js”></script>
```

现在，当在 server.js 中创建消息时，我们需要创建一个发出动作。

```
app.post('/messages', (req, res) => {
  var message = new Message(req.body);
  message.save((err) =>{
    if(err)
      sendStatus(500);
    **io.emit('message', req.body);**
    res.sendStatus(200);
  })
})
```

并在客户端脚本标签中的【index.html】、**、**中添加以下代码:

```
var socket = io();socket.on(‘message’, addMessages)
```

所以每次发布消息时，服务器都会更新 message div 中的消息。

![](img/f95cf97d09a07223cd6813ddbb9d0ce0.png)

太好了！

# 结论

我们在 Node.js 中创建了一个非常基本的消息应用程序，还有很多改进的空间。我希望这有助于理解一些基本概念。完成的代码可以在[https://github.com/amkurian/simple-chat](https://github.com/amkurian/simple-chat)中找到。

server.js

# 链接和参考

[](https://socket.io/) [## 插座。超正析象管(Image Orthicon)

### 插座。IO 2.0 在这里以最快和最可靠的实时引擎为特色~/Projects/tweets/index . js var IO =…

socket.io](https://socket.io/)  [## Express - Node.js web 应用程序框架

### Express 是一个最小且灵活的 Node.js web 应用程序框架，它为 web 和…

expressjs.com](https://expressjs.com/) 

http://mongoosejs.com/