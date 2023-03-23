# MongoDB 的三大替代方案

> 原文：<https://betterprogramming.pub/the-top-three-alternatives-to-mongodb-in-2019-399b324e53d8>

## JAM Stack、PostgreSQL 和 Amazon 的 DynamoDB 都是 MongoDB 的竞争对手

![](img/436759d0092395c15ae248e91fb6be66.png)

由[玛琳·普鲁西克](https://unsplash.com/@soppeldunk?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

[NoSQL](https://en.wikipedia.org/wiki/NoSQL) 是一个名字，用来描述任何不符合 [SQL 关系模型](https://en.wikipedia.org/wiki/Relational_model#SQL_and_the_relational_model)的数据库类型。

NoSQL 数据库起源于 2009 年，当时 MySpace 等网站规模扩大:

> NoSQL 的出现是因为当时的数据库无法处理所需的规模。
> 
> — [JAM Stack 是 JavaScript、API 和预呈现标记的缩写，它是一种完全不需要数据库的无服务器架构。](https://medium.com/u/69099c2f9012#1: JAM Stack</h1><p id=)
> 
> [JAM Stack 甚至可以取代文档数据库，只要数据可以转移到用](https://medium.com/u/69099c2f9012#1: JAM Stack</h1><p id=) [Markdown](https://www.youtube.com/watch?v=LcgPRe7rLT4) 编写的大量 JSON 文件中。
> 
> 当使用 JAM Stack 时，整个应用程序然后被编译(或构建)，通常使用类似 [Gatsby](https://www.gatsbyjs.org/) 的工具。
> 
> 不幸的是，这意味着任何改变都需要重建网站。
> 
> 另一方面，如果通过 API 访问，动态内容可以实时改变，比如通过 [Disqus](https://disqus.com/api/docs/) 处理的评论。
> 
> ## 果酱叠的优点
> 
> 闪电般快速的内容交付是 JAM Stack 的最大优势，因为没有服务器——整个应用程序都在客户端运行。
> 
> 同时，也有最大的 SEO 因为应用程序是预编译的，它是作为静态页面提供的，而不是按需生成的。
> 
> JAM Stack 的另一个优势是最小的托管成本。例如，静态页面基本上是[免费托管在 AWS 的](https://aws.amazon.com/getting-started/projects/host-static-website/)。
> 
> ## 卡纸堆叠的缺点
> 
> 每一个变化都要重新构建。这意味着该应用程序需要重新编译后才能在线发布。
> 
> 而且，网站越大，构建时间越长。每次都是。
> 
> 因此，JAM Stack 通常不太适合需要频繁发布、大量数据或实时编辑数据记录的应用程序。

# MongoDB 选择#2: PostgreSQL

使用 SQL 的倡导者列举了 SQL 架构的持续流行和在 SQL 数据库上执行操作分析的卓越能力:

> 关系数据库已经发展了。他们可以处理几乎所有的工作负载，满足现代应用程序所需的可扩展性、可靠性和可用性要求。
> 
> — [DynamoDB 是亚马逊网络服务](https://medium.com/u/69099c2f9012#3: DynamoDB</h1><p id=)[的 NoSQL 产品。MongoDB 和 DynamoDB 都用任意模式存储类似 JSON 的数据。](https://aws.amazon.com/)
> 
> DynamoDB 最近[增加了一个名为](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/EncryptionAtRest.html) [*静态加密*](https://en.wikipedia.org/wiki/Data_at_rest#Encryption) 的特性，现在它与 MongoDB 竞争激烈，同时仍然是一个 NoSQL 数据库。
> 
> 这意味着从 MongoDB 迁移到 DynamoDB 非常容易。
> 
> ## DynamoDB 的优势
> 
> DynamoDB 是 AWS 上的旗舰 NoSQL 数据库，所以它的固有优势是 [AWS 平台的](https://aws.amazon.com/about-aws/)成本、速度和可靠性。
> 
> 作为一个 AWS 产品，它很容易与 [AWS Lambda](https://aws.amazon.com/lambda/) 和 [API Gateway](https://aws.amazon.com/api-gateway/) 集成。
> 
> 对于目前使用 Mongo 的人来说，DynamoDB 的一大优势是用户可以执行从 MongoDB 到 DynamoDB 的实时迁移。
> 
> 最棒的是没有基础设施需要管理——由亚马逊处理。
> 
> ## DynamoDB 的缺点
> 
> 如果我认为 NoSQL 丢失记录的可能性太高，那么 DynamoDB 对我来说不是一个好的选择。
> 
> DynamoDB 可能存在可伸缩性问题，就像那些与所谓的[热键](https://read.acloud.guru/why-amazon-dynamodb-isnt-for-everyone-and-how-to-decide-when-it-s-for-you-aefc52ea9476)相关联的问题，这些记录被评估的频率比其他记录高得多。
> 
> 此外，与 MongoDB 相比，DynamoDB 支持不太复杂的数据类型，并且是 AWS 专有的产品。
> 
> ## DynamoDB 不是 MongoDB
> 
> 像 [Samantha Atkins](https://medium.com/u/114261c0cd6a?source=post_page-----399b324e53d8--------------------------------) 和 [Fredrik Erasmus](https://medium.com/u/32d514a8ab9c?source=post_page-----399b324e53d8--------------------------------) 这样有帮助的读者花时间回应了这篇文章，指出了 DynamoDB 的其他缺点。
> 
> *   使用 DynamoDB 进行索引是很困难的，如果没有全表扫描，它就没有通用查询支持。
> *   DynamoDB 甚至很难从集合中检索简单的文档数，而且它不使用[游标](https://en.wikipedia.org/wiki/Cursor_(databases))。
> *   DynamoDB 的热键和热分区需要专业技术才能有效使用。
> *   对于 AWS 上的 DynamoDB，您必须根据 rcu(读容量单位)和 wcu(写容量单位)来指定“吞吐量”，这大大增加了项目的复杂性。
> 
> DynamoDB 被称为“我用过的最不友好的持久性解决方案”事实上，MongoDB 自己的广告文案[列出了 MongoDB 相对于 DynamoDB 的十几个优势。](https://www.mongodb.com/compare/mongodb-dynamodb)
> 
> 因此，在从 MongoDB 切换到 DynamoDB 之前，一定要三思而行，尽管两者之间有明显的相似之处。

# 我的看法

> "集装箱赢得了这场战斗，但将输给无服务器."
> 
> — [西蒙·沃德利](https://medium.com/u/8aec33a1b502?source=post_page-----399b324e53d8--------------------------------)引自[黑客报](https://hackernoon.com/forget-docker-the-future-is-jamstack-aae5bcaf4616)

JAM Stack 是当今最酷的技术之一。它的速度快得不可思议，易于访问，而且对 SEO 友好，而且静态文件可以在 AWS 上免费托管。

然而，JAM Stack 只适合那些拥有构建步骤和每次修改都要重新编译的奢侈应用。

如果你问我，我会说即使是非常大的网站也可以通过预定的编译，比如每小时一次，建立在一个堆栈上。卡纸栈是 *rad* 。

这对于新闻网站来说可能不起作用，但是许多动态网站不需要比这更频繁的内容更新，静态网站当然也不需要。

## SQL 与 NoSQL

如果需要一个编译步骤是一个项目的决定性因素，那么它归结为 SQL vs NoSQL: PostgreSQL 或 DynamoDB。

PostgreSQL 不必托管在 Amazon 上，尽管它可以托管在 Amazon RDS 上。DynamoDB 是由亚马逊开发的，并且是亚马逊独有的。

SQL 数据库本来就更强大，但也更复杂。他们的优势在于商业分析和搜索。

NoSQL 数据库更容易建立，对许多项目来说足够了。这就是为什么卫报[为他们的 230 万篇文章考虑 DynamoDB](https://www.theguardian.com/info/2018/nov/30/bye-bye-mongo-hello-postgres) 的原因。

最终，他们选择了 PostgreSQL，因为 DynamoDB 在 2017 年没有提供静态加密，但如果他们今天考虑从 MongoDB 迁移，他们很可能会做出不同的决定。

# 总结

JAM Stack(无服务器架构)是一个很好的选择，尤其是对于任何静态的网站，这也是一项需要注意的伟大技术。

[PostgreSQL](https://www.postgresql.org/) 有一个庞大的社区，在一个可靠、功能丰富、性能卓越的开源包中提供 SQL 的所有功能。

[DynamoDB](https://aws.amazon.com/dynamodb/) 是 MongoDB 的替代产品，为您提供规模、安全性和速度，而无需维护任何基础设施。

## 额外资源

*   了解 JAMstack 的一个好方法是站点 [WTF 是 JAM Stack？](https://jamstack.wtf/):

[](https://jamstack.wtf/) [## JAMstack WTF

### 以下提示将帮助您充分利用堆栈中的精华。内容交付网络，因为所有的标记和…

jamstack.wtf](https://jamstack.wtf/) 

*   网站 [PostgreSQL 教程](http://www.postgresqltutorial.com/what-is-postgresql/)讲述了是什么让“PostgreSQL 从其他数据库管理系统中脱颖而出”:

 [## 什么是 PostgreSQL

### 摘要:在本教程中，您将了解 PostgreSQL 以及使 PostgreSQL 从其他语言中脱颖而出的特性

www.postgresqltutorial.com](http://www.postgresqltutorial.com/what-is-postgresql/) 

*   [CMARIX TechnoLabs 博客](https://www.cmarix.com/benefits-of-amazon-dynamodb/)解释了 DynamoDB 的优势:

[](https://www.cmarix.com/benefits-of-amazon-dynamodb/) [## Amazon DynamoDB 的优势

### 为了满足 IT 世界的数据存储需求，您需要依赖数据库管理系统(DBMS)…

www.cmarix.com](https://www.cmarix.com/benefits-of-amazon-dynamodb/) 

*   如果你问 Mongo 的人，MongoDB 比 dynamo db[好得多:](https://www.mongodb.com/compare/mongodb-dynamodb)

 [## 比较 DynamoDB 和 MongoDB

### MongoDB 的查询语言使开发人员能够构建可以在多种环境中查询和分析数据的应用程序

www.mongodb.com](https://www.mongodb.com/compare/mongodb-dynamodb) 

## 加入我的电子邮件列表，免费获取我所有的媒体文章。