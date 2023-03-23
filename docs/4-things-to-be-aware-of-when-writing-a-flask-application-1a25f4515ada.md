# 我从编写烧瓶应用程序中学到的 4 件事

> 原文：<https://betterprogramming.pub/4-things-to-be-aware-of-when-writing-a-flask-application-1a25f4515ada>

## 有多种原因可以解释为什么你应该注意你的烧瓶应用程序

![](img/0c0acc99680a6b039ac956a77a0e8c74.png)

Simone Viani 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

在过去的两年里，我已经了解了[烧瓶](https://flask.palletsprojects.com/en/2.0.x/)的来龙去脉。Flask 是一个非常灵活的 Python 后端框架，它有许多库，可以毫不费力地插入到您的代码库中。

当编写应用程序时，您会遇到一些问题，您必须努力理解这些问题并找到解决方案。我将在下面讨论的四个项目是我已经浪费了相当长时间的事情。我希望两年前我开始写申请的时候就知道这些。

我还有很多东西要学，所以如果你想在清单上添加一些东西，请告诉我！

# Flask SQL Alchemy/RQ

首先我想讨论的是`flask_sqlalchemy`的用法。太棒了！与`flask_migrate`一起，它允许我在生产环境中快速执行数据库更改，而不必担心另一个开发人员在手动迁移时会出错。

然而，`flask_sqlalchemy`的一个问题是它必须绑定到 Flask 应用程序生命周期。这意味着您只能在 HTTP 请求的上下文中创建数据库调用。您可以通过手动创建这样的上下文来解决这个问题:

```
with app.app_context():
    db.create_all()
```

这很好，但这意味着您需要初始化应用程序。如果您有一个更大的应用程序，您想在其中使用 RQ/Celery 执行后台任务，这意味着您必须为每个工人初始化一个应用程序来使用`flask_sqlalchemy`。**请不要这样。**

我个人的解决方案是在整个应用程序中使用来自`sqlalchemy`库的`scoped_session`,当它是一个活动的工人时。

这意味着我必须为`flask_sqlalchemy`的`SQLAlchemy`对象编写一个包装器，并在 worker 的上下文中使用它。这在一个工人的创业中节省了我大量的时间！

回顾过去，我建议每个人直接使用`sqlalchemy`,如果你写的不仅仅是一个简单的 CRUD 应用程序。生命周期管理是伟大的，但是当你需要定制的时候，它不值得所有的麻烦。

# SQL 炼金术—预 ping

这个人让我非常头疼。我们的应用程序运行在 DigitalOcean 上，突然，它就停止工作了。我发现这很奇怪，因为开始时只有另外一个人在使用它。

当查看日志时，我们有`OperationalError: (OperationalError) (2006, 'MySQL server has gone away')`没有更多的信息。太好了。

在搜索了大量时间并花费了多次部署来解决这个问题之后，我了解到如果一个应用程序超过八个小时没有被使用，连接就会被断开。

基本上，有两种解决方案可以防止这种情况发生。[第一个](https://docs.sqlalchemy.org/en/14/core/pooling.html#disconnect-handling-pessimistic)在 SQL 连接上发出一个测试语句，称为`pool_pre_ping`。通常，这是由`SQLAlchemy`自动完成的，在执行查询之前，它会向 SQL server 发送一条类似于`SELECT 1`的语句。这增加了一点开销，但是根据文档，它是:

> "最简单和可靠的方法，以彻底消除数据库错误，由于陈旧的池连接."— [SQLAlchemy 文档](https://docs.sqlalchemy.org/en/14/core/pooling.html#disconnect-handling-pessimistic)

另一个更“乐观”的方法是与《T4 时报》合作。默认情况下，这被设置为`-1`，这意味着没有回收发生。通过回收我们的池，已经超过一定年龄的连接将被自动关闭。这将防止我们使用陈旧的连接，这种连接会导致抛出异常，并使我们的用户感到失望。

# RQ /SQL Alchemy —关闭数据库连接

还记得我说过不要在后台工作者中使用`flask_sqlalchemy`吗？好吧，如果你仍然想使用这个库，你必须意识到它与你的应用程序的生命周期挂钩。

这意味着:发出请求→创建 SQL 会话→做一些工作→关闭 SQL 会话→发送响应。

如果你使用后台任务会发生什么？没有请求进来！这意味着您必须手动将应用程序上下文推送给您的员工。

这让我想到了第三个需要注意的事情:在后台任务中，总是去掉你的`db.session`。乍看之下，这似乎并不明显，因为您会发现，在请求的上下文中，没有它，您的应用程序也能很好地工作。

但是，在后台任务中，`flask_sqlalchemy`不会自动删除您的会话！因此，在每个后台任务结束时，删除会话就成了您的责任。否则，您将结束所有正在使用的会话，异常将开始淹没您的日志。

所以记住:`db.session.remove()`是你后台乔布斯背景下的朋友。

# 使用代理(NGinx/Apache)

当您部署应用程序时，您可能会使用类似 Nginx 或 Apache 的东西作为负载平衡器/代理。

问题是，WSGI 可能认为请求来自 Nginx 服务器，而不是来自真实的客户端。像 Nginx 这样的代理有很多头，允许您跟踪请求来自的实际用户。

这就是为什么，当用代理部署你的应用程序时，你应该使用一些定制的中间件，比如 [Werkzeug 的 ProxyFix](https://werkzeug.palletsprojects.com/en/2.0.x/middleware/proxy_fix/) 来允许应用程序根据请求正常工作。

请记住，在生产中，你永远不应该使用类似`flask run`的东西！始终为您的应用程序使用符合 WSGI 的容器，如 Gunicorn、unicorn、uWSGI、GEvent 等。

# 结论

在本文中，我分享了过去两年中我在使用 Flask 框架时遇到的一些麻烦。总的来说，这是一次积极的经历，我很高兴选择了它。

我希望这已经帮助了你们中的一些人，他们希望涉足 Flask 生态系统，或者希望将他们的知识推进一点。祝你好运！