# 适用于初学者的 12 个基本 SQL 命令

> 原文：<https://betterprogramming.pub/12-basic-sql-commands-for-beginners-1fcb34697ab6>

## 对每个开发人员都有用的 SQL 命令

![](img/9832ee5194dab9bd88e3e72e1d135db7.png)

照片由 [Pexels](https://www.pexels.com/photo/person-in-beige-long-sleeve-shirt-using-macbook-pro-4065876/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 [cottonbro](https://www.pexels.com/@cottonbro?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

SQL(结构化查询语言)是软件开发人员和程序员最常用的工具之一。大多数网站使用数据库来存储他们的产品或客户的数据。

SQL 是一种特定于领域的语言，用于与关系数据库管理系统进行交互。它用于管理和组织存储在数据库中的数据。

在本文中，我将讨论每个软件开发人员和程序员都应该知道的一些最常用的 SQL 命令。

我们开始吧！

# 1.挑选

这个`select`命令是 SQL 最简单也是最基本的命令之一。顾名思义，该命令用于从数据库中选择/检索数据。

```
SELECT * FROM table;
```

这个命令可以分为两部分。第一部分(`Select *`)指定需要从表中检索哪些列，第二部分(`FROM table`)指定需要从中检索数据的表的名称。

# 2.插入

我们讨论了从表中检索记录，但是插入一条新记录怎么样呢？`insert`命令用于向表中插入一条新记录。

```
INSERT into table_name(column1, column2,...) VALUES (value1, value2,...);
```

我们可以指定要插入新记录的表的名称、列名以及需要插入的值。

`VALUES`子句用于指定这些是需要插入的值。

# 3.别名

在 SQL 中，别名用于临时重命名表。使用`as` 关键字**创建别名。**我们使用`as`命令为一个实体提供一个临时名称。我们可以给一个表或表中的任何列取一个临时名称。

表别名语法:

```
SELECT name, age FROM person as alias_name ;
```

列别名语法:

```
SELECT name as alias_name FROM person;
```

当我们联接两个表并且它们都包含同名的列时，别名对于避免不明确的错误也很有用。

# 4.在哪里

我们已经看到了使用`select`命令从数据库中检索数据，但是如果我们想根据某些条件过滤记录呢？

`where`命令允许您对`select`查询应用一些条件来过滤数据。我们可以使用`where`子句来限制表中记录的数量。`where`子句用在语句的末尾。

```
SELECT age, name FROM person p WHERE p.age > 10 ;
```

我们可以在一个`where` 子句中应用多个条件，方法是用`AND`操作符将它们分开。

```
SELECT age, name FROM person p WHERE p.age>10 AND p.age<20;
```

# 5.以...排序

`Order By` 命令用于根据指定的列对记录进行排序。您可以按升序或降序对记录进行排序。

```
SELECT age, name FROM person ORDER BY age DESC;
```

我们需要指定列的名称和顺序:`ASC`表示升序，`DESC` 表示降序。

# 6.加入

`join`命令用于*根据两个表中的公共列合并*数据库中的两个或多个表。

我们需要根据想要连接两个表的列来指定列名。

```
SELECT s.name, s.rollNo, s.courseId  FROM Student s JOIN Department d WHERE s.courseId = d.courseId ;
```

# 7.在…里

在 SQL 中，`In`操作符用于根据值列表中的值检索记录。

```
SELECT name, salary FROM employee WHERE salary IN(20000, 25000, 30000);
```

上面的脚本将从`employee`表中检索所有记录，其中员工的工资将是`20000`、`25000`或`30000`。

我们还可以使用`NOT IN`子句来排除与列表中的值相匹配的记录。

# 8.改变

`alter`命令用于改变表格的结构。使用这个`alter`命令，我们可以`ADD`向我们的表中添加一个新列，`DROP`从表中添加一个列，以及`MODIFY`表中现有的列。

添加列:

```
ALTER TABLE person ADD salary integer;
```

这个脚本将在 person 表中添加一个整数类型的列 **salary** 。

删除列:

```
ALTER TABLE person DROP COLUMN age;
```

这个脚本将从 person 表中删除名为 **age** 的列。

修改列:

```
ALTER TABLE person MODIFY salary float;
```

上面的脚本将把`person`表中的`salary`列的类型从整型修改为浮点型。

# 9.更新

在表中插入值后，一些行中可能会插入错误的值，需要进行更正。

在这种情况下，`Update`命令用于更新指定行中的值。我们可以使用`where` 子句来指定哪些记录需要更新。我们可以一次更新多个列的值。

```
UPDATE people SET age=25 WHERE name='John' ; 
```

上面的查询会将名为“John”的记录的`age` 列的值更新为`25`。

# 10.删除

`Delete`命令允许你从表格中删除记录。您可以使用`where` 子句和`delete` 子句从表中删除特定的记录。

它的语法非常简单易用:

```
DELETE FROM person WHERE name='John' ;
```

必须小心使用`delete`命令，因为一旦记录被删除，就无法检索数据。

# 11.创建表格

顾名思义，`create`命令用于在 SQL 数据库中创建一个新表。它的语法是:

```
CREATE TABLE person(age integer, name varchar(50), salary integer);
```

您需要指定表的名称(即我们示例中的`person`)和您希望在表中出现的列名，以及它们各自的数据类型。

# 12.翻桌

`drop`命令用于从数据库中删除表。如果我们比较一下`drop`命令和`delete`命令，`delete`命令用于从表中删除特定的行。同时，`drop`命令用于从数据库中删除整个表。

它的语法是:

```
DROP TABLE table_name ;
```

执行该命令后，您将看到整个表都从数据库中删除了。

# 结论

在本文中，我们讨论了每个程序员和软件开发人员都应该知道的一些基本命令。在使用数据库时，必须熟悉它们。

我希望你喜欢这篇文章。感谢阅读！