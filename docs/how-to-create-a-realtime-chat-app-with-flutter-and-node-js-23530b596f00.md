# 如何用 Flutter 和 Node.js 创建实时聊天应用

> 原文：<https://betterprogramming.pub/how-to-create-a-realtime-chat-app-with-flutter-and-node-js-23530b596f00>

## Flutter 前端遇到 Node.js 后端

![](img/d34c9c638a138130c73f79a7135dc94c.png)

在这篇博客中，我们将看到如何制作一个以 Node.js 为后端，以 Flutter 为前端的实时聊天 app。我们将使用套接字在设备之间进行通信。

## Node.js(服务器端)

让我们创建一个名为`chat_server`的新项目，并在终端中运行它。现在运行这个命令来启动项目:

```
touch index.js && npm init && npm install ws && npm install express
```

这将为您完全安装项目。完成后，在您最喜欢的 IDE 中打开项目，并打开您的`index.js`文件。

这是我们的基本 Websocket 服务器，现在它并没有做太多事情。让我们现在改变这一点。

这将完全运行您的消息！现在这已经完成了，我们现在可以开始使用我们的 Flutter 聊天应用程序了。

## 颤动(客户端)

我们现在已经完成了我们的后端，所以我们可以开始在颤振聊天应用程序。运行这个命令来启动我们新的颤振项目:

```
flutter create chat_app && cd chat_app && flutter run
```

在你的 IDE 中打开这个项目，打开你的`pubspec.yaml`文件，我们将添加`web_socket_channel` 确保它看起来像这样:

```
dependencies:
  flutter:
    sdk: flutter
  cupertino_icons: ^1.0.2
  **web_socket_channel**: ^2.1.0 # Add this.
  **sqflite**: ^2.0.2 # And this.
```

要开始我们的项目，请删除`lib/main.dart`中的所有内容，并确保插入以下内容:

现在让我们创建一个名为`messages.dart`的新文件，我们将在其中存储消息。因为我们将在本地存储我们的消息，所以我们将首先为此创建一个函数。

代码真多啊。这个函数的作用是检索我们数据库中的每一条信息。然后，它会检查消息是否已由您发送或接收。

如果已经完成，那么最后我们将显示消息。现在我们将在`messages.dart`中创建另一个函数，它将向我们的服务器发送一条消息，并将一条新消息插入我们的数据库。

这个函数将存储我们将要发送的消息。至此，我们将创建一个页面来显示这些消息。

我们将这个页面称为`chat_page.dart`，它看起来像这样。

现在，为了接收消息，我们想为此创建一个函数。我们将调用我们的函数`getMessage()`这将把我们自己链接到服务器并监听任何消息。如果有消息，我们也会显示出来。

这就是全部，现在你完全创建了一个聊天应用程序。现在在两台本地设备上运行这个应用程序，看看它是否能工作。如果它不起作用，请让我知道，我会帮助你。感谢阅读。