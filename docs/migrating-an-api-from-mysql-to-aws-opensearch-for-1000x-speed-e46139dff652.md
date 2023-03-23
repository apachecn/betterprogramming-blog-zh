# 在将一个 API 从 MySQL 迁移到 AWS OpenSearch 后，我们如何将响应时间提高了 1000 倍

> 原文：<https://betterprogramming.pub/migrating-an-api-from-mysql-to-aws-opensearch-for-1000x-speed-e46139dff652>

## 优化 API 响应时间

![](img/aec9be5aa58c14e3179e58539dba4118.png)

照片由[卡拉·富勒](https://unsplash.com/@caraventurera?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/cheetah-running?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

合著者— [莫希特·塞加尔](https://medium.com/u/3c199e7ac817?source=post_page-----e46139dff652--------------------------------)，[加里玛·马哈詹](https://medium.com/u/b8554f4c2ed9?source=post_page-----e46139dff652--------------------------------)

在生产中的一个遗留 API 繁忙的一天，我们开始收到响应时间缓慢的警报。API 用了 **~70 秒**来响应客户端的正常流量！这是寻求优化的巨大动力。

# 灯光，摄像机…

因此，我们从理解 API 在做什么以及它是如何做的开始。

该应用程序在 MySQL 数据库中存储了图书及其作者的目录。大约有 5800 万本书，一本书有 1 家出版社。

下面是书和作者的表格结构。

回到 API，用例是在 UI 上的自动完成特性中使用 API 来搜索特定出版商出版的书籍，并将用户的查询字符串与书籍名称或描述的前缀进行匹配。

API 中使用的 MySQL 查询如下所示:

```
select book_uuid_bin, 
       display_name, 
       normalized_name, 
       description,       
       author_uuid_bin 
from book
where 
     ((lower(display_name) like lower("%Software E%") or  lower(description) like lower("%Software E%")) and publishing_house_uuid_bin = UUID_TO_BIN("d2230981-e570-5ba4-9a3a-16028c51d54f"))order by display_name asc limit 100;
```

SQL 查询使用`~70 seconds`来查询数据库表，即使该查询是在单个表上完成的，并且不涉及与作者表的连接。

我们在 where 子句中使用的列上有索引。然而，以下是我们在这个实现中可以看到的问题-

1.  显示名称和描述等列是`VARCHAR`类型。
2.  在`VARCHAR`类型列上使用带有 or 子句的`LIKE`运算符。
3.  `ORDER BY`被使用。
4.  `WHERE`子句中使用的所有列都缺少复合索引。
5.  该表包含 5800 万条记录。

我们尝试在查询中使用的列上创建一个复合索引，但是我们知道这不会有什么帮助，因为在 RDBMS 数据库的一个大表上搜索一个`VARCHAR`列上的文本是没有效率的。

我们知道 Elasticsearch 的[全文搜索](https://www.elastic.co/guide/en/elasticsearch/reference/current/full-text-queries.html)功能，我们希望在我们的用例中探索它。由于我们使用 AWS 作为我们的云提供商，因此我们选择使用 [AWS OpenSearch](https://aws.amazon.com/opensearch-service/) 服务。

> Amazon OpenSearch 服务是一项托管服务，它使得在 AWS 云中部署、操作和扩展 OpenSearch 集群变得非常容易。亚马逊 OpenSearch 服务是亚马逊 Elasticsearch 服务的继承者。

# 开始行动…

我们使用一个脚本将表数据从 MySQL 加载到 AWS OpenSearch 集群中。数据迁移在几个小时内完成。

我们为我们的索引和 1 个复制因子保留了 5 个碎片。

我们为我们的用例编写了一个等价的 OpenSearch 查询，如下所示

API — `POST /books-catalog/_search`

结果—

**我们的 API 运行时间不到 70 毫秒**

API 响应时间提高了 1000 倍！

## OpenSearch 全文搜索的一些细节

当在弹性搜索中索引(创建)文档时，AWS OpenSearch 对字符串类型字段使用文本分析器。

文本分析器将一个字符串字段拆分成标记，为标记构建内部索引，然后根据查询中提供的标记应用匹配。

# 权衡取舍

我们不想为了从 MySQL 转换到 AWS OpenSearch 而重写整个服务。我们希望尽快将解决方案投入生产。所以我们决定只在这个特殊的用例中使用 OpenSearch。

这种速度的代价是额外的维护。我们现在需要在 OpenSearch 中维护一份数据拷贝。但是由于数据大部分是静态的——很少对现有记录进行更新，因此维护工作非常少。

这篇文章强调了为实现业务用例选择正确的数据库引擎的重要性。

快乐编码。