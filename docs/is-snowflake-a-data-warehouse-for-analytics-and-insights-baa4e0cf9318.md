# 雪花是分析和洞察的数据仓库吗？

> 原文：<https://betterprogramming.pub/is-snowflake-a-data-warehouse-for-analytics-and-insights-baa4e0cf9318>

## 分析术语“虚拟仓库”和“数据仓库”之间有一个重要的区别以下是你需要知道的

![](img/3ecb82a45ec5eafa95f068659ff55e74.png)

由 [Darius Cotoi](https://unsplash.com/@dariuscotoi?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/snowflake?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的封面照片

雪花是一个数据分析平台，因为它在运行商业智能(BI)和其他数据分析查询时的出色性能而变得流行。术语“数据仓库”是指用于存储分析数据的任何服务或计算基础设施，尤其是将存储(数据)与计算(所述数据的处理)分开的服务或计算基础设施。

数据仓库的服务器保存着您的数据的副本，这些数据最初存储在其他地方，因此数据仓库类似于在非现场仓库中存储实际库存。

另一方面，雪花的“虚拟仓库”(或者只是“仓库”)的概念与“数据仓库”有一点不同。在雪花中，[仓库](https://www.propeldata.com/blog/what-are-warehouses-in-snowflake-data-analytics-platform)是类似于虚拟机的计算引擎，它们是你扩展雪花的方式。当您需要更多的处理能力来运行更快的查询时，您可以让雪花仓库变得更大。

通俗地说，许多人将雪花数据平台称为数据仓库。毕竟，[数据分析师和工程师](https://www.propeldata.com/blog/what-is-the-difference-between-a-data-engineer-a-data-scientist-and-a-data-analyst)从各种来源(如[数据库](https://www.propeldata.com/blog/what-is-the-difference-between-a-database-and-a-warehouse-in-snowflake))向雪花复制数据。从这个意义上说，你在雪花的平台上存储或“仓库”你的数据。

然而，与私有服务器不同，数据仓库通常将存储空间的费用与计算时间的费用分开。雪花的文档还使用相关术语“数据湖”来强调雪花与大量非结构化或半结构化数据兼容，而不仅仅是具有固定数据库模式的结构化数据(就像 SQL 所必需的)。

# 雪花是数据仓库吗？

雪花是一个数据管理系统，旨在支持商业智能(BI)活动，尤其是数据分析。许多人将雪花称为他们数据分析堆栈中的“数据仓库”，因为它针对存储和计算大量数据进行了优化。

然而，如上所述，当使用雪花时，术语“仓库”具有特定的含义。在雪花术语中，仓库(或“虚拟仓库”)是虚拟计算机时间的临时分配，允许平台执行某些数据库任务。

雪花中的仓库是一个虚拟机(VM)，允许您使用其基于云的 SQL 引擎进行大数据分析。换句话说，仓库就像一个计算集群，分配 CPU(处理速度)和内存(RAM)，但你不必管理它们。根据雪花对该术语的定义，雪花的“仓库”并不是数据仓库，即使你在同一个雪花账户中有[多个虚拟仓库，但大多数人把雪花的平台本身称为数据仓库或数据湖。](https://www.propeldata.com/blog/how-many-virtual-warehouses-can-snowflake-hold)

# 为什么要用雪花做数据仓库？

使用雪花作为数据仓库或数据湖的主要优势是它在处理大量数据时的性能，尤其是在运行复杂的分析查询时。与 SQL 或 noSQL 等传统数据库技术相比，雪花大放异彩。

例如，基于产品类别生成销售收入排行榜，使用雪花将比使用 SQL 更高效、更快速。雪花还能更快地找到销售额最高的商店或收入最高的销售人员。

雪花与更传统的数据库技术的区别在于底层数据库是组织成行还是列。PostgreSQL 或 MySQL 等基于行的数据库记录事务的速度非常快。它们非常适合高速处理客户销售和其他类型交易信息的应用程序。另一方面，像 Snowflake 这样的列数据库针对快速分析大量数据进行了优化，但它在记录每一个发生的事务方面做得不太好。

“事务性能”和“分析性能”之间的这种平衡就是像雪花这样的数据仓库存在的原因，也是术语“数据仓库”变得流行的原因。几乎每个使用雪花的人都将他们的数据从某个原始来源(如 SaaS 应用程序)复制到雪花中，而不是使用雪花作为唯一的数据库技术。通过在雪花中存储数据，运行分析不会降低任何其他应用程序的速度。

# 雪花数据仓库可以用于洞察吗？

使用任何数据仓库都需要大量的手动设置和维护，尤其是在将数据仓库连接到面向客户的应用程序以提供洞察力时。虽然数据分析师可能习惯于使用 SQL 并等待 10 或 15 分钟来完成数据库查询，但这种查询性能不足以构建像面向客户的数据应用程序那样的东西，为客户提供产品内分析和见解。

想想谷歌航班，它处理大量数据来告诉你何时购买机票，这是一个提供有用的产品内洞察作为关键功能的应用程序的例子。如果每次搜索都要花半个小时，这个特性就没用了。客户期望即时且按需获得快速的产品体验和可操作的见解。

当然，可以使用像[三角洲湖](http://delta.io/)和[冰山](https://iceberg.apache.org/)这样的工具来编写定制中间件，以桥接雪花的数据平台和生成客户洞察的数据应用。棘手的部分是[雪花的虚拟仓库一次只为单个用户](https://www.propeldata.com/blog/what-is-a-multi-cluster-virtual-warehouse-in-snowflake-data-platform)优化，而不是数百个并发用户，所以你需要执行大量的定制数据工程。

**编码快乐！❄️**

德里克·奥斯汀博士是《职业规划:如何在 6 个月内成为一名成功的 6 位数程序员》一书的作者，该书现已在亚马逊上出售。