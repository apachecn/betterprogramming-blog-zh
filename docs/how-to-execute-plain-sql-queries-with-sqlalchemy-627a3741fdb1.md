# 如何用 Python 中的 SQLAlchemy 执行普通 SQL 查询

> 原文：<https://betterprogramming.pub/how-to-execute-plain-sql-queries-with-sqlalchemy-627a3741fdb1>

## 了解用 Python 运行 SQL 查询的标准方法

![](img/2253365dbb2a96d9d6eaadb4bd9b23cb.png)

图片来自 [Pixabay](https://pixabay.com/illustrations/server-servers-data-computer-5451985/) 。

在很多情况下，您不想使用 [SQLAlchemy](https://www.sqlalchemy.org/) 的高级[对象关系映射器(ORM)](https://docs.sqlalchemy.org/en/13/orm/) 特性，而只想执行普通的 SQL 查询。这对于到处都使用普通查询的遗留系统来说很常见。此外，对于面向数据分析的项目，使用普通 SQL 查询也更常见、更方便。对于包含大量 JOIN 子句和子查询的 SQL 查询，直接执行它们比将它们翻译成复杂的基于 ORM 模型的代码要简单得多。此外，我们可以将 SQL 查询存储在一个文件中，然后用 [*sqlparse*](https://pypi.org/project/sqlparse/) 和 SQLAlchemy 执行它们。

Python 中可以用来执行普通 SQL 查询的工具有很多，比如 [PyMySQL](https://pypi.org/project/PyMySQL/) 、 [mysql-connector-python](https://pypi.org/project/mysql-connector-python/) 、 [MySQL-python](https://pypi.org/project/MySQL-python/) 等。但是，建议使用 SQLAlchemy 来执行普通的 SQL 查询。这是因为 SQLAlchemy 非常通用，可以用于各种数据库。此外，即使你现在不愿意在你的应用程序中学习和使用 ORM 模型，你很可能有一天会有机会去读或写 ORM 代码。一旦您学会了如何使用 SQLAlchemy 来执行普通的 SQL 查询，那么探索 SQLAlchemy 的其他特性，包括 ORM 模型，将会变得更加简单。

对于本教程，我们需要有一个本地运行的 MySQL 服务器。建议使用 Docker 在本地启动一个 MySQL 容器。这样，您可以使用任何版本的 MySQL，并且可以避免潜在的端口冲突。

```
# Create a volume to persist the data.
$ **docker volume create mysql8-data**# Create the container for MySQL.
$ **docker run --name mysql8 -d -e MYSQL_ROOT_PASSWORD=root -p 13306:3306 -v mysql8-data:/var/lib/mysql mysql:8**# Connect to the local MySQL server in Docker.
$ **docker exec -it mysql8 mysql -u root -proot**
mysql> SELECT VERSION();
+-----------+
| VERSION() |
+-----------+
| 8.0.27    |
+-----------+
1 row in set (0.00 sec)
```

注意事项:

*   为 MySQL 容器创建并装载了一个卷，这样，即使容器重新启动，我们稍后创建的数据库和表也可以持久化。
*   高端口 13306 用于容器，以避免与系统的潜在端口冲突。如果也使用 13306，请选择一个不同的。
*   在命令行上为*根*指定了密码。实际上，您不应该在命令行上暴露 *root* 密码。并且您应该避免直接为您的应用程序使用 *root* ，而是使用一个具有有限权限的帐户。
*   `docker exec`用于直接启动 Docker 容器内的 MySQL 控制台。这样我们就不需要在本地安装 MySQL 客户端了。您可以在这个控制台中执行下面介绍的 SQL 查询，以检查我们稍后将对表进行的更新。

如果我们只想执行普通的 SQL 查询，通常已经创建了数据库和表，您只需要对表记录执行`SELECT/UPDATE/INSERT/DELETE` ( [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) )操作。请在上面打开的 MySQL 控制台中运行以下查询，为本教程准备数据库和表:

在我们使用 SQLAlchemy 连接到我们的数据库并执行 SQL 查询之前，我们需要安装这个包及其依赖项。建议在虚拟环境[中安装软件包](https://medium.com/codex/how-to-create-virtual-environments-with-venv-and-conda-in-python-31814c0a8ec2)，这样就不会弄乱你的系统库。你可以使用`venv`或`conda`在 Python 中创建一个虚拟环境。建议使用`[conda](https://medium.com/codex/how-to-create-virtual-environments-with-venv-and-conda-in-python-31814c0a8ec2)`创建虚拟环境，因为可以在虚拟环境中安装特定版本的 Python:

为本教程安装的软件包:

*   SQLAlchemy —将用于执行普通 SQL 查询的主包。
*   [PyMySQL](https://pypi.org/project/PyMySQL/)—SQLAlchemy 使用它来连接 MySQL 数据库并与之交互，稍后将对此进行更详细的介绍。
*   [*密码术*](https://pypi.org/project/cryptography/) —由 SQLAlchemy 用于认证。
*   [*sqlparse*](https://pypi.org/project/sqlparse/) —用于将原始字符串解析成 SQL 查询。
*   [*代码计时*](https://pypi.org/project/codetiming/) —用于检查某段代码的执行时间。
*   [*ipython*](https://pypi.org/project/ipython/) —用于更方便地执行交互式 python 代码。

## **创建一个 SQLAlchemy 引擎和连接**

要使用 SQLAlchemy 执行普通的 SQL 查询，我们需要首先创建一个引擎和一个连接实例。

[引擎](https://docs.sqlalchemy.org/en/14/core/engines.html)是任何 SQLAlchemy 应用程序的起点。该引擎结合了一个[池](https://docs.sqlalchemy.org/en/14/core/pooling.html)和一个[方言](https://docs.sqlalchemy.org/en/14/core/internals.html#sqlalchemy.engine.Dialect)，并提供了一种连接到数据库并与之交互的方式。

连接池是一组可以有效重用的活动的长期运行的数据库连接。这是必要且重要的，因为创建新连接是有开销的。通过重用池中的连接，应用程序可以工作得更快。正常情况下，可以同时创建和使用的连接数量有一个上限，我们将在后面看到。

由于 SQLAlchemy 可以处理多种类型的数据库，因此每种类型的数据库都被视为一种方言。从技术上讲，方言定义了特定数据库的名称和底层 Python**D**ATA**b**ase**API**规范(DBAPI)。比如方言可以是`mysql`、`postgresql`、`oracle`、`sqlite`等。

要创建引擎实例，我们需要首先定义一个数据库 URL，其格式如下:

```
dialect[+driver]://user:password@host:port/dbname
```

*   方言——上面介绍的方言，可以是`mysql`、`postgresql`等。
*   驱动程序—用于连接数据库并与之交互的 DBAPI 的名称。如果未指定驱动程序，将使用默认驱动程序。对于 MySQL，默认驱动是 [MySQL-Python](https://pypi.org/project/MySQL-python/) 。然而，MySQL-Python 不能正确支持 Python3，现在应该被视为已弃用。因此，我们应该始终提供最佳实践的驱动因素。对于 MySQL，常用的驱动有 [mysqlclient](https://docs.sqlalchemy.org/en/14/dialects/mysql.html#module-sqlalchemy.dialects.mysql.mysqldb) 、 [PyMySQL](https://docs.sqlalchemy.org/en/14/dialects/mysql.html#module-sqlalchemy.dialects.mysql.pymysql) 、 [MySQL-Connector](https://docs.sqlalchemy.org/en/14/dialects/mysql.html#module-sqlalchemy.dialects.mysql.mysqlconnector) 等。一般来说，如果没有批量插入/更新，使用哪个驱动程序并不重要，只要它被积极地维护。但是，不同的驱动程序有不同的系统依赖性，有些可能无法安装在您的计算机上。如果一个驱动程序无法安装，您可以尝试安装另一个。在本教程中，我们将使用 [PyMySQL](https://pypi.org/project/PyMySQL/) ，这是一个积极维护和使用的 MySQL 驱动程序。它可以很容易地安装在大多数系统上。如果您想了解更多关于不同驱动程序的性能，请查看[这个堆栈溢出讨论](https://stackoverflow.com/questions/43102442/whats-the-difference-between-mysqldb-mysqlclient-and-mysql-connector-python)。
*   用户、口令、主机、端口、数据库名-目标数据库的用户名、口令、主机名、端口和默认数据库/模式。请在实践中针对自己的案例使用相应的。

现在让我们为 MySQL 数据库创建一个引擎，从上面的 Docker 容器开始:

注意，我们可以将一组定义特殊配置的键/值对传递给`create_engine()`函数。最常用的键是`echo`，它记录引擎执行的 SQL 查询。它有助于调试，但是不要在生产环境中使用它。此外，对于 MySQL，我们通常需要指定`pool_size`和`pool_recycle`，它们分别定义了多少个连接将在池中保持打开，以及多长时间后一个连接将被回收到池中。这样，我们可以控制活动连接的数量，并且不会有已经关闭的过时连接。这是必要的，因为如果在[八小时](https://docs.sqlalchemy.org/en/14/core/engines.html#sqlalchemy.create_engine.params.pool_recycle)内没有检测到连接活动，MySQL 会自动断开连接。

创建了引擎实例后，我们可以从它创建一个[连接](https://docs.sqlalchemy.org/en/14/core/connections.html#sqlalchemy.engine.Connection)实例。我们不应该直接使用引擎执行 SQL 查询，而应该通过连接实例来执行，该实例为包装的 DBAPI 连接提供高级功能。

用`Engine.connect()`方法创建一个连接对象。创建的连接对象是从前面介绍的连接池中签出的单个 DBAPI 连接。当连接关闭时，它将被回收到池中，以便重新使用。通过重用池中已经创建的连接，我们节省了每次创建新连接的开销，这是相当繁重的。您可以尝试先建立一个连接，然后关闭它，然后再次创建一个新的连接。第二次应该比第一次快得多，因为连接是直接从池中签出的。

让我们用代码测试一下。您可以复制以下代码并在 iPython 中运行它:

我们可以看到，第二个连接的创建速度比第一个快得多。这是因为第二个是直接从池中获取的，它存储在内存中以便有效地重用。如果您的数据库是远程数据库，时差会更明显。要了解 Python 中的[日志](https://lynn-kwong.medium.com/stop-using-print-in-your-python-code-for-logging-use-the-logging-module-like-a-pro-66fb0427d636)和[定时器](https://medium.com/codex/learn-more-than-the-basics-about-the-date-time-and-timezone-in-python-b88e6a1071fc)的更多信息，请查看链接中的相应文章。

既然我们已经学习了如何创建引擎和连接，现在我们可以开始执行普通的 SQL 查询了。

虽然在遗留系统中经常使用，但是直接向`Connection.execute()`传递一个普通字符串是[不赞成的](https://docs.sqlalchemy.org/en/14/core/connections.html#sqlalchemy.engine.Connection.execute)，我们应该使用`[text()](https://docs.sqlalchemy.org/en/14/core/sqlelement.html#sqlalchemy.sql.expression.text)`来指定一个普通的 SQL 查询字符串。让我们试着从上面创建的`product_stocks`表中选择:

请注意，如果模式与在`db_url`中指定的模式相同，您可以在普通 SQL 查询中省略数据库名称，在 MySQL 中称为[模式](https://dev.mysql.com/doc/refman/8.0/en/system-schema.html#:~:text=The%20mysql%20schema%20is%20the,used%20for%20other%20operational%20purposes.)。然而，通常我们在一个 MySQL 数据库中会有不止一个模式。因此，为了清晰起见，建议总是在普通 SQL 查询中指定模式。

此外，我们需要在使用后关闭连接，以便它可以被回收
回池中。通过这种方式，其他进程可以更有效地重用该连接，正如上面所证明的那样。作为替代，我们可以把它放在一个`with()`上下文块中，这样在里面的代码被执行后，连接可以自动关闭。

建议使用`with()`上下文管理器创建一个连接，这样您就不会忘记最后关闭连接。

通常，我们需要向普通的 SQL 查询传递一些参数，以便根据指定的条件只选择一些行。这可以通过`[TextClause.bindparams()](https://docs.sqlalchemy.org/en/14/core/sqlelement.html#sqlalchemy.sql.expression.TextClause.bindparams)`方法完成。顺便提一下，`text()`构造返回一个`[TextClause](https://docs.sqlalchemy.org/en/14/core/sqlelement.html#sqlalchemy.sql.expression.TextClause)`对象，它代表 SQlAlchemy 中的一个文本 SQL 文本片段。

用`text()`指定参数的语法是`= :VAR_NAME`。注意冒号放在等号后面，冒号后面没有空格！`VAR_NAME`可以是 Python 中任何有效的变量名。变量名用于在使用`Connection.execute()`方法执行查询时指定一个值。

正如我们看到的，用这种语法指定多个参数并不方便。事实上，我们不应该在这些场合使用`text()`和`Connection.execute()`，因为这里可以找到。相反，对于这些情况，我们应该使用`[Connection.exec_driver_sql()](https://docs.sqlalchemy.org/en/14/core/connections.html#sqlalchemy.engine.Connection.exec_driver_sql)`。

对于`Connection.exec_driver_sql()`，我们不传递一个由`text()`构造的`TextClause`对象，而是直接传递一个普通的 SQL 查询。此外，我们可以使用字典来指定参数，并使用字典列表来支持多执行。

`Connection.exec_driver_sql()`对于有多个参数的`SELECT`查询非常有用，对于通常有多个参数的`INSERT/UPDATE`查询也非常有用。让我们在实践中看到它。

干杯，成功了！如果你不熟悉 Python 中的百分号(%)字符串格式，可以看看[这篇文章](https://medium.com/codex/special-python-string-formatting-in-logging-and-pint-unit-conversion-fddb51f3d03a)。特别是，请注意无论值的类型是什么，在普通 SQL 查询中总是使用`%()s`。SQLAlchemy 自动处理这些类型。

我们可以在普通 SQL 查询中使用其他运算符，而不仅仅是等号(=)运算符:

对于要检查的值列表，就像使用`IN`操作符一样，我们可以传递一个列表或一组元素。

我们现在已经介绍了如何在不同的场合执行普通 SELECT 查询，这也是使用 SQLAlchemy 执行普通 SQL 查询的最常见用例。然而，在实际情况中，您可能还想执行普通的`INSERT/UPDATE/DELETE`查询。现在就让我们来探索一下。

执行普通`INSERT/UPDATE/DELETE`查询的语法与上面介绍的使用`Connection.exec_driver_sql()`的语法非常相似。例如，让我们在表中插入一条新记录:

使用上面的代码，我们在`product_stocks`表中插入了一个新行。特别是`lastrowid`返回最后插入行的主键的值。

我们可以通过传入字典列表来插入多行:

现在让我们尝试更新一行:

这是删除一行的方法:

在我们进入关于如何执行存储在文件中的 SQL 查询的下一节之前，让我们检查一个 MySQL 特有的特性。如果要插入的记录已经存在于数据库中，您将得到重复条目错误:

```
IntegrityError: (pymysql.err.IntegrityError) (1062, "**Duplicate entry** 'Cellphone-2022-01-10 08:00:00' for key 'product_stocks.uq_category_check_time'")
```

您可以通过在数据库中插入一条带有现有`category`和`check_time`的记录来重现此错误。这将触发该错误，因为`category`和`check_time`是表`data.product_stocks`中的唯一键。

为了解决这个问题，我们可以使用`[ON DUPLICATE KEY UPDATE](https://dev.mysql.com/doc/refman/8.0/en/insert-on-duplicate.html)`语法:

您现在可以多次执行这个查询，不会出现“重复输入”错误。

最后，让我们看看如何执行存储在文本文件中的多个 SQL 查询。如果您希望使用 CRON 作业或 Airflow 重复执行一些 SQL 查询，这非常有用。作为数据工程师或数据科学家，您可能会发现它在您的工作中非常方便。

要从一个文本文件执行多个 SQL 查询，我们需要使用本教程开始时安装在我们的虚拟环境中的 [*sqlparse*](https://pypi.org/project/sqlparse/) 模块。 *sqlparse* 可以从字符串中剥离注释，并将其拆分成多个可以单独执行的 SQL 查询:

如前所述，我们可以使用`sqlparse.format()`从原始字符串中提取注释，然后使用`sqlparse.split()`将其分割成多个可执行的普通 SQL 查询。

我们将用于演示的[虚拟 SQL 文件](https://gist.github.com/lynnkwong/3e2c7c48d3152897fbca6d91885c3aea)的内容如下:

现在让我们使用 *sqlparse* 来解析文件，然后使用 SQLAlchemy 来执行查询。注意，由于我们通常不需要为存储在文件中的 SQL 查询传递参数，我们可以使用本教程前面介绍的`text()`构造。

请注意，我们在创建引擎时添加了`echo=True`,因此执行的查询可以在控制台中打印出来:

干杯！我们现在已经成功地执行了文本文件中存储的所有 SQL 查询。

在本文中，我们介绍了 SQLAlchemy 的基本概念，以及如何使用`Connection.execute()`和`Connection.exec_driver_sql()`方法执行普通的 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) SQL 查询。重要的是，您还学习了如何使用`sqlparse`来解析文件中的原始数据，并生成可执行的普通 SQL 查询。这种技术对于数据管道和分析非常方便，尤其是对于遗留系统。

掌握了本教程的知识，你应该有足够的信心在 Python 中使用数据库，并且为学习更高级的对象相关映射器(ORM)做好了充分的准备，这些将在后面的教程中介绍。稍后您会发现，我们可以使用 ORM 以更方便、更 Pythonic 化的方式与数据库交互。

相关文章:

*   [学习基础知识并开始使用 SQLAlchemy ORM](https://lynn-kwong.medium.com/learn-the-basics-and-get-started-with-sqlalchemy-orm-from-scratch-66c8624b069?source=your_stories_page----------------------------------------)