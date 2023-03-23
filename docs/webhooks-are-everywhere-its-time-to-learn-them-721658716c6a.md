# 网络钩子无处不在——是时候学习它们了

> 原文：<https://betterprogramming.pub/webhooks-are-everywhere-its-time-to-learn-them-721658716c6a>

## 一个快速简单的指南，帮助你学习用 Python 发送 webhooks

![](img/ee7c363d9ae07114d63136033ed534a9.png)

[Saad Chaudhry](https://unsplash.com/@saadchdhry?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果你曾经研究过 API 和自动化，你肯定听说过 webhooks。现在，对于每个开发人员来说，在他们的技能包中包含这些是很重要的。

# 什么是 Webhooks 及其用例？

我将保持简单，因为我注意到阅读/浏览百分比分数最高的文章是那些技术细节较少而例子较多的文章。

Webhooks 基本上是用户定义的回调，工作在 HTTP 协议上。它们是请求，通常由用户在事件开始时触发，并提供实时信息。一旦事件启动，一个 webhook 就会被发送到一个定义好的“webhook URL ”,其中包含有关该事件的数据。看一个真实世界的例子，我们可以使用 webhooks 来通知我们，一旦我们的代码执行完了一个任务，或者一旦我们完成了推送 GitHub repo。

例如，当在服务器上运行长时间的任务时，在我的代码中，我将 webhooks 发送给 Discord(使用它的 webhooks 功能)以向我发送输出，并在进程结束时发送。它非常有用，因为我不需要打开外壳，无论我在哪里，我都可以在手机上查看它。

作为一名开发人员，无论你是做前端还是后端工作，把它们放在你的技能包中是很重要的。

# Python 通用实现

发送 webhooks 总是相对简单的，对于 Python 也是如此。有许多方法可以做到这一点。我个人更喜欢使用 webhooks 包，因为它使用的只是一个简单的装饰器。

webhook 请求基本上是一个带有 JSON 主体的简单 POST 请求。我们可以在 Python 中很容易地复制这一点(导入`webhooks`模块):

这里我们定义了一个新的使用`webhook`装饰器的`basic`函数(用于发送基本的 webhooks)。装饰器只是从函数中获取参数，并完成所有的艰苦工作。我们只需要在 webhook 的主体中添加`hey_msg`参数。最后，我们调用`basic`函数并设置 webhook 的 URL 和`hey_msg`参数的内容。

此外，`webhooks`包附带了许多其他有用的功能，您可以通过阅读[文档](https://webhooks.readthedocs.io/en/latest/)来发现。

# Python Discord 实现

经过简单实用的介绍，我们现在可以看到如何使用 webhooks。对我来说，这非常有用:当在服务器上运行或构建时，这是从我的脚本和应用程序获得输出的一种快速而可靠的方式。我只是使用`nohup`并让我的脚本在服务器的后台运行，然后休息一下，只是从我的手机监控我的不和谐。当它遇到断点时，我给自己发送调试信息、输出结果等。

好消息是什么？实现和前面的例子一样简单:

1.  在 Discord 上创建 webhook 集成。
2.  发送 webhooks。

## 创建 webhook 集成

1.  创建测试服务器。
2.  在服务器的常规聊天中，转到设置:

![](img/b31dee94503f96037d1a97019373dcb6.png)

3.转到集成>创建 webhook >复制 webhook URL。

我们现在只需要使用`webhooks`包向这个 URL 发送一个 webhook 请求。

## 发送网页挂钩

使用下面的代码，我们将向 webhook bot 发送信息，以在`general` 频道上发送一条以`content` ( `"hey”`)内容为主体的消息。要了解更多关于 webhook 不和谐请求的内容，请参见这个令人敬畏的 [GitHub 要点](https://gist.github.com/Birdie0/78ee79402a4301b1faf412ab5f1cdcf9):

# Python Slack 实现

我确信你们中的许多人更喜欢 Slack 来与团队合作，所以我也描述了如何使用 webhooks 在 Slack 中发送消息。这个过程类似于不和谐的过程。你可以按照这个简单的指南来激活和创建 webhooks。一旦你有了你的 webhook URL(看起来像这样:[https://hooks.slack.com/services/XXXXXX/XXXXXX/XXXXX](https://hooks.slack.com/services/))，你可以使用下面的代码片段:

就像不和谐音一样，这会用`"hey"`身体发出一个信息。您可以使用此[资源](https://api.slack.com/messaging/webhooks#advanced_message_formatting)来了解邮件的高级格式。

现在，您可以从代码中间发送 webhooks，并与主要的团队平台进行交互。就我个人而言，我将这些 webhook 函数插入到我的许多代码中。同样，当在服务器上工作或投入生产时，它们变得非常有用。

我希望这篇文章对你学习使用 webhooks 有所帮助。