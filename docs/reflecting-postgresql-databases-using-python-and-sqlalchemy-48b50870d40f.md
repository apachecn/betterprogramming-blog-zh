# 用 Python 和 SQLAlchemy 反映 PostgreSQL 数据库

> 原文：<https://betterprogramming.pub/reflecting-postgresql-databases-using-python-and-sqlalchemy-48b50870d40f>

## 解释 ORM 中的反向迁移

![](img/937de2660eb299933005d92ac0c0b767.png)

图片来源:[https://www.andreafiori.net/](https://www.andreafiori.net/)

我们所说的“反映数据库”是什么意思？听起来很奇怪，不是吗？别担心，一切都会解释清楚的。

你可能听说过 ORM 世界里的“迁移”。这是将对象转换成真正的数据库表和关系的操作。太好了，那如果你想反过来呢？那叫“反射”。

当你已经有了一个装满数据的数据库，并且需要使用 ORM 来处理它时，反射是很有用的。

# 反映单个表格

首先，我们将只把一个表反射到一个 Python 对象，让我们从创建那个表并插入一些数据开始。我们假设您已经安装了 PostgreSQL。

运行以下查询来创建包含三列(id、用户名和电子邮件)的帐户表:

```
CREATE TABLE accounts(                                   
id serial PRIMARY KEY, 
username VARCHAR(50) UNIQUE NOT NULL, 
email VARCHAR(256) UNIQUE NOT NULL);
```

我们的桌子准备好了，现在让我们输入一些数据

```
INSERT INTO accounts (username, email) VALUES ('user1', 'user1@gmail.com'), ('user2', 'user2@gmail.com');
```

我们有一个充满数据的表，现在让我们反映它并查询它。
首先我们需要安装 *SQLAlchemy、*这是使用的 ORM，以及 *psycopg2* 这是 Python 的原生 PostgreSQL 驱动程序:

```
pip3 install sqlalchemy psycopg2
```

> linux 上的 psycopg2 依赖于 python3-devel 和 postgresql-devel 包，所以一定要安装它们

下面的代码将表反映给一个类，然后查询数据:

现在我们的表被成功地反映出来，查询也正常工作了，是时候反映整个数据库了！是的，在生产系统中，您可以找到数十或数百个表，而不仅仅是一个单独的表。

# **反映数据库**

我们将模拟一个生产数据库，其中包含许多表以及它们之间的关系。一种快速的方法是导入一个数据库示例。我在网上找到的一个很好的例子是 dvd 租赁数据库——你可以在这里下载。

这是一个 zip 存档文件——解压它，您将得到一个 tar 存档文件。要将 tar 存档导入 PostgreSQL，首先创建一个新的数据库:

```
CREATE DATABASE dvdrental;
```

现在让我们使用 pg_restore 导入它:

```
pg_restore -U postgres -d dvdrental dvdrental.tar
```

我们的数据库准备好了。如果您尝试使用您最喜欢的 PostgreSQL 客户端连接到它，您会发现 15 个充满数据的表——很棒吧？但是如何使用 SQLAlchemy 来反映这一点呢？

SQLAlchemy 有一个名为 *automap 的扩展。它从数据库中自动生成类和关系。让我们试一试:*

如果运行上面的代码，将会打印出一个包含 15 个表名的列表。这些名称是从数据库表中反映出来的类！现在的问题是如何查询它们。

首先，我们需要将代码扫描的数据库表映射到 Python 对象。此外，我们需要一个会话来进行查询。添加以下代码:

这很好——我们可以从反射数据库中查询单个表。但是关系呢？

别担心，SQLAlchemy 会帮你处理的。automap 扩展自动反映关系，并支持一对多、多对多和多对一。automap 反映的关系将被命名为 <related_table>_collection。</related_table>

我们的示例数据库中的示例关系是表电影和表语言之间的关系——多对一关系。所以我们来拿一个英文片列表(要知道英文是语言表的第一排):

完成了吗？真快！

我知道——这不是一个深入的指南，但这里的目标是用简单的例子阐明数据库反射。

如果你想进一步阅读，请关注 [SQLAlchemy 官方文件](https://docs.sqlalchemy.org/en/13/)。