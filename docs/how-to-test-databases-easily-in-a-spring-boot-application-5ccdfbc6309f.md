# 如何在 Spring Boot 应用程序中轻松测试数据库

> 原文：<https://betterprogramming.pub/how-to-test-databases-easily-in-a-spring-boot-application-5ccdfbc6309f>

## 嵌入式数据库和 Testcontainers 库的测试技术

![](img/74f979caec0794bce9e1ac6b1aadfcd5.png)

Christopher Gower 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

数据库是几乎所有应用程序的关键后端点。编写好的测试有助于我们在投入生产之前检查数据库功能是否如预期的那样工作。幸运的是，有一些方便的工具可以帮助我们完成这项任务。

在本教程中，我将向您展示在 Spring Boot 应用程序中测试数据库的两种方法:

*   使用 [H2](http://www.h2database.com/html/quickstart.html) 内存/嵌入式数据库。
*   使用[测试容器](https://www.testcontainers.org/)库。

你会明白每种方法的利弊。这将帮助您在选择数据库测试工具时做出正确的决定。

我们开始吧！

# H2 嵌入式数据库

H2 是一个基于 Java 的轻量级开源数据库。它为开发人员提供了一种在内存中配置和运行数据库的快速方法。这样，数据将不会被持久存储在真实的数据库中。它支持多种 SQL 兼容模式，如 IBM DB2、Apache Derby、HSQLDB、MS SQL Server、MySQL、Oracle 和 PostgreSQL。

为了演示 H2 的用法，我们将创建一个简单的 Spring Boot 应用程序，用一个 JPA 存储库来检索用户。

在这个例子中，我使用 Maven 作为构建工具。在您喜欢的 IDE 中创建一个新的 Maven 项目，并将以下依赖项放入自动生成的`pom.xml` 文件中:

创建一个名为`User`的新 Java 类:

用户类别

注意，我使用`Lombok`库来删除任何样板代码，比如 getters、setters 等。

下一步是创建 JPA 存储库。创建一个名为`UserRepository.java`的新界面:

用户存储库

*   第一个查询返回名称超过 X 个字符的用户。
*   第二个查询通过电子邮件从数据库中检索用户。
*   第三个按角色收集多个用户。

*注意，在最后两种方法中，Spring 会自动生成查询。*

现在，让我们进入测试部分。

我们将使用两个辅助 SQL 脚本将数据插入数据库，并在完成测试后清理数据。

相应地在`test/resources`文件夹下创建两个新文件:

`create-data.sql`文件的内容:

```
INSERT INTO USER(id, NAME, ROLE, EMAIL) VALUES ('1', 'Kirshi', 'developer', 'kirshi@example.org');
```

`cleanup-data.sql`文件的内容:

```
DELETE FROM USER
```

创建一个新的测试类，例如`UserRepositoryH2Test.java`:

使用 H2 测试用户存储库

`DataJpaTest`用嵌入式数据库建立一个环境。我们调用之前创建的 SQL 脚本。

请注意，每一个测试都将依赖于这些脚本。如果你想在每个测试中检查不同的查询，你可以在测试方法中保存实体的 `*Assertions*` *部分。这种方法不容易出错，因为更改存储库代码会导致测试编译失败。这样，您可以立即发现并修复错误。然而，如果您针对复杂的查询进行测试，JUnit 测试可能会变得难以理解。*

运行测试以检查结果。一切都应该是成功的。

这又快又简单！

# Testcontainers 库

Testcontainers 库为我们提供了管理一次性容器的生命周期的能力。它用于编写集成和端到端测试。由 Docker 提供支持，它帮助我们方便地启动基础设施组件，如数据库和消息代理。我们可以使用任何 Docker 映像或者提供一个`docker-compose.yml`文件来启动一个测试容器。

虽然 H2 数据库测试非常方便，但有时它并不是最佳选择。例如，在某些情况下，H2 不支持某些数据库特性。此外，对不同于生产数据库的数据库进行测试是不可靠的。

Testcontainers 提供了完全的数据库兼容性，因为一个真实的数据库在容器中运行。当您需要执行需要数据库存在的集成测试时，它会很有帮助。

现在，让我们为我们的`UserRepository`创建另一个测试类，看看 Testcontainers 是如何工作的:

使用 Tescontainers 的 UserRepository 测试

代码与上一个非常相似。

*   `@AutoConfigureTestDatabase(replace=AutoConfigureTestDatabase.Replace.NONE)`注释禁用内存数据库的默认启动。
*   `Testcontainers`注释用于 Jupiter 与 Testcontainers 的集成。
*   方法初始化并启动一个 MySQL Docker 镜像。

让我们将以下 SQL 语句添加到我们的`create-data.sql`文件中，以创建并填充数据库:

创建和插入命令

现在让我们开始 JUnit 测试。您应该看到 Docker 映像正在被提取，MySQL 容器正在启动:

```
......
Creating container for image: mysql:latest
Starting container with ID: f029587577b4d955f442515264e6a35755eac6b49c5dc3064d46516d1be42f26
Container mysql:latest is starting: f029587577b4d955f442515264e6a35755eac6b49c5dc3064d46516d1be42f26
Waiting for database connection to become available at jdbc:mysql://localhost:49263/test using query 'SELECT 1'
Container is started (JDBC URL: jdbc:mysql://localhost:49263/test)
....
```

测试应该通过了，您应该会在 IDE 控制台中看到 Hibernate 查询。

## 集成测试容器和 CI/CD 管道

要将 Testcontainers 与 CI/CD 管道集成，您必须使用 [Docker-In-Docker 服务](https://docs.gitlab.com/ee/ci/docker/using_docker_build.html#use-docker-in-docker-workflow-with-docker-executor) ( **dind** )将作业作为 Docker 容器运行。

下面是 GitLab `.gitlab-ci.yml` 配置的一个例子:

带有测试容器的 GitLab CICD 管道

查看[文档](https://www.testcontainers.org/supported_docker_environment/continuous_integration/gitlab_ci/)找到其他 CI/CD 平台的配置设置，如 Bitbucket、CircleCI 等。

# 结论

在本教程中，我们回顾了为 Spring Boot 应用程序编写数据库测试以测试数据库功能的两种方法。

当您测试不依赖于任何数据库兼容性的简单查询时，可以使用 H2。这是快速和容易上手的。测试执行时间很快。

Testcontainers 库没有 H2 那么快，但是测试更可靠。它提供了 H2 可能不支持的真正的数据库特性。

始终根据您的用例场景选择正确的工具。

我希望你能从这篇文章中学到一些新的东西。欢迎在评论中分享你是如何测试数据库功能的。

感谢您的阅读，下次再见！

# 资源

*   [https://github.com/kirshiyin89/dbtestingdemo](https://github.com/kirshiyin89/dbtestingdemo)
*   [https://www.testcontainers.org/modules/databases/](https://www.testcontainers.org/modules/databases/)
*   [https://www . test containers . org/supported _ docker _ environment/continuous _ integration/git lab _ ci/](https://www.testcontainers.org/supported_docker_environment/continuous_integration/gitlab_ci/)