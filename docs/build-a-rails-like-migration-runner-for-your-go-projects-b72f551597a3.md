# 为您的 Go 项目构建一个类似 Rails 的迁移运行器

> 原文：<https://betterprogramming.pub/build-a-rails-like-migration-runner-for-your-go-projects-b72f551597a3>

## 可靠地向上和向下迁移开发和测试数据库

![](img/1711729f91f563eb48dd0f0ad3120bf8.png)

照片由[路德维希·沃伦多夫](https://unsplash.com/@wallendorff?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

发现自己有一周的空闲时间，我决定将一个旧的 Rails 项目移植到 Go 中。我努力整理数据库模式，规划我将使用的包，并就是否使用 serial IDs、UUIDs 或 ULIDs 进行内部辩论。只有当所有的细节都确定后，我才意识到一个显而易见的事实——在我开始应用程序之前，我需要一些管理迁移的方法。

Rails 是一个全栈的 web 框架，旨在让项目轻而易举地启动。Go 是一种编程语言，擅长分布式系统，原则上避开框架。虽然 Go web 框架已经存在(例如 Gin)，但是我觉得通过最小化“魔法”和编写自己的工具，还可以学到更多东西。

具体来说，我需要可靠地上下迁移开发和测试数据库的能力，这样当我不可避免地把应用程序开发搞得一团糟时，回滚数据库并重试就不会有任何痛苦。

# 1.环境

Migrate ( `[golang-migrate/migrate](https://github.com/golang-migrate/migrate)`)是一个运行迁移的出色的包，它附带了一个可以在 Go 应用程序中使用的库和一个生成和触发迁移文件的命令行工具。

然而，使用现成的 CLI 的问题是，设置环境变量和手动键入数据库地址有很多麻烦。我想要无缝的解决方案，它可以为我的应用程序加载正确的环境配置，并迁移正确的数据库，而不需要我亲自操作。这意味着被困在图书馆里。

作为第一步，我选定了一个变量来向我的程序发送当前环境的信号:`FB05_ENV=[development|test|staging|production]`。`FB05`是我的应用程序的名字——随便你怎么称呼你的。我希望这是我必须手动更改的唯一 env 变量。

第二，我在我的项目根中创建了`environment.yaml`。这将最终保存我的应用程序需要运行的每个 env 变量(当然，注意隐藏敏感的变量)。到目前为止，它仅限于数据库配置:

这不一定是 YAML 的文件。如果你是一个`.env`或者其他配置格式的粉丝，那也可以。

假设您已经单独创建了相应的数据库，这就是开始构建我们的跑步者所需的所有设置。

# 2.'迁移. go '

我将运行程序命名为`migrate.go`，它位于项目根下的`cmd/migrate/migrate.go`。最后，我们将使用类似于 Rail 的`rake db:migrate`的`make`命令来运行这个带有适当参数的脚本。

让我们从`main`功能开始:

在第 3-13 行，我们指定并解析了控制我们的程序应该在哪里寻找环境配置文件的标志。在这种情况下，我将默认值设置为指向我们项目的根目录中的`environment.yaml`。

接下来，我们检查以确保调用脚本时至少有一个参数:让`Migrate`包运行的命令。其中一些命令(例如`(m* Migrate) Steps(n int)`)带有自己的参数，但是我们还没有检查这一点。

在第 24-29 行，我们加载由我们的标志描述的环境文件。在幕后，这是由一个名为 Viper 的包完成的。稍后会有更多内容。

成功加载环境后，我们就可以使用指定的命令和传递的任何参数来运行迁移了。

让我们仔细看看`loadEnvVars`和`runMigration`。

## `'loadEnvVars'`

`loadEnvVars`获取我们在`main`中解析的`configName`和`configPath`标志，并返回一个指向`viper.Viper`的指针。

[Viper 是一个用于 Go 应用程序的高性能配置包](https://github.com/spf13/viper),因此，它对于这样的脚本来说是多余的。然而，我计划在我的项目中的其他地方使用 Viper，所以把它放在这里也是有意义的。如果你喜欢轻量级的，可以考虑 [envconfig](https://github.com/kelseyhightower/envconfig) 、 [GoDotEnv](https://github.com/joho/godotenv) ，或者只是自己解析环境文件。

在第 12-14 行，我们将 Viper 配置为从位于项目根目录下的名为`environment.yaml`的文件中读取数据。注意，我没有创建一个名为`configType`的标志——我已经硬编码了`yaml`，因为我知道我的配置文件将总是 YAML。你可能会有不同的决定。

`viper.ReadInConfig`使 Viper 读取源文件中包含的 env 变量并创建一个`*viper.Viper`。当我们想要访问一个环境变量时，我们用像`(v *Viper) Get(key string)`这样的方法查询这个结构，而不是运行`os.Getenv`。

但我们还没完！Viper 已经加载了整个`environment.yaml`文件，包括`test`、`production`的配置，以及您可能已经存储在那里的任何其他组。这就是为什么`loadEnvVars`做的第一件事就是用`os.Getenv("FB05_ENV")`寻找目标环境。我们将这个值传递给`viper.Sub`以返回一个`*viper.Viper`，它只包含与我们的目标环境匹配的环境变量的子集。

当`loadEnvVars`返回时，这个`*viper.Viper`会发生什么？你会注意到，当我在`main:25`上调用`env, err = loadEnvVars(*configName, *configPath)`时，我没有在中声明任何新的变量。`env`实际上是在`main` : `var env *viper.Viper`之外声明的全局变量。这样，就可以在脚本的任何地方访问它，就像`os.Getenv`一样。

## '运行迁移'

这是一个矮胖的。抓紧了:

首先，我们使用迁移包创建一个新的`*migrate.Migration`。这里的设置有些复杂，因为您必须根据迁移的存储位置和使用的 DBMS，通过空白导入来注册不同的 Migrate 子包。

我将迁移存储在本地，我选择的药物是 PostgreSQL。所以我的导入看起来像这样:

`migrate.New`的第一个参数是`source`:迁移文件存储的位置。我的在我的项目根目录下的`db/migrations`。

第二个是数据库 URL，这就是为什么在运行迁移之前加载环境变量如此重要的原因:

想象一下，每当您想要运行迁移时，都必须键入这些内容。想象一下，在生产中必须手动运行您的迁移。这些就是这个小型项目要解决的问题。

在创建了`*migrate.Migration`并验证了所提供的任何数字参数确实是数字之后，我们使用指定的命令`switch`并触发相应的迁移方法。

我不会在这里重复文档，但是作为一个例子，`(m *Migration) Up()`通过迁移文件夹中的每次向上迁移向前迁移数据库*，而`(m *Migration) Down()`则相反。如果您来自 Rails，这一点需要记住——这里没有单一的`change`方法。每次迁移都必须有单独的 up 和 down SQL 文件，以分别应用和撤销更改。*

*简单的向上迁移可能如下所示:*

*而相应的向下迁移如下所示:*

*(如果你还没猜到的话，我正在打造一个老式的脸书克隆版)。*

# *运行迁移*

*我们现在有了运行迁移的代码，但是输入命令`go run cmd/migrate/migrate.go steps 5`并不方便。*

*最后，我们将创建一个简单的 Makefile，为我们的迁移任务提供方便的简写。额外的好处是，每当我们更新数据库模式时，我们将自动执行转储数据库模式这一至关重要的任务。*

*这看起来更像 Rails。少数需要变量的任务由`make db_migrate_steps STEPS=3`触发。这是一个粗糙的边缘，但对我的目的来说足够好。*

*如果我们想更进一步，使之成为一个真正优雅的迁移者，我会探索使`migrate.go`与项目无关的方法(如果我们真的想推动自己，与 DBMS 无关)。然后我们可以安装二进制文件，并把我们的迁移运行器带到每个新项目中。*

*采用更懒惰的方法，`db_dump_schema`是拼图的最后一块。您很少希望在项目的整个生命周期中依赖于迁移，尤其是如果项目将在生产中运行的话。一年后，您将有数千个迁移文件混杂在源代码控制中，每次重建数据库时都要一个接一个地应用。*

*将当前数据库模式的转储签入源代码控制更为简洁。这样，您就可以在一个 SQL 文件中为 DBMS 提供数据库应该有的样子，而不是将一步一步的指令输入到 DBMS 中以最终达到所需的状态。*

*Rails 是这样建立测试数据库的:它获取开发数据库的转储，并将其应用到测试环境中。这就是我选择在 Makefile 中快速添加的内容:*

*在`db_dump_schema`中，我们检查当前环境是否为`development`，以确保我们不会用测试数据库的结构覆盖现有的转储。在`if`块开始的`@`只是防止`make`在命令运行时回应命令。*

*如果环境是`development`，我们运行 Postgres 的`pg_dump`工具，在`SCHEMA`中指定输出文件。如果您没有使用 Postgres，请务必查找如何从首选 DBMS 转储。*

*注意每个改变数据库结构的迁移操作是如何自动调用我们的`pg_dump`方法:`@$(MAKE) db_dump_schema`。我们可以放心，因为我们签入源代码控制的模式总是与开发数据库的当前状态相匹配。(`@$(MAKE)`仅仅意味着“使用`make`运行下面的任务，而不回显命令”)。*

*为了快速建立一个测试数据库，我编写了`make`任务`db_test_prepare`，它只不过是从转储的 SQL 构建模式的 Postgres 语法。*

# *结论*

*这就是:为您的 Go 项目构建一个超快速的类似 Rails 的迁移运行器。[在这里找到源代码](https://github.com/AngusGMorrison/fb-class-of-05)。*

*下一步是用每个项目的配置文件将它概括成一个可安装的二进制文件，使您可以灵活地选择使用命名约定和 DBMS。交给你了。*