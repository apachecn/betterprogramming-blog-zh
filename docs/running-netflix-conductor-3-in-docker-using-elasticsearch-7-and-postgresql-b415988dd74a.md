# 使用 Elasticsearch 7 和 PostgreSQL 在 Docker 中运行网飞导体 3

> 原文：<https://betterprogramming.pub/running-netflix-conductor-3-in-docker-using-elasticsearch-7-and-postgresql-b415988dd74a>

## 所有这些都包含在一个简单、可重复和可维护的过程中

![](img/e04e46203404c6f4928ef3078f2bfd33.png)

网飞指挥家 GitHub 资料库中的网飞指挥家徽标。

# 关于这篇文章

网飞导体的标准实现配置为使用 Elasticsearch 6。本文涵盖了使用 Elasticsearch 7 和 PostgreSQL 需要进行的更改，并提供了几个脚本来自动构建和运行 Conductor，包括在本地和在云架构中。

在撰写本文时，最新发布的网飞指挥版本是 3.5.3。本文中的信息基于版本 3.5.1，但是应该适用于(并且容易修改)最新版本。如果你遇到任何问题，请让我知道，我会尽力帮助你。另一个巨大的帮助来源是网飞指挥讨论委员会。

我在一台 Windows 10 机器上运行所有提供的代码，来自 Git Bash，它包含在 [Git for Windows](https://git-scm.com/downloads) 中。我们的云架构运行在 CentOS 上。

# 什么是网飞指挥家？

网飞指挥是一个工作流程编排引擎，由网飞创建，用于“编排基于微服务的流程”(来源:[网飞指挥文档](https://netflix.github.io/conductor/))。网飞指挥的完整功能列表位于[这里](https://netflix.github.io/conductor/)，但一些关键功能是:

*   工作流和任务定义以 JSON 格式实现。这允许版本控制和对定制工具的简单支持，以创建和管理工作流和任务。
*   任务和工作者，即分别构成工作流和承载它们的微服务的构建块，是完全语言不可知的，这允许以最适合任务的语言来完成实现。Java、Python、.NET(不要脸插:[这个](https://github.com/erikbrgr/supersimpleconductor)是我开发的)，还有其他的。
*   数据库架构是可插拔的，这意味着我们可以选择我们想要用于 Conductor 的数据库。Conductor 附带了许多现成的插件，支持 Dynomite(也是网飞开发的)、MySQL 和 PostgreSQL。
*   网飞指挥配备了一个(可选)用户界面，允许洞察和控制工作流程，如(重新)开始，暂停，停止。
*   整个项目是开源的，并且正在被积极地开发和支持。

简而言之，网飞指挥允许您自动化和管理由多个任务组成的流程(这些任务本身可能就是流程)，这些任务可能需要以可靠、一致和可扩展的方式按顺序执行。

在我目前的项目中，我们使用网飞 Conductor 和 PostgreSQL 来运行预定的 ETL 过程、创建报告、下载和上传文件，以及进行监控和报警。我们还研究了如何使用 Conductor 来自动提供用户帐户和其他基础设施。

# 为 Elasticsearch 7 配置网飞导体

如简介中所述，网飞导体的标准实现配置为使用 Elasticsearch 6。对 Elasticsearch 7 的支持是内置的，但需要进行一些更改才能启用。GitHub 上有[文档](https://github.com/Netflix/conductor/blob/b4673608bec51c3880ce69fc854a01c38fa0f13e/es7-persistence/README.md)可以进行这些更改，但它似乎并不完全完整。我需要我下面描述的改变来构建和运行 Elasticsearch 7。

在撰写本文时，Elasticsearch 7.17.1 是 Elasticsearch 7 的最新版本。

下面是所需更改的快速总结(所有文件引用都是相对于根文件夹的):

*   `build.gradle` —将`ext['elasticsearch.version'] = revElasticSearch6`
    改为`ext['elasticsearch.version'] = revElasticSearch7`
*   `settings.gradle` —移除线`include 'es6-persistence'`
*   `server/build.gradle` —将`implementation project(':conductor-es6-persistence')`
    改为`implementation project(':conductor-es7-persistence')`
*   `server/src/main/resources/application.properties` —将`conductor.elasticsearch.version=6`
    改为`conductor.elasticsearch.version=7`
*   `docker/docker-compose.yaml` —将`image: elasticsearch:6.8.15`
    改为`image: docker.elastic.co/elasticsearch/elasticsearch:7.17.1`
*   `test-harness/build.gradle` —将`testImplementation project(‘:conductor-es6-persistence’)`
    改为`testImplementation project(‘:conductor-es7-persistence’)`
*   `/testharness/src/test/java/com/netflix/conductor/test/integration/AbstractEndToEndTest.java` —将`conductor.elasticsearch.version=6`
    改为`conductor.elasticsearch.version=7`

将`DockerImageName.parse("docker.elastic.co/elasticsearch/elasticsearch-oss").withTag("6.8.12"));`
改为`DockerImageName.parse("docker.elastic.co/elasticsearch/elasticsearch").withTag("7.17.1"));`

应用这些更改的标准方式是创建一个 Conductor 存储库的分支，并从那里开始工作。

然而，我发现复制整个存储库来更改少量文件是对资源的浪费，所以我采用了不同的方法。

在构建时，我`clone`了指挥库，然后应用几个`patch`文件来进行我需要的更改。虽然这些步骤稍微增加了构建时间，但我觉得这是速度和保持我自己的库干净和可维护之间的一个很好的折衷。

这是如何工作的:

1.  在您的项目目录中克隆 Conductor repo。
2.  进行上述更改。
3.  在 Conductor 文件夹中，运行`git diff > v3.5.1-update-to-es7.patch`创建一个`patch`文件。`v3.5.1`是为了方便起见，表示这个补丁只对那个版本有效。
4.  将`patch`文件复制到根目录，这样您就可以安全地删除 Conductor repo，并在再次克隆该 repo 时应用这些更改。

在构建之前，您还需要删除任何`dependencies.lock`文件。这和运行`rm -f **/dependencies.lock`一样简单。

为了自动删除`lock`文件并应用补丁，我创建了`01.get-conductor.sh`脚本:

你可以看到我实际上应用了更多的补丁。这些不需要与 Elasticsearch 7 一起运行。

此时，您可以构建导体。我为此创建了`02.build-conductor-server.sh`:

在“整合”一节中，我将提供几个额外的脚本来进一步自动化克隆、修补和构建过程，包括如何使用环境变量来配置 Elasticsearch。

# 为 PostgreSQL 配置网飞导体

针对 PostgreSQL 运行 Conductor 也在[自述文件](https://github.com/Netflix/conductor/blob/v3.5.1/docker/README.md)中有所描述。运行`docker-compose -f docker-compose.yaml -f docker-compose-postgres.yaml up`将为 PostgreSQL 启动一个额外的容器实例，并配置 Conductor 来使用它。

但是在我们的例子中，我们希望运行 PostgreSQL 的现有安装。除此之外，我们不希望在`public`模式中创建指挥数据库对象，这是默认设置。

为了实现这一点，我们需要改变`config-postgres.properties`文件。这存在于`conductor/docker/server/config`文件夹中。但是因为我们在每个版本上都有指挥回购，所以我们不能在那里改变它。

所以，我在我的项目根目录下创建了一个`docker`文件夹，它将包含对`conductor/docker`文件夹中文件的所有定制。在构建时，我只是复制这个文件夹的内容。

`02.build-conductor-server.sh`脚本现在变成了:

我们的本地`config-postgres.properties`文件只是原始文件的副本，修改了 PostgreSQL 部分，如下所示:

通过将`currentSchema`添加到数据源 URL，我们确保在指定的模式中创建了导体数据库对象。如果需要的话,`applicationName`属性可以帮助我们调试数据库会话。

除了它不工作。当我们仅使用这些更改运行 Conductor 时，`currentSchema`属性被忽略，所有内容都在`public`模式中创建。不酷。我对引起它的公共关系提出了一个错误。

我将为您节省几个小时的搜索时间来查找原因，并给出“解决方案”包括引号，因为感觉更像黑客。但这很有效。

在文件`postgres-persistence/src/main/java/com/netflix/conductor/postgres/config/PostgresConfiguration.java`中，用行`.schemas("public")`配置了 Flyway(它处理数据库迁移),这会导致任何更改默认模式的尝试都被忽略。删除此行可以解决问题。

我为此创建了`v3.5.1-fix-currentSchema-override.patch`，并将其包含在`01.get-conductor.sh`脚本中。

如果您想知道那里的`##`占位符，它们将被匹配环境变量的值所替换。我将在“整合”一节中讨论这个问题。与此同时，就用你的价值观来代替这些吧。

# 配置工作流归档

作为一个小而有用的奖励，我们通过配置工作流状态监听器，使已完成和已终止的工作流能够在 60 秒(默认)后归档。

只需将`conductor.workflow-status-listener.type=archive`添加到`config-postgres.properties`文件中。

无论如何，这将防止您的数据库被您永远不想再看到的工作流淹没。即使你这么做了，这些数据仍然会被存储在 Elasticsearch 中，并且可以随时访问。

# 把所有的放在一起

为了使上述内容在本地和我们的云环境中都能工作，我们想使用环境变量来配置 Elasticsearch 和 PostgreSQL。为了做到这一点，我们求助于`conductor/docker/server/bin`文件夹中的`startup.sh`文件。我们将这个文件复制到本地的`docker`文件夹中，这样我们可以做一些修改。

我们在`if..fi`块后添加了以下几行:

还记得你之前看到的那些`##`占位符吗？我们使用工具`sed`将它们替换为匹配环境变量中配置的值。

为了在本地运行，我们将环境变量及其值添加到`docker-compose.yaml`和`docker-compose-postgres.yaml`文件中，并将其复制到本地的`docker`文件夹中。例如，参见`docker-compose-postgres.yaml`文件:

我们使用神奇的`host.docker.internal`值指向本地 PostgreSQL 实例，因为`localhost`不能在 Docker 容器中工作。

`02.build-conductor-server.sh`脚本也复制了`startup.sh`。

最后，只剩下几个脚本了。

`03.build-conductor-ui.sh`负责构建指挥 UI 图像:

我们可以使用`04.run-local.sh`运行 Conductor，它实际上只做调用`docker compose up`:

或者，我们可以运行`04.run-local-postgres.sh`来运行 PostgreSQL 数据库:

这就是了。使用上面的脚本，您应该能够在几分钟内构建并运行网飞指挥的本地实例。并且将脚本连接到您最喜欢的 CI/CD 工具也应该是完全可行的。

# 最后

使用 Elasticsearch 7 和 PostgreSQL 运行网飞指挥需要一些工作，但是通过使用`git diff`和 bash 脚本的能力，我们可以使这个过程变得简单、可重复，并且在本地和云环境中都可用。

以上所有源代码都可以在 GitHub 的[上获得。](https://github.com/erikbrgr/conductor-es7-postgres)

我希望这篇文章能帮助你使用 Elasticsearch 7 和 PostgreSQL 开始学习网飞指挥。

编码快乐！

本文中的所有代码片段都是使用 [gistbuilder](https://levelup.gitconnected.com/create-beautiful-embedded-code-without-the-hassle-of-creating-github-gists-fe6ce2fa0109) 创建的，这是我专门为此编写的一个漂亮的小程序。

如果您觉得这篇文章有用，您可能会对我们如何在项目中安排工作流感兴趣。你很幸运，我也写了一篇关于这个的文章:

[](https://levelup.gitconnected.com/cron-docker-the-easiest-job-scheduler-youll-ever-create-e1753eb5ea44) [## Cron + Docker =你将创建的最简单的作业调度程序

### 最大功率和灵活性的两个组件。

levelup.gitconnected.com](https://levelup.gitconnected.com/cron-docker-the-easiest-job-scheduler-youll-ever-create-e1753eb5ea44)