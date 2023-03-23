# 如何使用 Hibernate 在 JPA 查询中使用原生 SQL 函数

> 原文：<https://betterprogramming.pub/how-to-use-native-sql-functions-in-jpa-queries-with-hibernate-d1f17d07a588>

## 通过注册本机函数来避免使用本机 SQL 查询

![](img/de9c27b96b0f73b1c9b023f86aa3c3de.png)

由[道格拉斯·洛佩斯](https://unsplash.com/@douglasamarelo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

[JPA](https://en.wikipedia.org/wiki/Jakarta_Persistence) 原生支持 native SQL。当您想要利用正在使用的数据库的独特功能时，这很有用。另一方面，原生 SQL 查询不是数据库不可知的，这可能会成为一个问题。

比如从 [MySQL](https://www.mysql.com/it/) 切换到 [PostgreSQL](https://www.postgresql.org/) 或者相反的时候。如果您使用原生查询，转换将不会很简单，因为您必须主动翻译它们。这就是为什么只有在严格需要时才应该使用原生查询。

选择原生 SQL 查询的一个主要原因是原生函数，比如 MySQL 的`[GROUP_CONCAT](https://mariadb.com/kb/it/group_concat/)`。事实上，您可能认为使用原生查询是利用这些功能的唯一方法。其实这不是真的！事实上， [JPA 2.1](https://en.wikipedia.org/wiki/Jakarta_Persistence#JPA_2.1) 引入了允许您在 [JPQL](https://en.wikipedia.org/wiki/Jakarta_Persistence_Query_Language) 或[标准查询](https://docs.jboss.org/hibernate/core/4.0/hem/en-US/html/querycriteria.html)中调用任何本地函数的特性。

让我们看看如何在 Kotlin 和 Java 中使用 Hibernate 在 JPA 查询中使用原生 SQL 函数。

# 注册本机函数

您想要使用的原生 SQL 函数必须在 Hibernate `[Dialect](https://docs.jboss.org/hibernate/orm/5.4/javadocs/org/hibernate/dialect/Dialect.html)`实现类中注册，这取决于您的数据库。你可以在这里找到 Hibernate [支持的所有方言的列表](https://docs.jboss.org/hibernate/orm/5.4/javadocs/org/hibernate/dialect/package-summary.html)。在本例中，您将看到`[MySQL8Dialect](https://docs.jboss.org/hibernate/orm/5.4/javadocs/org/hibernate/dialect/MySQL8Dialect.html)`和`[PostgreSQL9Dialect](https://docs.jboss.org/hibernate/orm/5.4/javadocs/org/hibernate/dialect/PostgreSQL9Dialect.html)`。

首先，您需要创建一个扩展您特定的`Dialect`实现类的类。在下面的例子中，这个类被放在`com.yourapp.configs`包中。然后，使用它注册本机函数，如下所示:

**Java**

科特林

在这种情况下，通过向`[registerFunction](https://docs.jboss.org/hibernate/orm/5.4/javadocs/org/hibernate/dialect/Dialect.html#registerFunction-java.lang.String-org.hibernate.dialect.function.SQLFunction-)`方法传递一个`[StandardSQLFunction](https://docs.jboss.org/hibernate/orm/5.4/javadocs/org/hibernate/dialect/function/StandardSQLFunction.html)`类，MySQL `GROUP_CONCAT`函数被添加到了`MySQL8Dialect`中。传递给该方法的第一个字符串表示用于在查询中调用本机函数的名称。请注意，许多其他本机 SQL 函数也可以类似地注册。

现在，您需要使这个类成为默认的 Hibernate 方言。您可以通过将以下属性添加到您的`[application.properties](https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html)`文件中来实现这一点:

```
spring.jpa.properties.hibernate.dialect = com.myapp.util.CustomSQLDialect
```

瞧啊！您可以开始使用刚刚在 JPA 查询中注册的原生 SQL 函数。

# 运行中的本机函数

让我们看一个刚刚注册的`STRING_CONCAT`函数的例子:

```
SELECT F, STRING_CONCAT(F.name) AS names
FROM Foo F 
GROUP BY F.birthDate
```

让我们假设 [JPA 实体](https://medium.com/swlh/defining-jpa-hibernate-entities-in-kotlin-1ff8ee470805) Foo 映射一个`foo`表，该表具有列`id`、`name`、`birthDate`。

[](https://medium.com/swlh/defining-jpa-hibernate-entities-in-kotlin-1ff8ee470805) [## 在 Kotlin 中定义 JPA/Hibernate 实体

### 让我们看看如何在 Kotlin 中正确定义 JPA/Hibernate 实体

medium.com](https://medium.com/swlh/defining-jpa-hibernate-entities-in-kotlin-1ff8ee470805) 

然后，这个 JPQL 查询将被翻译成下面的 MySQL 查询:

```
SELECT F.id, F.name, F.surname, GROUP_CONCAT(F.name) AS names
FROM foo F 
GROUP BY F.birthDate
```

如您所见，MySQL `GROUP_CONCAT`函数现在由`STRING_CONCAT` JPA 函数映射。这意味着 MySQL 原生函数现在可以在不使用不可移植的原生 SQL 查询的情况下使用。

# 迁移到不同的数据库

假设您想从 MySQL 迁移到 PostgreSQL。如果你注册的每一个原生 MySQL 函数在 PostgreSQL 中都有对应的函数，这就很容易了。另外，不需要修改任何 JPA 查询。

您唯一需要做的事情就是如下更改您的`CustomSQLDialect`类:

**Java**

科特林

由于 MYSQL `GROUP_CONCAT`函数对应于 PostgreSQL `STRING_AGG`函数，所以不需要做其他的修改。您编写的每个 JPA 函数仍将按预期工作。请注意，在迁移到其他数据库时，可以应用相同的过程。

# 奖金

您可能也会对以下文章感兴趣:

[](https://medium.com/swlh/designing-a-multi-layered-architecture-for-building-restful-web-services-with-spring-boot-and-a12ef85b77c9) [## 用 Spring Boot 和…设计一个多层架构来构建 RESTful Web 服务

### 设计超过五层的高级架构

medium.com](https://medium.com/swlh/designing-a-multi-layered-architecture-for-building-restful-web-services-with-spring-boot-and-a12ef85b77c9) [](https://codeburst.io/criteria-queries-and-jpa-metamodel-with-spring-boot-and-kotlin-9c82be54d626) [## Spring Boot 和科特林的标准查询和 JPA 元模型

### Kotlin 中更干净、更简单、更强大的标准查询

codeburst.io](https://codeburst.io/criteria-queries-and-jpa-metamodel-with-spring-boot-and-kotlin-9c82be54d626) [](https://codeburst.io/avoiding-code-duplication-by-adding-an-api-layer-in-spring-boot-48ee3a949cb4) [## 通过在 Spring Boot 中添加 API 层来避免代码重复

### 使用后端 HTTP 客户端构建高级 API 层

codeburst.io](https://codeburst.io/avoiding-code-duplication-by-adding-an-api-layer-in-spring-boot-48ee3a949cb4) 

# 结论

今天，我们研究了如何让 Hibernate 在 JPQL 或条件查询中支持原生 SQL 函数。这是避免采用原生查询的一个有用的变通方法，根据定义，原生查询是不可移植的。正如您刚才看到的，向 Hibernate `Dialect`类注册函数只需要几行代码。另一方面，它减少了使用原生查询的需要，使得迁移到不同的数据库更加容易。这是一个重要的目标，本文就是要展示如何实现它。

感谢阅读！我希望这篇文章对你有所帮助。