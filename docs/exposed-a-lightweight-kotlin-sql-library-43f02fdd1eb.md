# exposed——一个轻量级 Kotlin SQL 库

> 原文：<https://betterprogramming.pub/exposed-a-lightweight-kotlin-sql-library-43f02fdd1eb>

## Kotlin 公开框架指南

![](img/73f9d936958086b1e39c980d5be94335.png)

马库斯·温克勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 这篇文章的要点

根据我的经验，编写和执行查询需要仔细分析。编写定制查询是可能的，但是很复杂。如果你使用 Kotlin，那么使用 [Exposed](https://github.com/JetBrains/Exposed) 就是你的解决方案。在本文中，您将学习如何创建数据库表以及如何对它们执行 CURD 操作。

# 介绍

暴露的是 JetBrains 的一个轻量级 SQL 库，用纯 Kotlin 编写。当我在使用 Ktor 寻找一个简单的 SQL 库来处理服务器端应用程序中的数据库时，我找到了它。

暴露的是 Kotlin 的一个 ORM 框架。它提供了两种级别的数据库访问:类型安全的 SQL 包装 DSL 和轻量级数据访问对象。在本文中，我们将讨论使用 Kotlin DSL 的第一种方法。

Exposed 可以充当中间层，连接众多数据库引擎，并通过奇特的 Kotlin DSL 语言以不同的方式编写乏味的查询。H2、MySQL、MariaDB 和 PostgreSQL 是一些公开支持的数据库。

# 综合

说够了；让我们从集成部分开始。在`build.gradle`文件中的`dependencies`标签下添加下面一行，以导入您的项目。

```
implementation("**org.jetbrains.exposed:exposed:0.12.1**")
```

回到数据库部分，在这个例子中，我们使用 H2 数据库。要导入它，请添加以下行:

```
implementation("**com.h2database:h2:1.4.191**")
```

仅此而已。我们已经完成了整合部分。

# 连接到数据库

我们通过传递 URL 和驱动程序，使用`Database`类创建一个数据库实例。看一看:

```
**Database.connect**(url ="jdbc:h2:mem:test", driver = "org.h2.Driver")
```

或者，出于安全原因，我们也可以指定用户和密码。看一看:

```
**Database.connect**(
   url ="jdbc:h2:mem:test", driver = "org.h2.Driver",
   **user =** "James", **password =** "Bond003")
```

# 定义表格

现在我们已经完成了数据库连接，下一步是创建表。正如我前面说过的，使用 Exposed，我们不需要使用通常的数据库语法和查询。

相反，我们定义一个 Kotlin 对象并用`Table`扩展它。然后，我们将表中必要的列声明为类内部的变量。如果你来自 Android 开发背景，这类似于为 [Room](https://developer.android.com/topic/libraries/architecture/room) 数据库库创建一个`Entity`类。看一看:

通过编写 12 行代码，我们在数据库中创建了两个表。Kotlin 对象声明用于创建单例实例。一旦一个对象被声明，我们就可以直接引用它的名字而不用创建一个实例(使用 Kotlin 的一个)。

以下所有函数都是`Table`类的一部分:

*   `integer`:创建整数列。将列名作为其参数。
*   `varchar`:创建一个字符串列
*   `primaryKey`:创建主键类型的列
*   `references`:这是`Table`中的 Kotlin-extension 函数，用于提供外键关系

# 创建表格

创建表的第一种也是最简单的方法是调用`SchemaUtils`类上的`create`函数，并传递所有想要创建的表。看一下代码:

```
SchemaUtils.**create**(Actors, Movies)
```

这很好，对吧？但是，如果您已经创建了这些表，现在又想添加新列，该怎么办呢？别担心，Exposed 已经覆盖了你——我们可以用`createMissingTablesAndColumns`代替`create`函数。

```
SchemaUtils.**createMissingTablesAndColumns**(Actors, Movies)
```

这样做的目的是，只有当表不存在时，它才会创建表，另一个很酷的事情是，如果可能的话，它会为现有的表添加缺少的列(如果这些列可以为空或者有默认值)。

请记住:它应该在`transaction`内部完成，这个函数采用了`org.jetbrains.exposed.sql.Transaction` 类的扩展 lambda。这个 lambda 中的所有内容都在事务上下文中执行。

# 问题

此时，我们已经建立了数据库连接并创建了表，这意味着我们已经准备好从数据库中存储和检索数据。多亏了 Kotlin 扩展特性，我们不需要做太多工作。公开的框架包含扩展函数，以尽可能简单的方式执行数据库查询。

## 选择全部

这个函数用于将表中的所有行和列转换成查询格式。我们可以使用 Kotlin `map`函数将结果转换成适当的格式。看一看:

## 选择列的子集

现在，并不总是需要将所有的列都放入表中。例如，要显示一个电影列表，我们只需要一个电影名称和图像。为此，Exposed 有一个使用`slice`函数的内置解决方案。看一看:

## 插入数据

要在表中创建一行，我们可以使用`insert`函数。看一看:

```
Actors.**insert** {
     it[name] = "Actor name"
     it[image ] = "http://sampleurel.png"
}
```

## 更新数据

是时候更新已经插入数据库的数据了。类似于`insert`，我们有一个`update`函数。看一看:

```
Actors.**update** ({ Actors.actorId eq 3}) {
     it[name] = "Captain Marvel"
 }
```

## 删除数据

这是最容易的部分；我们可以用`deleteWhere` 的方法删除数据。该函数删除满足传递条件的所有行。看一看:

```
StarWarsFilms.**deleteWhere** ({ Actors.actorId eq 9})
```