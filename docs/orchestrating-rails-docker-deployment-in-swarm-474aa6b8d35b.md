# 在 Swarm 中编排 Rails Docker 部署

> 原文：<https://betterprogramming.pub/orchestrating-rails-docker-deployment-in-swarm-474aa6b8d35b>

## 使用 Swarm Orca gem 简化您的 Docker swarm 部署

![](img/53081289c7fe4171d092f944d3ac8b0c.png)

照片由特雷弗·科尔曼在 [Unsplash](https://unsplash.com/s/photos/orca?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# **背景**

Swarm Orca 是一个简单的 Ruby gem，它可以帮助你像在本地部署一样部署到多个 Swarm 集群。

Docker 是可以用来简化软件部署过程的工具之一。在我看来，使用 Docker 进行软件部署的最大优势是它提供了一种简单的方法来打包软件及其依赖项，包括操作系统。因此，不需要在主机服务器上安装这些依赖项(唯一需要的依赖项是 Docker 本身)。

Docker 附带的另一个工具是 Swarm，这是一个开源的容器编排平台。它是 Docker 的本地集群引擎。Swarm 提供了一种构建高可用 Docker 集群和以高可用模式部署服务的方法。

使用 Swarm，您可以定义您的服务以及这些服务的副本数量。Swarm 将确保每个服务的状态总是符合要求，如果一个容器关闭，Swarm 将尝试重新创建该容器——或者甚至尝试在一个 Docker 节点关闭的情况下重新定位服务。

使用 Swarm 进行部署非常简单，可以通过以下步骤进行描述:

*   使用`docker-compose`文件定义群组堆栈
*   用`docker stack`命令创建 Docker 栈

```
docker stack deploy --compose-file docker-compose.yml ${stack_name}
```

一旦命令`docker stack deploy`被执行，Swarm 将创建堆栈文件中定义的服务，然后在集群中寻找一个空闲节点来部署(创建容器)每个定义的服务，并确保服务配置得到满足。

例如，您可以在部署或推出服务的过程中定义容器的替换顺序。您还可以为每个已定义的服务、日志记录驱动程序和其他配置定义副本的数量。

一旦您开始管理多个微服务或 Docker 堆栈的部署，并开始通过测试、试运行和生产支持多个环境，从配置角度来看，事情将变得更加复杂。部署过程中会出现新的挑战。这些挑战列举如下:

*   Docker 服务可以通过定义环境变量来配置。这些变量的值可以在 Docker 堆栈中定义。对于大多数变量来说，这是可以的；但是，对于敏感信息，我们需要为这些变量提供加密/解密工具。
*   部署过程的自动化:为了保持 Docker 部署简单，需要自动化服务部署步骤，例如，将堆栈文件复制到 Swarm manager 节点，如果需要，提取部署所需的 Docker 映像，并执行 Docker 命令来创建服务。
*   支持多种环境需要将服务的配置值从堆栈文件中分离出来，以便能够在多个环境中使用相同的堆栈，而无需复制这些文件。
*   支持多种环境需要将组合堆栈文件转换为模板，以便我们可以为不同的环境提供不同的配置——例如，在需要将服务部署到具有不同日志记录驱动程序和相同堆栈文件的两个环境的情况下。
*   对于像 MySql 这样在 RDMS 中持久化数据的应用程序，需要在推出新版本之前运行数据库模式迁移，以实现零停机部署。不幸的是，我们不能将数据库迁移定义为另一种服务，因为 Swarm 数据库迁移容器将在完成迁移后停止运行，因此 Swarm 将一直尝试重启它们。另一个原因是我们希望通过数据库迁移来阻止部署，以确保只有在数据库迁移成功完成后才部署新代码——否则，部署过程将会失败。
*   需要将所有的配置值存储在版本控制中，这样我们就可以跟踪谁在做更改，这些更改是何时做的，以及为什么要做更改？
*   以与生产相同的方式支持开发环境中的本地部署。

市场上有一些解决上述挑战的解决方案，如 [Portainer](https://www.portainer.io/) 和 [Rancher](https://rancher.com/docs/rancher/v1.3/en/swarm/) 。

然而，并非所有的挑战都能在一个解决方案中解决。因此，我开始开发一个命令行工具来解决上述所有挑战，并为开发人员提供一种简单的方法来使用 Swarm 将服务部署到本地和生产环境中。

# **宝石特征**

因为我主要使用 Rails 应用程序，所以我关心的是实现这个工具来支持 Rails 应用程序部署。然而，该工具可用于部署第三方服务，如 Nginx、MySQL 和 Redis。它还可以用来部署非 Rails 应用程序。

下面详细描述了我想在该工具的第一个版本中包含的主要特性:

*   该工具必须支持多阶段部署:这意味着该工具应该能够用于将同一组服务部署到多个环境中，包括本地开发和生产环境。
*   该工具应该能够管理和组织所有支持的环境和应用程序的配置项/文件。
*   支持 Docker-stack 模板:这个特性对于支持多阶段非常重要，因为它有助于减少托管 Docker-stack 文件的数量。例如，我们可以只为每个受支持的阶段呈现一个模板，而不是为每个受支持的阶段提供 Docker 堆栈。
*   支持加密密钥生成以及对配置项进行加密和解密。能够在文件系统中存储加密的敏感数据，并且仅在 Docker 主机上的部署期间解密这些值。
*   支持 Rails 数据库操作，比如数据库、创建迁移和种子。
*   提供一种为应用程序加载自定义种子的方法。如果您将特定于客户的种子保存在应用程序源代码之外，这个特性会很有用。
*   为执行部署提供简单灵活的命令行，允许对单个堆栈、自定义数量的堆栈或所有堆栈执行部署。

为了构建具有这些特性的工具，我决定构建一个能够处理 Swarm 部署的 Ruby gem，并且我还决定利用 [Capistrano](https://capistranorb.com/) ，这是一个用 Ruby 编写的远程服务器自动化和部署工具。Capistrano 将帮助我实现我想开发的大部分功能。

我将部署宝石命名为[虫群虎鲸](https://github.com/wshihadeh/swarm_orca)、*T5，在 [GitHub](https://github.com/wshihadeh/swarm_orca) 和[RubyGems.org](https://rubygems.org/gems/swarm_orca)*上很容易找到。*请随意尝试并给我反馈。*

# **用 Swarm Orca 建立一个示例项目**

在下一节中，我将尝试展示如何使用 [Swarm Orca](https://github.com/wshihadeh/swarm_orca) 建立一个新项目，并将其部署到本地开发环境中。我建立的 orca 项目将包括(仅为了简单起见)三个服务，它们是:

*   关系型数据库
*   Nginx
*   示例-rails-app

这个项目的第一步是用下面的命令在本地安装 gem。

```
➜  ~/ gem install swarm_orca
```

一旦您安装了 gem，就会安装`orca`命令行，您可以用下面的命令创建一个新项目:

```
#orca new ORCA_DIRECTORY_NAME GIT_FORK DOCKER_NETWORK
➜  ~/ orca new  orca_test wshihadeh orca_test_network
```

*   `ORCA_DIRECTORY_NAME`是项目文件夹名
*   `GIT_FORK`是你的 Git 叉名吗
*   `DOCKER_NETWORK`是将部署服务的 Docker 网络的名称

上面的命令将创建一个样例 Orca 项目，其中包含启动本地部署所需的所有文件和一些预定义的堆栈。

下面是默认情况下将创建的文件列表。如 Nginx，MySQL Redis 等。

```
➜  ~/ orca new  orca_test wshihadeh orca_test_network                                                                                                                                                                      
Start Generating orca files
      create  .gitignore
      create  .ruby-version
      create  README.md
      create  capistrano/Capfile
      create  nginx/Dockerfile
      create  nginx/nginx.conf
      create  redis/Dockerfile
      create  redis/redis.conf
      create  capistrano/config/deploy/template_stage.rb
      create  capistrano/Gemfile
      create  .ruby-gemset
      create  capistrano/config/deploy.rb
      create  application_stack/docker-stack-elasticsearch.yml.erb
      create  application_stack/docker-stack-errbit.yml.erb
      create  application_stack/docker-stack-mysql.yml.erb
      create  application_stack/docker-stack-nginx.yml.erb
      create  application_stack/docker-stack-rabbitmq.yml.erb
      create  application_stack/docker-stack-redis.yml.erb
```

*   支持的 Docker-stack 文件可以在`application_stack`文件夹下找到。这些堆栈应该是 ERB 堆栈模板。
*   支持的环境配置项可以在`capistrano/config/deploy/${env}.rb`下找到。
*   共享部署配置可以在`capistrano/config/deploy.rb`中找到。
*   自定义 Docker 映像和配置文件可以在`${service_name}`下找到，例如 Nginx 或 Redis。

新的命令输出还将包括后续步骤的说明。

```
Complete!
Next step is to create development stage file from the
 template file use the following commands to do it
cd orca_test/capistrano
gem install bundler
bundle install
cd orca_test/capistrano/config/deploy
cp template_stage.rb local.rb
Replace all ${VAR} with a valid value
Read Readme for more information
```

为了能够在本地部署我们的服务，我们需要执行上面提供的说明，然后执行以下操作:

*   创建本地舞台。

```
➜  ~/ pwd
orca_test/capistrano/config/deploy
➜  ~/ cp template_stage.rb local.rb
```

*   因为我们不打算只支持默认应用中的 MySQL 和 Nginx，所以我清理了`local.rb`，只包含这些配置。我们必须在文件顶部定义服务者和用户，并且只保留`Nginx`、`MySQL`和`swarm_manager`角色。需要添加`deploy_to`，因为默认的`deploy_to`在我的本地机器上不存在(我们也可以创建默认的`deploy_to`文件夹，也就是`/home/deploy/orca`)。

*   由于我们的项目不叫`orca`并且我们在本地有不同的部署路径，我们需要覆盖`deploy.rb`中的一些默认配置。

*   提交更改，并将其推送到您的分支。
*   现在我们已经为部署做好了准备，只需使用以下命令之一进行部署:

```
# For deploying all stacks 
s➜  ~/  bundle exec cap local deploy:setup deploy:all
# For deploying only mysql
s➜  ~/  bundle exec cap local deploy:setup deploy:mysql
# For deploying only nginx
s➜  ~/  bundle exec cap local deploy:setup deploy:nginx
# For deploying only nginx and mysql 
s➜  ~/  bundle exec cap local deploy:setup deploy:nginx deploy:mysql
# For deploying only nginx and mysql
s➜  ~/ DEPLOYED_STACKS='nginx mysql' bundle exec cap local deploy:auto
```

*   您还可以通过将以下环境变量添加到部署命令行，使用复制策略而不是 Git 来执行本地部署:

```
s➜  ~/ SCM=copy bundle exec cap local deploy:setup deploy:all
```

下一步是将 Rails 应用程序添加到 Orca 中，并尝试将其部署到本地环境中。假设我们有一个具有以下 Docker 映像的 Rails 应用程序，`wshihadeh/simple_rails`可以配置以下环境变量:`RAILS_ENV`、`RAILS_LOG_TO_STDOUT`、`DATABASE_URL`和`LOG_LEVEL`。

*   集成该应用程序的第一步是为新应用程序创建 Docker-stack 模板，并用文件名`application_stack/docker-stack-simple_rails.erb`保存它。该文件的内容应该类似于以下内容:

*   下一步是用下面的`simple_rails`应用程序信息更新`capistrano/config/deploy.rb`。

`db_apps_stacks_mapping`是一个散列对象，它定义了连接到数据库的支持堆栈和应用程序的列表。散列的关键字是栈名，这需要匹配栈模板文件名。例如，堆栈`simple_railS`应该在`application_stack/docker-stack-simple_rails.erb`中定义。键值可以是堆栈中使用数据库的应用程序列表的空数组。由于`simple_rails`只有一个 Rails 应用程序，我们可以将其设置为与`simple_rails`相同的值。

*   用`simple_rails`应用程序的配置更新本地阶段。唯一需要的环境变量是`stack_name`。只有当您希望支持数据库迁移时，才需要`${application}_database_url`。其余的变量取决于应用程序及其堆栈模板文件。

我们还需要更新本地 stage 来拥有应用程序角色。

使用角色`simple_rails`，我们允许将应用程序部署到本地环境，使用角色`simple_rails_db`，我们允许在本地环境中执行数据库迁移。在登台和生产环境中，这些角色可以分布在多个 Docker 节点上，以便在部署期间平衡负载。

*   要在本地部署`simple_rails`，我们需要首先创建数据库和种子，然后部署应用程序。这些操作可以通过以下命令完成:

```
bundle exec cap local deploy:setup
bundle exec cap local deploy:create_simple_rails_dbs
bundle exec cap local deploy:migrate_simple_rails_dbs
bundle exec cap local deploy:seed_simple_rails_dbs
bundle exec cap local deploy:simple_rails
```

任务`deploy:simple_rails`将自动执行数据库迁移——因此，它只在第一次部署时需要。

*   在最后一步中，我们以纯文本的形式存储了`simple_rails_database_url`。这不是一个好的做法，因为任何有权访问回购的人都可以找到数据库密码。因此，在这一步，我们将生成一个加密密钥并加密`simple_rails_database_url`变量。

使用下面的命令，我们可以生成一个新的加密密钥。请确保保护此密钥，不要发布它。

```
➜ : orca gen_enc_key                                                                                                                                     
Encryption Key: 6764f56ee9e24f3bf5903a394ce1081a7921f212bf017182149219fdc82f21d4
```

要加密配置项，我们可以使用以下命令:

```
➜ : orca encrypt $KEY mysql2://root:mysql@mysql/simple_rails_local
OQ8VXMq5Hz/xlygPmphrtVVpOcE
```

在使用加密密钥进行重新部署之前，我们需要为本地环境更新应用程序的配置。

应用程序配置上唯一需要的更改是将`simple_rails_database_url`配置重命名为`encrypted_imple_rails_database_url`，并将其分配给加密值。Swarm Orca 将在部署期间负责解密这个值。

```
encrypted_simple_rails_database_url: 'OQ8VXMq5Hz/xlygPmphrtVVpOcE',
```

*   现在我们已经完成了应用程序`simple_rails`的重新配置，我们可以将它重新部署到本地环境中。这次唯一不同的是，我们需要在部署命令行中提供加密密钥，以便能够解密 Docker 主机上的值。可以使用以下命令行执行部署:

```
➜ : export ENCRYPTION_KEY=$key
➜ : export SCM=copy
➜ : bundle exec cap local deploy:setup deploy:simple_rails
```

*   通过在`capistrano/config/deploy`下创建新的 stage 文件，可以简单地集成新的环境。这可以通过复制配置文件并在新文件中更新所需的配置来完成，例如服务器 IP 甚至应用程序配置。
*   改进和自动化解决方案的最后一步是构建 Jenkins 管道，用于自动化 Jenkins 的部署。以下 Jenkins 文件是使用 Swarm Orca 定义部署管道的示例文件。

# 结论

Docker containers 和 Swarm 简化了软件部署过程，但它们没有解决我们在管理遵循微服务架构的软件部署时面临的所有挑战。

市场上有一些解决这些挑战的解决方案，但大多数都需要花费一些精力来设置服务器或将其安装在 Swarm 上。

Swarm Orca 是一个简单的 Ruby gem，它可以帮助您以与本地部署相同的方式部署到 Swarm 集群。gem 支持部署到多个集群、加密配置项和运行数据库迁移等功能。