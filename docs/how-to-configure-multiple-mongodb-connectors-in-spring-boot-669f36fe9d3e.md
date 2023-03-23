# 如何在 Spring Boot 配置多个 MongoDB 连接器

> 原文：<https://betterprogramming.pub/how-to-configure-multiple-mongodb-connectors-in-spring-boot-669f36fe9d3e>

## 用 MongoDB 4.x 驱动程序处理不同的 MongoTemplates

![](img/d2f84f41d16eba7094e8e6da20907f0f.png)

[Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上 [AltumCode](https://unsplash.com/@altumcode?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 的照片。

有大量的[在线指南](https://dzone.com/articles/multiple-mongodb-connectors-with-spring-boot)解释了如何配置连接到多个[MongoDB](https://www.mongodb.com/)的 Spring Boot 应用程序。常见的方法是使用`[MongoDbFactory](https://docs.spring.io/spring-data/data-mongodb/docs/current/api/org/springframework/data/mongodb/MongoDbFactory.html)`，但是这个类在[Spring Data MongoDB](https://spring.io/projects/spring-data-mongodb)[3 . x](https://docs.spring.io/spring-data/data-mongodb/docs/current/api/deprecated-list.html)中被弃用，它引入了 [MongoDB 4.x 驱动](https://docs.mongodb.com/drivers/java)。

所以，让我们看看如何使用 Spring Data MongoDB 3.x 在 Spring Boot 配置多个 MongoDB 数据源

请注意，代码将用 Kotlin 编写，但用 Java 也可以达到同样的结果。同样，在本教程中，我将只使用两种不同的 MongoDB 连接，但是这种方法适用于任何数量的数据库。

# 添加所需的依赖项

首先，您需要将`[spring-boot-starter-data-mongodb](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-mongodb)`添加到项目的依赖项中。至少使用 2.3.0 版本，因为 [MongoDB 驱动](https://mvnrepository.com/artifact/org.mongodb/mongodb-driver-sync) 4.x 是在 [Spring Boot 启动数据 MongoDB 2.3.0.RELEASE](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-mongodb/2.3.0.RELEASE) 中引入的。

如果您是 Gradle 用户，请将此依赖项添加到项目的构建文件中:

```
compile "org.springframework.boot:spring-boot-starter-data-mongodb:2.3.0.RELEASE"
```

否则，如果您是 Maven 用户，请将以下依赖项添加到项目的构建 POM 中:

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
    <version>2.3.0.RELEASE</version>
</dependency>
```

现在，您已经拥有了在 Spring Boot 使用 MongoDB 所需的一切。

# 定义多个 MongoTemplate 实例

如果你已经有多个`MongoTemplates`但是你想升级到 Spring Data MongoDB 3.x，我推荐你先阅读[文档](https://docs.spring.io/spring-data/mongodb/docs/current/reference/html/#upgrading.2-3)。否则，您可以按照下面的几个步骤从头开始。

首先，您需要在您的`application.properties` 文件中定义您所有的 MongoDB 连接细节。

MongoDB 4.x 驱动程序不再支持`host`和`port`配置，因此您需要使用一个 URI 来提供以下格式的所有所需配置:

```
mongodb://<username>:<password>@<host>:<port>/<db_name>
```

请注意，`<username>:<password>@`是可选的，您应该只将它用于认证连接。

所以，你的`application.properties`应该包含这样的内容:

```
#--- Primary MongoDB ---#
spring.data.mongodb.uri=mongodb://admin:password@127.0.0.1:27017/primary#--- Secondary MongoDB ---#
mongodb.uri=mongodb://admin:password@127.0.0.1:27017/secondary
```

现在，是时候添加一个新的 Spring Boot 配置文件了，您将在其中为每个连接定义一个`[MongoTemplate](https://docs.spring.io/spring-data/mongodb/docs/current/api/org/springframework/data/mongodb/core/MongoTemplate.html)` [Spring Boot bean](https://www.baeldung.com/spring-bean) :

`[@ConfigurationProperties](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/context/properties/ConfigurationProperties.html)`注释使用在配置文件中定义了相同前缀的分层属性。

Spring Boot 将自动绑定在`application.properties`文件中定义的任何属性，该属性具有在注释中声明的前缀，并且与目标类中的一个字段同名。这意味着`[MongoProperties](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/mongo/MongoProperties.html)`对象的`uri`字段将具有从配置文件中读取的值。

`MongoProperties`实例用于通过`[SimpleMongoClientDatabaseFactory](https://docs.spring.io/spring-data/mongodb/docs/current/api/org/springframework/data/mongodb/core/SimpleMongoClientDatabaseFactory.html)`建立到数据库的连接，然后创建所需的`MongoTemplate`实例。

`MongoTemplate` 类是`[MongoOperations](https://docs.spring.io/spring-data/mongodb/docs/current/api/org/springframework/data/mongodb/core/MongoOperations.html)`接口的主要实现，提供了一组基本的 MongoDB 操作。您可以使用一个`MongoTemplate`对象来进行聚合、排序，并根据它所引用的数据库中的多个标准来查找所需的文档。

最后，您必须禁用 Mongo 的 Spring Boot 自动配置。您可以通过在 Spring Boot 应用程序的主类上添加以下代码行来实现这一点:

```
@SpringBootApplication(exclude = [
   MongoAutoConfiguration::class,
   MongoDataAutoConfiguration::class
])
```

# 使用 MongoTemplate 实例

## 直接使用 MongoTemplate

`MongoTemplate`为底层持久引擎提供一个基本的 API，可以用来执行查询。以这种方式使用它所需要的只是一个工作实例。

让我们假设您想要检索保存在一个名为`authors`的集合中的所有作者，该集合按照特定的姓氏放置在辅助 MongoDB 数据库中。

现在，您需要一个用代表存储在集合中的文档的`[@Document](https://docs.spring.io/spring-data/data-mongodb/docs/current/api/org/springframework/data/mongodb/core/mapping/Document.html)`注释的`Author`类:

然后，在一个`[DAO](https://it.wikipedia.org/wiki/Data_Access_Object)`层类中，您可以如下定义检索逻辑:

注意，通过`Bean`名称选择所需的`MongoTemplate`实例需要`[@Qualifier](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/annotation/Qualifier.html)`注释。如果没有它，Spring Boot 会选择在`MultipleMongoConfig`中定义的`primaryMongoTemplate`对象，因为它标有`[@Primary](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Primary.html)`。

瞧啊。不需要其他代码行。

## 通过 MongoRepository 使用 MongoTemplate

[存储库](https://docs.spring.io/spring-data/data-commons/docs/1.6.1.RELEASE/reference/html/repositories.html)是 [Spring Data](https://spring.io/projects/spring-data) 的主要概念之一，也是通过避免样板代码来减少实现数据访问层的工作量的一种方式。对于进一步的阅读，我推荐[文档](https://docs.spring.io/spring-data/mongodb/docs/1.2.0.RELEASE/reference/html/repositories.html)。

`[MongoRepository](https://docs.spring.io/spring-data/mongodb/docs/current/api/org/springframework/data/mongodb/repository/MongoRepository.html)`的实现类在运行时利用一个`MongoTemplate`实例。

要指定必须使用哪个`MongoTemplate` bean，您需要额外的配置。特别是，您必须为每个`MongoTemplate`实例定义一个新的`@Configuration`文件。

这是支持仓库使用`primaryMongoTemplate`的配置文件:

这是支持存储库使用`secondaryMongoTemplate`的配置文件:

请注意，`basePackages`必须指向与特定`mongoTemplateRef`相关的所有存储库所在的包。

现在您已经拥有了在 Spring Boot 开始使用 MongoDB 存储库所需的一切。这篇文章并不旨在展示这一点，我建议阅读[的文档](https://docs.spring.io/spring-data/mongodb/docs/1.2.0.RELEASE/reference/html/mongo.repositories.html)。

# 结论

在今天的文章中，我们从头开始研究了如何在 Spring Boot 使用 MongoDB 4.x 驱动程序定义多个`MongoTemplates`。同时，我展示了如何直接或通过`MongoRepository`使用它们来执行检索和持久化数据所需的所有查询。

感谢阅读！我希望这篇文章对你有所帮助。如果有任何问题、意见或建议，请随时联系我。