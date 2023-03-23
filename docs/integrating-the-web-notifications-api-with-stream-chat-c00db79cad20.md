# 将 Web 通知 API 集成到我们的聊天应用中

> 原文：<https://betterprogramming.pub/integrating-the-web-notifications-api-with-stream-chat-c00db79cad20>

## 内置通知让我们的用户保持参与

![](img/4b359d429565243bd9022cdf27ef5d94.png)

如今，许多应用程序通过发送通知来告知用户与其应用程序相关的重要消息。这可以通过推送或网络。使用 web 通知，用户可以单击通知并立即返回到应用程序以进一步处理该内容。

对于[流聊天](https://getstream.io/chat/docs/)应用，用例之一可能是当新消息到达时发送通知，就像 Slack、WhatsApp、Facebook Messenger 等。

在这篇文章中，您将学习如何使用 [Web 通知 API](https://developer.mozilla.org/en-US/docs/Web/API/Notifications_API) ，这是一个 Web 规范，用于在新消息到达您的应用程序时显示通知。

![](img/a5a0f173c06eda7e49e96c77fd76f17c.png)

# 准备项目

我们将以 React 项目为例。为此，我们需要从 GitHub 中克隆一个简单的 [React 项目。](https://github.com/pmbanugo/react-conversational-ui-tutorial)

这个项目附带了一个关于用 React 和流聊天构建对话式 UI 应用程序的教程。打开命令行应用程序，在终端中键入以下命令:

# 使用通知 API

您运行的命令下载了项目并安装了运行应用程序所需的依赖项。我们将对应用程序进行修改，以便在新消息到达时通知用户。

为了正确使用 API，我们需要做的第一件事是请求用户允许我们的应用程序从浏览器发送系统通知。

通过请求许可，通知 API 将允许用户控制哪些应用程序允许显示通知，哪些不允许。做出选择后，该设置通常会在当前会话和未来很长一段时间内保持不变。

# 请求许可

Notifications API 公开了一个`requestPermission()`方法，请求向用户显示通知的权限时需要这个方法。当应用程序加载时，我们将请求许可。

在您喜欢的代码编辑器中打开项目，并打开`src/App.js`文件。

然后，将以下代码添加到该类中:

在上面的代码中，我们添加了一个名为`askNotificationPermission()`的方法，在组件挂载时会调用这个方法。

`askNotificationPermission()`方法首先检查浏览器是否支持通知 API。如果是，它调用`checkNotificationPromise()`来检查浏览器是否支持基于承诺的`Notification.requestPermission()`版本。

基于这个结果，它调用基于承诺的 API 版本或者基于回调的版本。

当用户响应权限请求时，我们调用`handlePermission(permission)`方法，该方法显式设置`Notification.permission`值，因为旧版本的 Chrome 不会自动这样做。

我们提到我们必须检查浏览器是否支持基于承诺的版本`Notification.requestPermission()`。我们通过调用`this.checkNotificationPromise()`方法做到了这一点。

让我们添加这个方法的定义:

这个方法所做的是在`Notification.requestPermission()`上调用`.then()`方法，然后如果没有抛出错误就返回 true，否则返回 false。

# 显示通知

当我们使用构造函数创建一个新的`Notification`实例时，会显示一个通知。

构造函数必须传递一个`title`参数，并且可以选择传递一个`options`对象来指定选项，比如`text direction`、`body text`等等。

让我们创建一个负责此事的方法，并在`onNewMessage`方法中调用该方法。

添加一个名为`createNotification`的新方法:

此方法接受消息作为参数。它调用带有标题的`Notification()`构造函数，并使用消息文本作为通知的正文文本。

我们现在将在`onNewMessage`方法中调用这个方法。在第 77 行后添加以下代码:

使用刚才添加的代码，只有当消息的作者不是当前登录的用户时，才会创建通知。

# 把所有的放在一起

让我们看看这在浏览器中是如何工作的。我们将运行 React 应用程序，但是我们还需要一个 API 来为流聊天客户端生成用户访问令牌。我们将使用你可以在 [GitHub](https://github.com/pmbanugo/stream-chat-boilerplate-api) 上找到的项目。

按照自述文件中的说明设置并启动应用程序。启动后，通过在命令行运行`yarn start`来启动 React 应用程序。

![](img/f56bd5a67048d5cf05c619ecb7797906.png)

# 这是一个总结！

你有它！您学习了如何使用[通知 API](https://developer.mozilla.org/en-US/docs/Web/API/Notifications_API) 来显示通知。

要使用这个 API，首先需要通过调用`Notification.requestPermission()`获得用户的许可，然后使用`Notification()`构造函数创建通知，传递给它一个标题和一个选项对象。

本教程的代码是 100%开源的，可以在 [GitHub](https://github.com/pmbanugo/react-conversational-ui-tutorial/tree/notification-api) 上获得。