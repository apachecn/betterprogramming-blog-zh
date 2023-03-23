# 如何在 Postgres 中设置 PgBouncer 连接池

> 原文：<https://betterprogramming.pub/how-to-set-up-a-pgbouncer-connection-pool-in-postgres-225d5f742471>

## 在 Heroku 上部署 PgBouncer 并探索其工作原理

![](img/f3c41d98fe9ecde674ebd03ef43e93de.png)

照片由 [Siora 摄影](https://unsplash.com/@siora18?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在我的[上一篇文章](http://medium.com/p/d8766a8a2c85)中，我谈到了为什么你应该在`Postgres`中创建一个连接池，以及你的一些选择。

在本文中，让我们在现有的应用程序上运行一次`PgBouncer`的实际部署，看看它是如何工作的。对于这个设置，我将使用`[Heroku](https://www.heroku.com)`,这样我们就可以快速部署一些东西，并以最少的麻烦开始工作。这个过程会很简单，因为`Heroku`是一个 PaaS 提供商，为我们处理大部分 DevOps 步骤。其他 IaaS 和 PaaS 提供商可能提供类似的东西，即使你喜欢在低层次工作，[实现](https://www.pgbouncer.org/install.html) `[PgBouncer](https://www.pgbouncer.org/install.html)` [也不是太难](https://www.pgbouncer.org/install.html)。

我将根据 Heroku 的 `[PgBouncer](https://devcenter.heroku.com/articles/best-practices-pgbouncer-configuration)` [](https://devcenter.heroku.com/articles/best-practices-pgbouncer-configuration)和[的](https://devcenter.heroku.com/articles/postgres-connection-pooling) `[Postgres](https://devcenter.heroku.com/articles/postgres-connection-pooling)` [连接池](https://devcenter.heroku.com/articles/postgres-connection-pooling)来编写我的示例。

# 架构决策

在我们部署或编写任何代码之前，我们需要做出一些架构决策。首先是选择服务器端或客户端实现。你可以在找到关于这个话题的[深入讨论。然而，因为我们已经为这个例子决定了中间件，这就是我们要坚持的。](https://devcenter.heroku.com/articles/best-practices-pgbouncer-configuration#pgbouncer-on-heroku-server-side-vs-client-side)

由于 Heroku 上的服务器端实现仍处于测试阶段，我们在其他配置选项上有些受限。如果你需要更高级的配置选项，你需要使用[客户端安装](https://devcenter.heroku.com/articles/on-dyno-postgres-connection-pooling)。但是通常，除了服务器与客户端部署之外，您还需要考虑以下几点。

## 连接池模式

在`PgBouncer`中有三种不同的连接池模式。这些模式确定了连接何时返回连接池。这些模式是:

*   **语句** —该语句一执行，连接就返回到池中(自动提交始终打开)。
*   **事务** —事务一完成(执行提交或回滚)，连接就返回到池中。
*   **会话** —用户会话一关闭，连接就返回到池中。

通常，您会希望使用事务模式，因为大多数应用程序仅在发生事务时才需要数据库连接—在事务之间，大多数应用程序都在等待用户、执行逻辑等，不需要数据库连接。然而，[值得通读每种](https://devcenter.heroku.com/articles/best-practices-pgbouncer-configuration#pgbouncer-s-connection-pooling-modes)模式的典型用例，以确保您使用的是最好的模式。

## 池设置

`PgBouncer`的池设置还真不少。例如，以下是五种常见设置:

*   `pool_size`——就像它的名字一样:游泳池的大小。默认值为 20。对于 Heroku 服务器端计划，默认值是计划连接限制的一半。
*   `reserve_pool_size`—在使用量激增时使用的备用池
*   `max_db_connections` —数据库允许的最大连接数。对于 Heroku 服务器端计划，默认值是计划连接限制的四分之三。
*   `max_user_connections` —每个用户允许的最大连接数。
*   `max_client_conn` —允许的最大传入客户端连接数(在所有用户中)。对于 Heroku 服务器端计划，默认值为 1000。

这需要进行大量的配置和优化，而且还有更多的设置。你可以在这里[读到他们以及他们做什么。](http://www.pgbouncer.org/usage.html)

对于我们的例子，我们将使用 Heroku 在安装时配置的事务模式和默认池设置。

# 部署我们的示例

我想从一个已经在使用`Postgres`的部署开始，然后将它转换成一个连接池。我将使用[这个项目](https://devcenter.heroku.com/articles/getting-started-with-nodejs)作为我的基础部署(同样，[本指南关于](https://devcenter.heroku.com/articles/best-practices-pgbouncer-configuration) `[Postgres](https://devcenter.heroku.com/articles/best-practices-pgbouncer-configuration)` [连接池](https://devcenter.heroku.com/articles/best-practices-pgbouncer-configuration))。这个项目部署了一个连接到 Postgres 数据库的 Node.js 应用程序(没有连接池)。启动并运行它大约需要 10 分钟，因此它非常适合我们的快速示例。

如果你以前没有使用过 Heroku，但仍然想跟随它，不要担心——指南会一步一步地教你。您需要做的唯一更改是在安装快结束时提供数据库。因为你不能使用爱好版进行连接池，[你需要使用一个标准计划](https://elements.heroku.com/addons/heroku-postgresql)(最低支付的月计划)数据库来代替。在 Heroku CLI 中使用这一行来配置数据库:

`$ heroku addons:create heroku-postgresql:standard-0`

一旦你完成设置，你的应用程序启动并运行，你就可以打开你的应用程序(网址末尾有`/db`)并看到这个页面:

![](img/9d85ab4225d24b431b8be3fcd97d8f61.png)

太好了！如果您遵循了该指南，那么您已经设置好了一个 Node.js 应用程序，它通过一个测试表连接到`Postgres`。现在让我们看看用`PgBouncer`开始使用连接池是多么容易。使用 Heroku CLI 只需几个简单的步骤:

*   启用池。下面的命令创建池和连接池 URL(使用当前数据库 URL)。

`*$ heroku pg:connection-pooling:attach DATABASE_URL — as DATABASE_CONNECTION_POOL*`

*   更改您的配置以使用池 URL 而不是数据库 URL:

![](img/b129d0fe9556de4cf536d4443a80fd75.png)

*   提交您的更改并重新启动。

就是这样！作为一个快速测试，我对客户端做了一个无意义的代码更改，看看会发生什么。在这里，我循环并向池中打开 999 条 select 语句:

![](img/3d120fe9915aa7dc01f694f56ec73efb.png)

部署了这段代码后，我点击了两次`/db` URL。让我们看看每次跑步后的统计数据，看看发生了什么。要查看统计数据，首先，使用命令`heroku config`找到您的数据库连接池 URL。然后使用您的数据库连接池 URL 发出以下命令，并用`pgbouncer`替换路径的最后一部分:

```
*$ psql postgres://username:password@ec2–192–168–1–1.compute-1.amazonaws.com:5433/pgbouncer*
```

现在你可以尝试不同的选项，如`show stats`或`show pools`。

这是我们在两次运行后看到的`show stats_totals`。有许多查询在`db`级别运行:

![](img/43315bc749915e1bbc22e0cafa0550c9.png)

现在`show pools`查询正在运行。对于所有这些查询，只有一个活动连接被重用:

![](img/9699b922bd5a4b2ce2dec2f1022945f8.png)

成功！我们在`Postgres`上启动并运行一个`PgBouncer`连接池。

# 下一步是什么？

对于`PgBouncer`和其他连接池，有几点需要注意。这里有一些需要注意的事项和资源，可以帮助您进一步挖掘。

## 监视

虽然您可以在我的例子中使用`show stats`，但这些都是非常基本的指标。当您使用连接池时，您会希望使用更健壮的监控解决方案。查看[这篇关于在监控连接池时同时使用 USE 和 RED 框架](https://blog.okmeter.io/use-red-and-real-world-pgbouncer-monitoring-61b34ebeebb8)的文章。

## 客户端与服务器

将池部署到客户机还是服务器可能是一个艰难的选择。有关更多信息，请查看 PgBouncer FAQ ，其中讨论了延迟与控制。

## 准备好的声明

由于预准备语句本质上是在数据库连接打开之前创建的，因此它们通常会导致连接池出现问题。我再次推荐[PgBouncer FAQ](https://www.pgbouncer.org/faq.html)作为这个话题的起点。

# 结论

希望您现在已经了解了为什么需要使用`Postgres`的连接池，您的实现选项是什么，以及典型的`PgBouncer`实现是什么样子。