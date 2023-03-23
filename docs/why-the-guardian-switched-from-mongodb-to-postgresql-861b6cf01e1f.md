# 《卫报》为何从 MongoDB 转向 PostgreSQL

> 原文：<https://betterprogramming.pub/why-the-guardian-switched-from-mongodb-to-postgresql-861b6cf01e1f>

## 他们为什么要转换，他们是如何迁移的，以及为什么他们现在不这么做

![](img/5f73af5a0ffa6e6f69ce14c4f5112c4f.png)

Jordi Fernandez 在 [Unsplash](https://unsplash.com/s/photos/migration?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在【2018 年初，英国报纸和在线出版物《卫报》将其 230 万条内容从 [MongoDB](https://www.mongodb.com/) 切换到 [PostgreSQL](https://www.postgresql.org/) 。

他们为什么要换？今天他们会有什么不同？

# 伦敦服务器被烤熟的那天

2015 年 7 月，一场大规模的热浪袭击了伦敦，导致《卫报》仅在三年后就从 MongoDB 转向 PostgreSQL，这是一个不寻常的结果。

当时,《卫报》的数据在一个标准的 NoSQL 数据库中；MongoDB 存储在本地的 Guardian Cloud，这是位于国王十字附近的 Guardian 办公室地下室的数据中心。

当《卫报》的作者们在博客上直播热浪的时候,《卫报》的开发者们正忙着让主要的服务器重新上线。

![](img/9604cd97bf64f6c8223d7e9d7fe6d487.png)

酷热导致《卫报》的内部 CMS 工具 Composer 及其数据库出现故障——导致《卫报》的网站临时切换到伦敦其他地方的故障转移服务器。

这是促使向更可靠的亚马逊网络服务(AWS)转变宜早不宜迟的决定性因素。

但是为什么要离开 MongoDB 呢？

# AWS 也有 MongoDB，但是…

在热浪对其故障转移程序进行压力测试后不久，卫报将其服务器从卫报云转移到 AWS。

但是，与 MongoDB 争论的真正焦点很快就出现了。

参与该项目的开发人员透露，解决 ops manager(Mongo 的数据库管理软件)的问题需要数百小时的工程时间:

> OpsManager 并没有真正兑现其无忧数据库管理的承诺。[...]我们每年至少损失两个月的工程时间来做这项数据库管理工作。——[菲利普·麦克马洪](https://www.theguardian.com/profile/philip-mcmahon)，[玛利亚-利维雅·奇奥利安](https://www.theguardian.com/profile/maria-livia-chiorean)，[苏西·科尔曼](https://www.theguardian.com/profile/susie-coleman)和[阿卡什·阿斯库勒姆](https://www.theguardian.com/profile/akash-askoolum)在[卫报](https://www.theguardian.com/info/2018/nov/30/bye-bye-mongo-hello-postgres)

事实上，该团队已经支付了昂贵的 Mongo 支持合同，但是在长达两个小时的数据库中断期间没有得到有用的支持。

# MongoDB 并不适合 AWS

公平地说,《卫报》有独特的需求，需要数百行 Ruby 脚本来处理 AWS 基础设施:

> 由于编辑需求，我们需要在 AWS 中自己的基础设施上运行数据库集群和 OpsManager，而不是使用 Mongo 的托管数据库产品。— [菲利普·麦克马洪](https://medium.com/u/20f674c57775?source=post_page-----861b6cf01e1f--------------------------------)等人在[卫报](https://www.theguardian.com/info/2018/nov/30/bye-bye-mongo-hello-postgres)

这意味着开发人员不断地在数据库基础设施上工作，而不是让 OpsManager 处理后端。

# 从 NoSQL 转行的决定

两年后,《卫报》决定是时候最终放弃他们在 AWS 上为 MongoDB 定制的基础设施了。

最自然的选择应该是亚马逊的 NoSQL 产品 DynamoDB。

但是缺少了一个关键的特征，这就造成了所有的不同。

时间是《卫报》从 MongoDB 转向 PostgreSQL 的主要原因——自从 DynamoDB 最近添加了这一缺失的重要功能以来，今天可能会做出不同的选择。

# 黑仔失踪的特征

《卫报》需要一个名为 [*的静态加密*](https://brightlineit.com/encryption-at-rest-important-business/) 功能，不幸的是，DynamoDB 当时没有提供。

开发商推迟了项目，但最终，他们不得不决定:

> 在等了大约九个月来添加这个特性之后，我们最终放弃了，并寻找其他东西，最终选择在 AWS RDS[关系数据库服务]上使用 Postgres。— [菲利普·麦克马洪](https://medium.com/u/20f674c57775?source=post_page-----861b6cf01e1f--------------------------------)等人在[卫报](https://www.theguardian.com/info/2018/nov/30/bye-bye-mongo-hello-postgres)

从 NoSQL 转换到 SQL 带来了一系列挑战，以及一个独特的技术解决方案:`jsonb`列类型，它支持 JSON blob 中字段的索引。

# 他们是如何做到的

SQL 和 NoSQL 数据库通常被认为是截然相反的:

> 像 MongoDB 这样的文档数据库使用 JSON 文档来存储记录，就像关系数据库中的表和行存储记录一样。—[MongoDB JSON 白皮书](https://www.mongodb.com/json-and-bson)

据开发人员称，`jsonb`类型有助于使 PostgreSQL 中的数据模型看起来更像 MongoDB 记录:

> 我们希望通过使用`jsonb`类型，我们可以从 Mongo 迁移到 Postgres，对我们的数据模型做最小的改动。此外，如果我们想在未来转移到一个更加关系化的模型，我们有这个选项。— [菲利普·麦克马洪](https://medium.com/u/20f674c57775?source=post_page-----861b6cf01e1f--------------------------------)等人在[卫报](https://www.theguardian.com/info/2018/nov/30/bye-bye-mongo-hello-postgres)

使用 JSON blob 意味着更少的数据重组和更快的迁移。

# 堆栈溢出的威力

社区支持可能是决定什么软件被广泛采用的最重要的因素。

《卫报》的决定似乎就是如此:

> Postgres 的另一个优点是它的成熟程度:我们想问的每个问题在大多数情况下都已经在堆栈溢出中得到了回答。— [菲利普·麦克马洪](https://medium.com/u/20f674c57775?source=post_page-----861b6cf01e1f--------------------------------)等人在[卫报](https://www.theguardian.com/info/2018/nov/30/bye-bye-mongo-hello-postgres)

显然，使用众所周知的通用 SQL server 是一个优势。

# 《卫报》是如何做出改变的

开发人员需要一种与新的 PostgreSQL 数据库对话的方式，所以第一步是制作一个 PostgreSQL API。

他们需要逐个功能地复制旧的 MongoDB API:

> 我们使用了 [doobie](https://tpolecat.github.io/doobie/) ，Scala 的纯功能 JDBC 层，添加了 [Docker](https://www.docker.com/) 用于本地运行和测试，并改进了日志记录和关注点分离。。— [菲利普·麦克马洪](https://medium.com/u/20f674c57775?source=post_page-----861b6cf01e1f--------------------------------)等人在[卫报](https://www.theguardian.com/info/2018/nov/30/bye-bye-mongo-hello-postgres)

到 2017 年 8 月底，他们已经部署了一个快速、现代化的 API，使用 PostgreSQL 作为其数据库，但在他们将生产服务器切换到 PostgreSQL 之前，还需要 10 个月。

# 在卫报的引擎盖下

在《卫报》上，所有的文章都存在于《卫报》的内容 API(CAPI)中，为应用程序和网站提供动力。

与此同时，卫报的内部 CMS Composer 是建立在 2014 年的 [Scala](https://www.scala-lang.org/) 、 [Scalatra Framework](http://scalatra.org/) 和 [Angular.js](https://angularjs.org/) 的网络堆栈之上的。

> 如果 CAPI 的弹性搜索集群发生了什么事，那么我们会从 Composer 的数据库中重新索引它。— [菲利普·麦克马洪](https://medium.com/u/20f674c57775?source=post_page-----861b6cf01e1f--------------------------------)等人在[卫报](https://www.theguardian.com/info/2018/nov/30/bye-bye-mongo-hello-postgres)

换句话说，开发人员只需迁移一个数据库。

# 迁移魔法

使用 [Scala](https://www.scala-sbt.org/) `[sbt](https://www.scala-sbt.org/)` [工具](https://www.scala-sbt.org/)迁移过程非常顺利:

> 我们使用了一个 sbt 项目来执行迁移。我们采用的方法允许我们使用我们有信心的语言工作，并执行多次“测试迁移”,直到我们有信心在生产中运行它。— [菲利普·麦克马洪](https://medium.com/u/20f674c57775?source=post_page-----861b6cf01e1f--------------------------------)等人在[卫报](https://www.theguardian.com/info/2018/nov/30/bye-bye-mongo-hello-postgres)

2017 年末，开发人员疯狂地测试访问 PostgreSQL 数据库的新 API 以及迁移数据。

但是在投入生产之前尝试测试迁移的服务器会产生一系列问题:与代理问题相关的短暂中断。

# 拔掉插头

最后，在 2018 年 4 月，是时候关闭代理并向世界推出新迁移的 PostgreSQL 数据库了:

> 团队聚集在一台电脑周围。开关就在一键之外。没有人再呼吸了。完全沉默。咔嚓！零钱已经用完了。什么都没坏！我们都放松了。— [菲利普·麦克马洪](https://medium.com/u/20f674c57775?source=post_page-----861b6cf01e1f--------------------------------)等人在[卫报](https://www.theguardian.com/info/2018/nov/30/bye-bye-mongo-hello-postgres)

令人印象深刻的是，超过 230 万篇文章已经从 MongoDB 转移到 PostgreSQL，零宕机— ，直到描述这一过程的文章于 2018 年 11 月发表，才有人注意到。

# 今天会有所不同吗？

导致 PostgreSQL 决策的致命特性是缺少支持静态加密的 DynamoDB。这就引出了一个问题:

如果该项目被推迟到 2019 年，会发生什么？

# DynamoDB 现在提供静态加密

截至 2019 年 9 月， [DynamoDB](https://aws.amazon.com/dynamodb/) 默认为静态加密。

Guardian 需要静态加密功能来保护其数据免受底层存储的未授权访问。

但是等待 DynamoDB 的加入意味着又要在一个不可靠的服务器上托管一年的内容，并签订昂贵的支持合同。

显然，长达一小时的数据库中断是转换的强大动力。

# 换回来了还来得及吗？

进行这种转换的开发人员希望 PostgreSQL 作为关系数据库的附加特性有一天会有用:

> 此外，如果我们想在未来转移到一个更相关的模型，我们可以选择。— [菲利普·麦克马洪](https://medium.com/u/20f674c57775?source=post_page-----861b6cf01e1f--------------------------------)等人在[卫报](https://www.theguardian.com/info/2018/nov/30/bye-bye-mongo-hello-postgres)

鉴于《卫报》上发布的内容类型——文档——很可能《卫报》的数据库将保持文档存储格式。

这个事实，以及 JSON blob 数据类型的使用，意味着从 PostgreSQL 切换回 NoSQL 文档数据库的选项总是存在的。

然而，在工程师们刚刚花了近一年时间编写全新的 SQL API 之后，切换回来将是从零开始重写 NoSQL API。

这可能是一个交易破坏者，因为在当前时间切换回来没有任何明显的优势。

# 重大的决定

伦敦有记录以来最热的七月天的命运转折，以及 DynamoDB 中静态加密的延迟推出，意味着《卫报》的内容现在托管在 PostgreSQL 数据库中。

感谢开发人员在《卫报》上详细描述了他们的旅程，我们已经了解了为什么该出版物放弃了他们在伦敦的本地服务器，以及为什么这一举动导致了从 NoSQL 到 SQL 数据库的迁移。

通过使用 JSON blob 列类型，开发人员能够快速地从 MongoDB 迁移到 PostgreSQL，而无需停机。

但是，如果今天考虑类似的举措，事情会有所不同——因为 AWS 上的 DynamoDB 现在支持静态加密。

如果开发人员现在考虑改变，他们可能会选择留在 NoSQL，从而更快地过渡。

# 进一步阅读

*   [可发货【2017 年](https://www.shippable.com/)[11 月](http://blog.shippable.com/why-we-moved-from-nosql-mongodb-to-postgressql)从 MongoDB 迁移到 PostgreSQL:

[](http://blog.shippable.com/why-we-moved-from-nosql-mongodb-to-postgressql) [## 为什么我们从 NoSQL MongoDB 迁移到 PostgreSQL

### 几年前，我们将代码库转移到 monorepo，这帮助我们在代码重用方面实现了巨大的扩展…

blog.shippable.com](http://blog.shippable.com/why-we-moved-from-nosql-mongodb-to-postgressql) 

*   [Ajay Kulkarni](https://medium.com/u/4a10cd338224?source=post_page-----861b6cf01e1f--------------------------------) 在 2019 年 7 月的一篇文章中讨论了为什么 SQL 打败了 NoSQL:

[](https://blog.timescale.com/blog/why-sql-beating-nosql-what-this-means-for-future-of-data-time-series-database-348b777b847a/) [## 为什么 SQL 打败了 NoSQL，这对数据的未来意味着什么

### 在被遗弃多年之后，SQL 今天正在卷土重来。怎么会这样这将对…产生什么影响

blog.timescale.com](https://blog.timescale.com/blog/why-sql-beating-nosql-what-this-means-for-future-of-data-time-series-database-348b777b847a/) 

*   [Rick Negrin](https://medium.com/u/69099c2f9012?source=post_page-----861b6cf01e1f--------------------------------) 在【2018 年 7 月的一篇帖子中认为 NoSQL 不是合适的工具:

[](https://www.memsql.com/blog/why-nosql-databases-wrong-tool-for-modern-application/) [## 为什么 NoSQL 数据库是现代应用的错误工具

### 是时候让我们承认我们早就知道的事实了:NoSQL 是很多人的错误工具…

www.memsql.com](https://www.memsql.com/blog/why-nosql-databases-wrong-tool-for-modern-application/) 

*   [Judah Himango](https://dzone.com/users/1304649/judahgabriel.html) 在 2017 年 9 月的一篇文章[中讨论了搬到 NoSQL 的问题](https://dzone.com/articles/the-biggest-challenges-of-moving-to-nosql):

[](https://dzone.com/articles/the-biggest-challenges-of-moving-to-nosql) [## 迁移到 NoSQL - DZone 数据库的最大挑战

### 我们正处于软件世界向 NoSQL 数据存储的转变中，尤其是在网络和移动领域…

dzone.com](https://dzone.com/articles/the-biggest-challenges-of-moving-to-nosql) 

*   这个关于 StackOverflow 的 [2018 问题](https://stackoverflow.com/questions/53427464/postgresql-jsonb-update-multiple-nested-fields?stw=2)由[卫报](https://twitter.com/gdndevelopers)、[玛利亚-利维雅](https://medium.com/u/acfd5c92b9fc?source=post_page-----861b6cf01e1f--------------------------------)的一位前开发人员提出，她是卫报关于交换机的文章的合著者，展示了 PostgreSQL 如何与`jsonb`数据类型一起工作:

[](https://stackoverflow.com/questions/53427464/postgresql-jsonb-update-multiple-nested-fields?stw=2) [## PostgreSQL jsonb 更新多个嵌套字段

### 在 postgresql 数据库中有一个 id 字段和一个 jsonb 字段的表。jsonb 的结构看起来有点像…

stackoverflow.com](https://stackoverflow.com/questions/53427464/postgresql-jsonb-update-multiple-nested-fields?stw=2) 

*   [鲁文·勒纳](https://medium.com/u/4b564dc9965a?source=post_page-----861b6cf01e1f--------------------------------)在 2015 年 1 月于 [Linux Journal](https://www.linuxjournal.com/) 撰文时，曾暗示使用 PostgreSQL 作为使用`jsonb`的文档数据库:

[](https://www.linuxjournal.com/content/postgresql-nosql-database) [## NoSQL 数据库

### 在过去的几年中，计算机世界最有趣的趋势之一是 NoSQL 的快速发展

www.linuxjournal.com](https://www.linuxjournal.com/content/postgresql-nosql-database) 

*   [Olery](https://olery.com/) 也在 2015 年从 MongoDB 迁移回 PostgreSQL】:

 [## 再见 MongoDB，你好 PostgreSQL

### Olery 成立于大约 5 年前。最初是由 Ruby 开发的单一产品(Olery 信誉)…

developer.olery.com](https://developer.olery.com/blog/goodbye-mongodb-hello-postgresql/) 

德里克·奥斯汀博士是《职业编程:如何在 6 个月内成为一名成功的 6 位数程序员 》一书的作者，该书现已在亚马逊上架。