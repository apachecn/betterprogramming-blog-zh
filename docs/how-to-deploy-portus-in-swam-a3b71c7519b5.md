# 如何在 Swarm 中部署 Portus 和 Docker 注册表

> 原文：<https://betterprogramming.pub/how-to-deploy-portus-in-swam-a3b71c7519b5>

## Portus 是下一代 Docker 注册中心的开源授权服务和用户界面

![](img/441f700e784957d1794aed2ee24d626a.png)

[月亮](https://unsplash.com/@sir_moon?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[破浪](https://unsplash.com/s/photos/swarm?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)的照片

托管你自己的私有 [Docker 注册表](https://docs.docker.com/registry/)是一个相对简单的任务，只要执行一个`docker`命令或者在`docker`命令变成一个长命令的情况下创建一个`docker-compose`文件就可以了。

不幸的是，Docker Registry 没有提供一个复杂的 UI 来查看和浏览托管的 Docker 图像。

此外，没有内置的用户身份验证和用户操作授权功能。(Docker 注册表只提供基本的 auth。)

幸运的是，Docker Registry 可以配置/扩展为支持用户认证和授权。可以用来扩展 Docker Registry 以提供这些特性和更多特性的开源项目之一是 [*Portus*](https://github.com/SUSE/Portus) 。

在本文中，我将解释如何部署 Docker 注册中心，并使用 Portus 扩展它以提供以下特性:

*   用户认证:只有通过认证的用户才可以使用 UI 并将 Docker 图像推送到注册中心。
*   用户授权:推和拉操作在执行之前都是经过授权的。
*   经过验证的用户可以搜索 Docker 图像。

我将使用 [Swarm Orca](https://github.com/wshihadeh/swarm_orca) 作为编排和部署工具来部署提议的解决方案。

简而言之，Swarm Orca 是一个 Ruby gem，可以帮助部署应用程序和编排 Docker 部署到集群。(你可以查看我的[帖子](https://medium.com/@wshihadeh/orchestrating-rails-docker-deployment-in-swarm-474aa6b8d35b)了解更多关于这个工具的信息。)

[](https://medium.com/faun/orchestrating-rails-docker-deployment-in-swarm-474aa6b8d35b) [## 在 Swarm 中协调 Rails Docker 部署

### 使用 swarm orca gem 简化 docker swarm 部署

medium.com](https://medium.com/faun/orchestrating-rails-docker-deployment-in-swarm-474aa6b8d35b) 

为了能够实现上述堆栈，我们需要使用 Swarm Orca 部署以下服务:

*   Docker 注册表
*   葡萄牙语
*   NGINX

对于本地环境，我们还需要以下依赖项:

*   MySQL:这是 Portus 能够持久保存用户信息的依赖项。
*   LDAP 服务器:用于管理用户帐户。
*   LDAP 服务器管理:LDAP 服务器的管理 UI 界面。

既然现在已经清楚了部署需要什么服务，让我们从部署依赖项开始。第一步是使用下面的命令创建一个新的 Swarm Orca 项目:

```
$> gem instal swarm_orca
$> orca new platform_services wshihadeh platform
```

幸运的是，默认项目包括我们的堆栈所需的两个服务的堆栈，MySQL 和 NGINX。

下面是为`mysql`服务渲染的 swarm 堆栈(您可以替换堆栈中的变量，并使用 Docker 命令部署它)。

# **LDAP**

我们需要 LDAP 服务器作为 Portus 管理本地环境中的用户帐户的依赖项。对于其他环境(尤其是生产环境)，您需要一个外部 LDAP 服务器。

下面是一个 swarm 堆栈文件，包含两个服务的定义，`ldap server`和`ldap admin interface`。在替换文件中的所有占位符后，您可以使用`docker deploy`命令部署这个堆栈。

# **Docker 注册表**

上述 Docker 栈可用于部署 Docker 注册应用程序。栈本身没什么特别的。但是，该堆栈允许按照下面的说明配置 Docker 注册中心来启用 Portus 集成。

覆盖`registry`命令，以便能够信任 Portus 的自签名证书。(如果您不使用自签名证书，则不需要这样做。)

init 脚本将自签名证书复制到注册表容器内的信任路径，然后启动 Docker 注册表。

## **设置 Docker 注册表配置**

注册表配置文件涵盖多个主题或章节。

第一部分是存储部分。在此部分，我们可以选择是将图像保存在`filesystem`还是`cloud`中。第二部分用于设置注册中心`http`端点配置。

第三部分是`auth`部分，在这个部分中，我们可以配置注册表认证方法。注册表支持三种类型的身份验证提供程序:

*   `silly` : 这个提供程序应该只用于测试或开发目的，因为它只检查授权头的存在，而不检查授权头的值。
*   `htpasswd`:这个提供者允许我们使用 Apache `htpasswd`文件配置注册中心的基本认证。
*   `token` : 这个提供者允许我们基于令牌将认证过程从 Docker 注册中心分离出来。(这是 Portus 集成所需的部分。)

Portus 提供了一个可以与 Docker 注册中心集成的认证系统，将认证和授权从 Docker 注册中心本身分离出来。

注册表配置文件的最后一部分是`notifications`部分，我们在这里配置 Docker 注册表，以便在注册表事件被触发和执行时通知`portus`。

下面是完整的注册表配置文件，包含上述所有部分。

注意:如果您对`portus`域名使用 HTTPS，上述文件中的 URL 需要更改。

# **葡萄牙语**

堆栈中的最后一个服务是 Portus 应用程序本身，为了成功部署 Portus 并扩展 Docker 注册表，我们需要涵盖以下几点。

*   `Portus`应用部署。
*   Portus 和 Docker 注册表的 NGINX 配置。

实现第一点可以通过部署下面的 Docker 栈来完成。该堆栈将创建两个 Docker 服务，一个用于 Portus web 服务器，另一个用于处理异步作业的 Portus 后台作业。

该堆栈提供了一种配置 LDAP 服务器身份验证和自动 Docker 映像清理的方法。

请注意，`extra_host`部分仅在 Portus 的域名仅由`/etc/hosts`管理且不包含在任何 DNS 服务器中的情况下才需要。

# NGINX 配置

Docker 注册中心和 Portus 的 NGINX 配置可以合并，由相同的 NGINX 服务器定义提供服务。

例如，我们可以配置 NGINX 在同一个域名`hub.shihadeh.intern`上为 Portus 用户界面和 Docker API 提供服务。服务器定义需要包括以下路径的路由:

*   `/v2/*`:对这个路径的请求是发送到 Docker Registry API 的请求，因此，它们应该被代理到 Docker Registry 服务。
*   `/v2/token`:请求此端点为认证过程生成令牌。这个请求应该由 Portus 来处理，因为 Portus 将基于 LDAP 认证而不是注册表来生成令牌。此端点需要从第一条路径中排除。
*   Docker 注册中心将调用这个端点来通知 Portus 关于注册中心的事件，比如推送图片或标签。
*   `/`:这将作为根路由和上述路径不包括的所有端点。所有这些请求都将被代理给 Portus，以便能够显示 Portus 用户界面并与之交互。

下面的 NGINX 配置文件显示了`hub.*`域名所需的 NGINX 配置。如上所述，该服务器定义对注册中心和 Portus 的代理请求。

# **蜂群部署**

这篇文章中描述的步骤在[这个 GitHub 库](https://github.com/wshihadeh/platform_services)中实现。您可以执行以下指令，将相同的堆栈重新部署到您的本地机器或集成新的环境。

克隆回购。

```
git clone [git@github.com](mailto:git@github.com):wshihadeh/platform_services.git
```

为奥卡安装所需的宝石。

```
$> cd platform_services/capistrano
$> gem install bundler
$> bundle install
```

更新本地环境文件`capistrano/config/deploy/local.rb`中的以下配置项:

*   `user`:这应该是你的用户名，
*   只有在使用`/etc/hosts`管理 DNS 记录时，才需要`registry_domain`和`registry_ip`。否则，您可以删除它们。
*   `deploy_to`:更新该变量中的路径，并确保创建了该文件夹，以及`local.rb`文件中提到的所有子文件夹，如`config_path`。您还可以修改任何其他值来满足您的规范。

如果需要，请更新配置文件夹。Docker 注册表配置存储在项目的根路径下的`configs`文件夹中。您可以更新注册表配置文件以及创建自己的自签名证书。

如果需要，更新 NGINX 配置。NGINX 配置存储在项目根目录下的`nginx`文件夹中。

默认情况下，Swarm Orca 将在部署期间为 NGINX 构建 Docker 映像，因此如果您需要对 NGINX 配置进行任何更改，您需要检查`nginx`下的文件并相应地修改它们。

## 开始服务部署

```
SCM=copy bundle exec cap local deploy:setup
SCM=copy bundle exec cap local copy_configsSCM=copy bundle exec cap local deploy:ldap
SCM=copy bundle exec cap local deploy:mysql
SCM=copy bundle exec cap local deploy:registry
SCM=copy bundle exec cap local deploy:portus
SCM=copy bundle exec cap local deploy:nginx
```

对于 LDAP 服务器，您可以导入下面的[文件](https://gist.github.com/wshihadeh/9056bd847d98485cd2d9f8862a601a2e)来填充一些用户和组。

使用`docker login`命令进行测试:

```
docker login -u ${user} ${domain}
```

# **结论**

出于安全原因，用认证和授权机制来保护私有 Docker 注册中心是非常重要的。

虽然 Docker 注册中心没有提供这些特性，但是可以通过使用 [Portus](http://port.us.org/) 来扩展注册中心以支持这些特性。

在这篇文章中，我解释了如何完成这项任务，并提供了一个本地部署的实现。