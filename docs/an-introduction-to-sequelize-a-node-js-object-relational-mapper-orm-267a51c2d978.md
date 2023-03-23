# Sequelize 简介，Node.js 对象关系映射器(ORM)

> 原文：<https://betterprogramming.pub/an-introduction-to-sequelize-a-node-js-object-relational-mapper-orm-267a51c2d978>

## 从 web 应用程序内部管理数据的更好方法

![](img/99500994c57f0d8525b0b25507ce81be.png)

# 介绍

Sequelize 是 Node.js 的一个流行的对象关系映射器(ORM)。ORM 是一个库，负责管理应用程序中的数据，特别是(a)用面向对象的编程语言编写的应用程序，以及(b)在数据库中持久存储数据的应用程序。

本文首先介绍 ORMs，然后概述 Sequelize 的基本功能，包括:

*   安装 Sequelize
*   连接到数据库；
*   定义模型；
*   创建模型实例；
*   查询模型；
*   创建模型之间的关联；和
*   涉及关联的查询。

# 先决条件

本文假设您对以下内容有所了解:

*   面向对象编程(OOP)；
*   JavaScript 和
*   结构化查询语言(SQL)。

# 什么是 ORM，它有什么好处？

尽管[是针对特定 ORM](https://docs.sqlalchemy.org/en/13/orm/tutorial.html) 编写的，但以下定义可适用于整体 ORM:

> SQLAlchemy 对象关系映射器提供了一种将用户定义的 Python 类与数据库表相关联，并将这些类(对象)的实例与其对应表中的行相关联的方法。它包括一个透明地同步对象及其相关行之间的所有状态变化的系统(称为工作单元),以及一个根据用户定义的类及其相互之间的定义关系来表达数据库查询的系统。

ORM 创建了两种完全不同的数据表示之间的映射:OOP 语言的类和类实例(对象),以及关系数据库的表和行。

ORM 使这两个组保持同步，因此每当应用程序中的类或实例发生变化时，相应的数据库表或行也会发生变化。

数据库语句和查询可以用您选择的编程语言编写，ORM 将这些指令转换成数据库可以理解的 SQL 代码。

编写复杂的 SQL 查询可能是一个漫长而繁琐的过程，因此使用 ORM 可以节省开发时间。这里的、这里的和这里的讨论了使用 ORM 与简单编写原始 SQL 的功效。

# Node.js ORMs

许多库实现了 ORM 技术。Node.js 世界里有 [Prisma](https://www.prisma.io/) 、[书架](https://bookshelfjs.org/)、[水线](https://sailsjs.com/documentation/reference/waterline-orm)、[异议](https://vincit.github.io/objection.js/)等等。Sequelize 是 SQL 数据库最流行的 Node.js ORM(在撰写本文时 GitHub 上有 26k 颗星)。

# 序列

Sequelize 的 [GitHub 页面](https://github.com/sequelize/sequelize)包括以下对该库的描述:

> Sequelize 是一个易于使用且基于承诺的 Node.js ORM 工具，适用于 Postgres、MySQL、MariaDB、SQLite、DB2、Microsoft SQL Server、Snowflake 和 IBM i。它具有可靠的事务支持、关系、急切加载和延迟加载、读取复制等特性。

# 装置

本文中的示例使用了 SQLite3 数据库。

可以使用节点包管理器(npm)将 Sequelize 添加到 Node.js 项目中。还需要安装特定数据库的驱动程序。在终端中键入以下命令将创建一个安装了 Sequelize 的新 Node.js 项目:

# 关系

我们需要创建一个`Sequelize` 类的实例来连接我们的数据库。`Sequelize`构造函数根据我们使用的数据库类型采用不同的参数。当使用 SQLite3 数据库时，我们传递两个参数—数据库方言(' SQLite ')和 SQLite3 存储文件的路径:

使用全功能的关系数据库管理系统(RDBMS ),如 MySQL 和 PostgreSQL，要求我们传递比 SQLite3 示例中更多的参数:

# 模型

模型是某些软件设计模式的组成部分。模型负责表示和管理应用程序中的数据。在 Sequelize 中，每个模型都是一个 JavaScript 类，代表数据库中的一个表。每当定义一个模型时，像 Sequelize 这样的 ORM 都会自动创建一个数据库表。

## 定义模型

为了在 Sequelize 中定义一个模型，我们首先创建一个扩展 Sequelize 的`Model` 类的类，然后我们在那个类上调用`init`方法，传入我们的模型属性和其他选项:

或者，可以通过调用`sequelize.define(modelName, attributes, options)`来定义模型。

## 模型属性

将属性添加到模型中会在数据库表中创建相应的列。在上面的例子中，`name`、`age`和`favoriteColor`被定义为用户模型属性，因此也是我们的 users 表中的列名。

## 模型数据类型

模型的每个属性都必须有一个数据类型。将`DataTypes` 导入到一个 JavaScript 文件中，可以访问 Sequelize 的内置数据类型，包括`String`、`Text`、`Boolean`、`Integer`、`Decimal`、`Float`、`Date.`

## 默认值

在属性上设置`defaultValue`选项可确保在创建模型实例时，如果没有输入其他值，该属性会自动被赋予默认值。

## 自动表格命名

当我们在上面的例子中定义一个`User`模型时，一个名为‘Users’的表被自动添加到数据库中。

尽管我们的代码从来没有明确地给我们的表命名，但是在幕后，Sequelize 创建了一个表名，它是模型名的复数。

## 自动创建“id”(主键)

使用 Sequelize，我们在定义模型时不需要指定一个`id`(主键)属性——sequel ize 自动在我们的表中包含一个`id` 列。

## 自动创建“创建日期”和“更新日期”

当我们定义一个模型时，Sequelize 自动包含并管理我们的表中的两个类型为`DATE`的列— `createdAt` 和`updatedAt`。

## 确认

Sequelize 提供了预定义的验证器，可以在定义模型属性时进行设置。我们也可以编写定制的验证器。验证器在执行 SQL 操作之前检查输入的数据。如果数据没有通过验证测试，就不会执行 SQL 操作。

例如，如果在定义一个模型时，在一个列上设置了`allowNull: false`验证器，那么在创建一个实例时，该列不能为空。

另一个常见的验证是`unique: true`。例如，如果在 username 列上设置了该选项，尝试插入现有用户名将会引发错误。

# 模型实例

一旦我们定义了一个模型，我们就可以基于这个模型创建实例。请记住，模型是一个 JavaScript 类，模型实例是该类的实例(对象)。这些实例映射到数据库表中的行(记录)。

当回顾下面 Sequelize 的实例方法时，您会注意到它们中的许多是异步的。这些方法返回一个`Promise`对象，而像`then` 和`catch`这样的承诺方法可以被链接到异步方法的末尾来处理它的返回值。

## 创建模型实例(并将它们插入数据库)

我们可以使用`build`方法创建一个已定义模型的实例。

尽管`build`方法创建了一个模型实例，但它并没有在数据库表中创建记录。在`build` 方法之后必须调用`save`方法来将实例保存到数据库中。

然而，确实存在一个捷径——`create`方法在一个方法中完成了`build` 和`save`的工作:

## 更新实例和数据库记录

Sequelize 实例的更新方式与 JavaScript 中对象的更新方式相同。

name 属性更改为' Ada '后必须调用`save`方法来保存对数据库的更改。

像`create`一样，`update` 方法是同时更新记录并将更改保存到数据库的快捷方式:

## 删除实例和数据库记录

`destroy`方法删除一个实例并将其从数据库中移除:

# 查询模型

Sequelize 提供了许多 finder 方法，每个方法都会生成一个 SQL `SELECT`查询。例如，使用`findAll`方法在 SQL 中生成一个`SELECT * FROM ...`类型的查询:

`findByPk`是另一种查找方法。它根据作为参数传入的`id`(主键)返回单个表记录:

每个 finder 方法返回一个`Promise`,包含调用该方法的模型的实例。换句话说，您不只是接收查询的数据库结果——sequel ize 将数据库查询的每个结果包装在一个实例对象中。每个返回的实例都有几个方便的方法可用。

像`findByPk`这样的查找器方法返回单个模型实例，而`findAll`返回模型实例的数组。

## 使用“where”过滤查询

Sequelize 的查询方法可以使用`where`选项进行过滤。

注意，在我们的示例代码中没有必要包含一个等式操作符——如果没有提供操作符，Sequelize 会假设等式成立。

但是当我们需要用`where`选项包含一个操作符时，我们可以简单地使用 JavaScript 内置操作符吗？不。我们需要使用 Sequelize 定义的运算符。Sequelize 的`Op`(必须导入到您的文件中)提供了许多操作符，相当于 SQL 的`WHERE`相关操作符。以下是几个例子:

下面是一个使用了`where`选项以及 Sequelize 的`Op.and`操作符的查询示例:

# 联合

关系数据库很有价值，因为它们可以在表之间创建关联(关系)。可以创建的关联类型包括一对多、一对一和多对多。

Sequelize 提供的函数在组合使用时，可以复制两个模型之间的标准数据库关联。

四个关联功能如下:

*   `hasOne`协会
*   `belongsTo` 协会
*   `hasMany` 协会
*   `belongsToMany` 协会

## 一对一

`hasOne` 和`belongsTo` 函数可以结合使用，在两个表之间创建一对一的关系。如果没有传递外键选项，Sequelize 会自动为调用`belongsTo`的模型分配一个外键。在这个例子中，概要文件模型获得了一个外键`UserId:`

## 一对多

`hasMany` 和`belongsTo`函数可以结合使用，在两个表之间创建一对多的关系。同样，调用`belongsTo`的模型获得外键——在本例中，Comment 模型获得外键`UserId`。

## 多对多

对于多对多关系，Sequelize 使用了`Junction Model`的概念，也称为连接表。在这个场景中，Sequelize 创建了第三个新模型(因此也是数据库中的第三个表),它包含两个外键列，每个外键列对应一个连接的表。

电影和演员模型提供了一个多对多关系的很好的例子，因为一个演员可以有许多电影，而一部电影可以有许多演员。在这个例子中，我们使用`belongsToMany` 函数来创建一个连接模型，它跟踪两个模型之间的关联。我们为我们的连接模型传递了一个名称(‘actor movies’)。该模型将包含外键“movieId”和“actorId”。

# 涉及关联的查询

如果我们正在处理一个模型，并且想要从与主模型相关联的第二个模型中获取数据，该怎么办呢？或者，如果我们想一次从两个关联的模型中获取数据呢？在 Sequelize 中，我们可以通过使用两种技术中的一种来实现这些目标:惰性加载和急切加载。

为了演示这两种技术，我们将创建一个涉及两个模型之间一对多关系的例子:`User`和`Comment`(一个用户有许多评论):

## 惰性装载

延迟加载包括首先获取用户数据，然后在我们需要的时候获取评论数据。

以下是 console.log()的输出:

`comments`变量包含一个对象数组，每个对象代表一个属于 id 为 1 的用户的评论。

`getComments()`方法是由 Sequelize 创建的，允许您获取与您正在处理的模型相关的数据。该方法的名称是从关联模型的名称中生成的。因为我们的关联模型被命名为“Comment”，所以我们的方法被自动命名为“getComments()”。

## 急切装载

急切加载包括从多个相互关联的表中获取数据。

如果我们想用 SQL 来做这件事，我们必须用一个或多个`JOIN`子句来构造一个查询。

在 Sequelize 中，我们可以使用一个 finder 方法，如`findAll`或`findByPk`，并将`include`选项整合到该方法中，以从两个模型中获取数据。

当我们这样做时，Sequelize 返回的对象的字段包含来自两个关联模型的数据。

对于我们的示例，我们将获取所有评论以及每个评论的相关用户:

下面的 console.log()输出显示 Sequelize 返回了一个对象，该对象包含注释字段`body`、`id` 和`userId`以及一个名为`user`的附加字段，该字段包含用户实例及其所有属性:

# 资源

*   [对文件进行排序](https://sequelize.org/docs/v6/)
*   [顺序化 API](https://sequelize.org/api/v6/identifiers.html)
*   [对象关系映射](https://en.wikipedia.org/wiki/Object%E2%80%93relational_mapping)

```
Check out my GitHub page![https://github.com/todd-demone](https://github.com/todd-demone)
```