# 如何避免 Node.js 应用程序中的瓶颈

> 原文：<https://betterprogramming.pub/how-to-avoid-bottlenecks-in-node-js-applications-8085d86b6b2e>

## 配置 Sequelize 和 Mongoose 来处理并发的数据密集型请求

![](img/07979ac6e5d2210d9c0b9709e3746a8e.png)

约书亚·阿拉贡在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

最近，我在 [Node.js](https://nodejs.org/) 中处理了一个巨大的性能问题。我用 [Sequelize](https://sequelize.org/) 和[mongose](https://mongoosejs.com/)构建的后端经常几分钟都无法响应任何请求。

让我们看看我是如何识别瓶颈的，为什么 Node.js 开发人员会遇到这样的问题，以及如何避免它们。

[](https://medium.com/quick-code/what-a-recurring-outofmemory-error-taught-me-11f2061063a1) [## 处理 Spring Boot 的记忆缺失错误

### 一个重复出现的 OutOfMemoryError 异常教会了我什么

medium.com](https://medium.com/quick-code/what-a-recurring-outofmemory-error-taught-me-11f2061063a1) 

# 检测瓶颈

在意识到问题出在别处之前，我已经监测了我的 VPS 好几天，没有发现任何令人担忧的事情。应用程序使用的每个数据库都有自己的 VPS。数据非常大——数以亿计的记录——但我使用的 16GB 4 CPU VPSs 从未使其资源饱和。尽管如此，问题仍然存在。

当监控应用程序和数据库时，我发现一切似乎都很好，即使我的后端正在努力响应。

首先，我必须复制这个问题。为此，我强调了我的申请。经过一些测试后，我能够重现这个问题。只有在处理大型查询时才会出现这种情况，但 CPU 密集型操作不会出现这种情况。

为了了解发生了什么，我开始监视以下查询的结果:

```
[SHOW PROCESSLIST](https://dev.mysql.com/doc/refman/8.0/en/show-processlist.html)
```

通过这个特殊的查询，MySQL 返回一个进程列表，指示服务器中执行的线程组当前正在执行的操作。

答对了。通过观察这个查询的结果，同时强调我的应用程序，我能够理解问题的起源。我的 Express 应用程序一次使用的进程不超过五个，对额外的请求进行排队。

# 什么是连接池？

打开和关闭数据库连接涉及 CPU 时间和内存，因此它们是开销很大的操作。因此，如果应用程序为每个数据库请求打开一个新的连接，对性能的影响将是巨大的。

为了节省资源和重用连接，大多数[数据库驱动程序](https://www.jdatalab.com/information_system/2017/02/16/database-driver.html)使用不同的方法。每次需要对数据库执行操作时，他们不是建立一个新的连接，而是从一个特殊的缓存中借用一个连接。一旦要求的操作结束，他们就把它归还，释放它。这个特殊的缓存被称为[连接池](https://en.wikipedia.org/wiki/Connection_pool)。

这种方法的缺点是，当达到连接池限制时，任何新请求都必须等待前一个请求释放连接。

这就是为什么应该仔细选择池的大小。如果您的数据不是很大，或者您不希望有很多并发用户，您可能不会遇到任何问题。否则，这很容易成为你的应用程序的瓶颈。

连接池的大小通常在每个数据库驱动程序中都是可配置的。默认池大小因驱动程序而异。为了找到正确的值，需要考虑的最重要的方面是预期的应用程序负载和并发级别。

让我们看看 Sequelize 和 Mongoose 中默认的连接池大小值是多少，以及如何配置它。

# 在序列中配置连接池大小

每当您的应用程序连接到数据库时，它实际上会派生一个新的进程来完成请求。

正如[官方文档](https://sequelize.org/master/manual/connection-pool.html)中所述，Sequelize 在初始化时设置一个连接池，可以通过构造函数的`options`参数进行配置。

默认情况下，该池仅限于`5`个连接。这意味着无论收到多少请求，您的应用程序一次使用的连接都不会超过五个。如前所述，如果六个用户同时访问任何需要连接到数据库的 API，其中一个将会排队等待。

当调用[序列化构造函数](https://sequelize.org/master/class/lib/sequelize.js~Sequelize.html#instance-constructor-constructor)时，可以通过改变`pool.max`参数值来修改该值，如下所示:

请注意，`max`属性的正确值也取决于您的 MySQL 服务器配置。

请记住， [mysqld](https://dev.mysql.com/doc/refman/5.6/en/mysqld.html) 实际上只允许`[max_connections](https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html#sysvar_max_connections)`个客户端连接，外加一个为拥有`[SUPER](https://dev.mysql.com/doc/refman/5.6/en/privileges-provided.html#priv_super)`权限的帐户保留的额外连接。在 MySQL 5.6 中，它的默认值是 151。

当到达`max_connections`时，服务器拒绝任何其他连接——这与在应用层发生的情况相反，在应用层请求被排队。

为`max_connections`找到一个好的值是复杂的，这不是本文要讨论的。相反，我推荐阅读 MySQL 官方文档中的[这一页](https://dev.mysql.com/doc/refman/5.6/en/connection-interfaces.html)。

# 在 Mongoose 中配置连接池大小

当使用 Mongoose 处理 MongoDB 时，也会出现完全相同的问题。

正如[官方文档](https://mongoosejs.com/docs/connections.html#connection_pools)中所述，每个`connection`——无论是用`mongoose.connect`还是`mongoose.createConnection`创建的——都有一个内部可配置的连接池作为支持。再次，其默认最大值为`5`。

调用`[mongoose.connect](https://mongoosejs.com/docs/connections.html#options)`时，您可以通过改变`poolSize`选项值来修改该值，如下所示:

至于 MySQL，`poolSize`值应该基于您的 MongoDB 服务器配置。具体来说，`[mongos](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)`或`[mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)`将接受的最大同时连接数由`[maxIncomingConnections](https://docs.mongodb.com/manual/reference/configuration-options/#net.maxIncomingConnections)`定义。在 MongoDB 4.4 中，其默认值为 65536。

根据 [MongoDB 文档](https://docs.mongodb.com/manual/reference/configuration-options/#net.maxIncomingConnections)，该参数的值应该大于连接池的最大大小。

请记住，处理`maxIncomingConnections`参数可能很棘手，如果没有很好的理由，你不应该改变它。

# 结论

在本文中，我们研究了连接池如何成为 Node.js 应用程序中的瓶颈。了解什么是连接池及其工作原理对于理解如何正确配置它至关重要。事实上，Sequelize 和 Mongoose 的默认限制可能会对应用程序的性能产生不利影响。如图所示，在处理并发数据密集型请求时尤其如此。

感谢阅读！我希望这篇文章对你有所帮助。如果有任何问题、意见或建议，请随时联系我。