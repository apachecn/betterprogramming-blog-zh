# 用不到 50 行代码构建与 Phoenix 和 LiveView 的实时聊天

> 原文：<https://betterprogramming.pub/real-time-chat-with-phoenix-liveview-in-fewer-than-50-lines-of-code-1b2cf8af7301>

## Phoenix 和 LiveView 如何让客户端之间可扩展的实时通信变得简单

![](img/f8a0efc52542d8750d981f50a426088a.png)

由[马雷克·皮尼基](https://unsplash.com/@marekpiwnicki?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果你还没有在 [Phoenix framework](http://phoenixframework.org) 中体验过 [LiveView](https://github.com/phoenixframework/phoenix_live_view) 的强大功能，请系好安全带:我们将用不到 50 行代码构建一个实时、高性能的聊天系统。这包括了前端*和后端*的所有代码(剧透:它们是一回事)。

我们在这个项目中的基本策略是为每个连接到服务器的用户使用一个持久的 Phoenix LiveView 进程，然后使用 Phoenix 内置的 PubSub(“发布/订阅”)功能向所有连接的进程广播消息。最后，LiveView 进程会将消息呈现给每个连接的用户。

为了让这个例子尽可能的简洁，我们将抄近路，把重点放在核心用例上。我们*不会*解决的一些具体问题(但对于那些想了解更多的人来说是简单的补充)是用户认证和管理；聊天消息持久性；以及对具有大厅的多个频道的支持。

# 先决条件—什么是凤凰？什么是 LiveView？

在开始这个项目之前，你需要按照说明安装[药剂](https://elixir-lang.org/install.html)和[凤凰](https://hexdocs.pm/phoenix/installation.html)。

![](img/31175d4ca8c5e9d04ea308030169b99f.png)

简言之， [Phoenix](https://hexdocs.pm/phoenix/installation.html) 是一个用[神奇灵药语言](https://elixir-lang.org/install.html)编写的复杂的 web 框架。Elixir 之所以如此神奇，部分原因是它构建在 [Erlang 虚拟机](https://www.erlang.org)之上，这意味着它本质上支持高度可用和高度并发的应用程序。

Phoenix web 框架利用了这些特性，并为高度交互式的服务器呈现内容启用了 LiveView。这意味着你可以构建高度互动的“单页”风格的 webapps *而无需编写一行 JavaScript* 。我以前写过关于 [LiveView 以及为什么我认为它是一个如此神奇的工具](https://blog.devgenius.io/why-your-next-frontend-might-be-the-backend-b43ff1ca9720)。

如果您以前没有使用过这个堆栈，这是一个绝佳的机会来尝试一下，亲自看看它的威力。我们开始吧！

# 步骤 1 —项目设置

一旦安装了 Elixir & Phoenix，我们要做的第一件事就是创建项目。假设您的环境配置正确，您可以生成一个完整的 Phoenix 项目目录，包含:

```
mix phx.new liveview_chat --no-ecto
```

这里的`--no-ecto`标志意味着我们不想在我们的项目中包含 Ecto `datamapper`库。为了简单起见，我们在这个例子中这样做，这样我们就不需要担心设置数据库。如果您计划将这个演示项目构建得更大，您可能希望通过省略`--no-ecto`标志来包含 Ecto 功能。

按照`mix`中的步骤创建项目并安装依赖项。如果一切顺利，你应该可以用`mix phx.server`运行你的凤凰服务器，并通过在浏览器中导航到`http://localhost:4000`来查看默认主页。

接下来，我们将通过在`lib/liveview_chat_web/router.ex`编辑路由器文件来为我们的新聊天页面创建一个路由(`/chat`)。这是用户访问聊天时将导航到的路径。我们还将告诉它哪个模块将呈现我们的 LiveView。在这种情况下，该模块将被命名为`LiveviewChatWeb.ChatLive.Index`。要设置路线，在如下所示的块中添加`live`行:

# **第二步——实时取景骨架**

接下来，我们将设置我们的实时视图。为了简单起见，我们将从 LiveView 模块和 HTML 模板的最小版本开始。然后，在我们的后续步骤中，我们将填充功能。

首先，让我们为页面创建一个简单的 HTML 模板。因为我们在这里追求简单，所以我们使用了一些最小的内联样式——在一个真正的应用程序中，我们希望将样式分离出来，或者使用像 Tailwind 这样的框架。

下面是我们的基本模板:

HTML 应该是不言自明的，并且看起来像您可能编写的任何其他 HTML，只有一个例外:我们表单上的属性`phx-submit=”send”` 。该属性是一个 LiveView *绑定，*，它表示我们希望在表单提交时向后端模块发送一条消息。

如果您过去做过 web 开发，您会注意到表单提交是一个客户端事件，但是 LiveView 模块是在服务器端进程中运行的。LiveView 和 Phoenix 对我们隐藏了这种复杂性，所以我们不需要编写 API 或一行 JavaScript 来实现它。我们将在下一步看到事件是如何被消费的。

现在转到后端，让我们编写我们的骨架LiveView 模块，足以让我们的 HTML 模板呈现:

我们需要在这里编写的唯一方法是`mount`——当用户导航到我们的`/chat`路线时，它被调用，并且是我们聊天功能的入口点。我们可以传入请求参数和会话信息，但是对于我们当前的例子，我们可以忽略它们。我们还在 socket 中得到传递，它表示 LiveView 连接。

一旦成功，LiveView 期望我们返回一个元组`{:ok, socket}`(一个成功的函数调用的常见的 Elixir 模式)。但是在返回套接字之前，我们还要给它赋值。这些`assigns`是连接到 LiveView 的用户的持久应用程序状态。在这种情况下，我们的应用程序需要存储用户名和消息列表。出于演示的目的，我们将使用一个随机的用户名——在一个真实的应用程序中，您可能需要一些实际的用户管理！

如果您已经成功添加了这些文件，您应该能够导航到`[http://localhost:4000/chat](http://localhost:4000/chat)`并看到基本页面。不过，您还不能发送任何消息，因为我们还没有添加该功能！

# 步骤 3-接受聊天消息，并广播

接下来，让我们为前端触发的“发送”事件添加一个处理程序。我们将通过一个`handle_event`函数来实现:

为了从前端处理这些`send`事件，我们实现了一个带有如下声明的函数:

```
def handle_event(“send”, %{“text” => text}, socket) do 
  ... 
end
```

如果您不熟悉 Elixir 的模式匹配，这里的第一个和第二个参数不是像在其他语言中那样的简单变量，而是要匹配的模式。这意味着如果第一个参数是“send ”,第二个参数是包含“text”键的字典，那么这个函数将只被调用。如果我们从客户端添加一个新事件，我们可以创建一个完全不同的函数定义来处理这种情况，而不是使用一个带有条件逻辑的`handle_event`函数。

在我们上面的实现中，当我们从客户端收到一个“发送”消息时，我们将通过 PubSub 发布一个消息。为我们生成的*端点*模块默认支持 PubSub。我们只需要指定一个*主题、*消息、和*有效负载。*主题的所有订阅者都将收到消息和有效负载。我们将在下一步中看到如何处理它。

我们这里的有效负载很简单:发送的消息，以及发送消息的人的名字。在真实的应用程序中，我们会有一个更复杂的数据结构，但这是我们演示所需要的。

# 步骤 4 —订阅和消费消息

接下来，添加了我们的聊天消息广播后，我们需要一些东西来消费它们！让我们添加下面的`handle_info`函数来完成我们的 LiveView 模块。

在这次更新中，我们添加了两件事:首先，在 mount 函数中，我们添加了一些行来订阅主题。然后，我们添加了一个`handle_info`函数来消费我们订阅的消息。

你会注意到，在我们订阅之前，我们检查了`connected?(socket)` **—** 为什么？如果你开始用 LiveView 构建很多东西，你迟早会注意到`mount` 被调用了两次*，*，如果你没有预料到的话，这会导致一些混乱和错误。原因是当查看 LiveView 页面时，它实际上是一个两步过程:首先，发出 HTTP GET 请求以获取初始内容，然后，建立 Websocket 连接以执行任何交互式渲染。任何支持交互或持久连接的代码都应该只在“套接字连接”的情况下执行。参见我在 LiveView 上发表的[上一篇文章，了解为什么这个两步过程如此重要的更多细节。](https://blog.devgenius.io/why-your-next-frontend-might-be-the-backend-b43ff1ca9720)

这里的另一个变化是`handle_info` 函数。像我们在上一步中编写的`handle_event`函数一样，我们在这里使用模式匹配只处理我们关心的事件。在这种情况下，我们在套接字的赋值语句中更新“message”数组，以添加新消息。

(补充说明:如果你想知道，`handle_info`和`handle_event`是 LiveView 模块的标准回调接口，如果你对细节感兴趣，[参见文档](https://hexdocs.pm/phoenix_live_view/Phoenix.LiveView.html#c:handle_info/2))。

剩下唯一要做的就是更新我们的 HTML 模板来呈现消息。我们将更新【T4 消息】如下所示:

# 第五步——聊天！

一切就绪后，您应该能够在浏览器中导航到`http://localhost:4000/chat`并查看应用程序的运行情况。在单独的窗口或浏览器中打开页面，发送一些聊天消息，以查看两者(或三者，或四者)之间的交互。

# 后续步骤

如果你已经做到了这一步，你就完成了这篇文章中的演示项目！但是，如果你想继续下去，这里有一些简单的东西，你可以添加到这个项目的扩展:

*   通过`chat:channel_name`支持多个频道
*   让用户指定他们的昵称，并添加基本的用户管理
*   添加消息持久层，以便新用户可以查看聊天历史

```
*Originally posted on* [*Blixtdev*](https://blixtdev.com/real-time-chat-with-phoenix-and-liveview/)*.***Want to Connect?**[*Jonathan*](http://medium.com/@jonnystartup) *has over 20 years of engineering leadership experience in startups big & small.* If you’d prefer to keep reading about the awesome things you can do with Elixir instead, I recommend you check out [my breakdown on LiveView and its benefits](https://blog.devgenius.io/why-your-next-frontend-might-be-the-backend-b43ff1ca9720?source=your_stories_page-------------------------------------), or my list of [5 Elixir Libraries I Install on Every New Project](https://blog.devgenius.io/5-elixir-libraries-i-install-on-every-new-project-a76fae7241a1).
```