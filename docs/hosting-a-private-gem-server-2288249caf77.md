# 托管一个具有 LDAP 身份验证和授权的安全私有 Gem 服务器

> 原文：<https://betterprogramming.pub/hosting-a-private-gem-server-2288249caf77>

## 让我们开始使用自己的 gem 服务器进行内部开发和私有 gem

![](img/58ca5bb8c40ad435311a2f72ef5cd210.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

# 背景

Ruby 软件开发包括在包或库中构建和发布软件。在 Ruby 世界中，这些包被称为 gems，它们通常发布在 [RubyGems](https://rubygems.org/) 上。来自世界各地的开发人员可以搜索这些宝石，安装它们，并将其集成到自己的项目中。RubyGems 还提供了一个很好的命令行来管理 gem，允许用户推拉 gem。

到目前为止，这很好，但如果有必要开发一些不应该为世界上每个人提供的私人宝石呢？不幸的是，RubyGems 不支持这一点，并且需要托管一个私有的 gem 服务器，以便能够在开发团队内部共享私有的 gem。

在这篇文章中，我将展示我托管自己的 Ruby gem 服务器的尝试。我将涉及以下几点:

*   RubyGems 的替代品有哪些？
*   我的托管 Ruby gem 服务器的解决方案的描述。
*   解决方案的进一步改进。

# 寻找现有的解决方案

在我开始开发任何代码或解决方案之前，我通常会花一些时间来搜索我可以重用来完成任务的替代方案或现有解决方案。我们并不总是需要从头开始重新发明一切。经过一些快速的研究，我发现[这篇文章](https://guides.rubygems.org/run-your-own-gem-server/)描述了托管你自己的`gem`服务器的几个选项。下面介绍了这些选项，并对每个选项进行了简短描述。

*   内置 gem 服务器:该服务器自带`Rubygems`，只能用于查看已安装的 gem。它不支持推或拉宝石。
*   [Gemirro](https://github.com/PierreRambaud/gemirro) :这个 gem 服务器侧重于镜像，也不支持推或者拉宝石。如果您想将您的管道和应用程序配置到一个私有的 gem 服务器，该服务器负责从 RubyGems 下载和兑现 gem，那么这是一个不错的选择。因此，如果 RubyGems 宕机，您仍然可以从内部服务器安装 Gems。
*   这是另一个现有的开源 Gem 服务器。幸运的是，该服务器既支持从命令行推送 gem，也支持从 web UI 上传和删除 gem。

在这一点上，我很清楚，实现我的任务的最佳选择将是 Gem in a Box，因为它比其他选项支持更多的功能。因此，我开始深入研究这个项目，以了解如何开始托管我自己的带有 gem 的 Ruby gem 服务器，以及这些项目支持哪些特性。因此，我提出了以下内置特性列表和一些问题的解决方法:

*   该项目提供了一个 web UI，允许查看、搜索、上传和删除 gem。
*   该项目提供了一个名为`inabox`的命令行工具。
*   命令`gem`可以用来从服务器上推送宝石。
*   该项目的 [wiki](https://github.com/geminabox/geminabox/wiki) 描述了一个扩展项目以支持`HTTP BASIC AUTH`、`Alternative UI styling`、`ssl configs`和其他特性的变通方法。

不幸的是，这些特征对我的情况来说是不够的，我缺少以下特征:

*   LDAP 基于角色的认证和授权。
*   API 密钥生成。
*   支持`gem signin`命令。
*   支持`[http://localhost/sginup](http://localhost/sginup)`请求。

因此，我决定在项目 wiki 上发布的一些解决方案的帮助下，用缺失的特性来扩展 Gem in a Box 项目。

# 解决方案

## 建筑决策

实现上述特性意味着需要持久存储一些数据。我们至少需要为每个 gem 的用户和所有者存储 API 密钥。因此，我开始在这里探索我的选择，因为我想尽可能简单，并且不集成任何数据库，如 [MySQL](https://www.mysql.com/) 、 [MongoDB](https://www.mongodb.com/) 等。我最终决定将数据保存在文本文件中。

编写软件来管理文本文件中的数据超出了我的范围。因此，我花了一些时间来寻找这个功能的现有解决方案。幸运的是，我发现 Ruby 提供了两个类来完成这项任务，它们有一个漂亮而简单的接口。这些类别是:

*   `[PStore](https://ruby-doc.org/stdlib-2.5.3/libdoc/pstore/rdoc/PStore.html)`:基于哈希实现基于文件的持久化机制。
*   `[YAML::Store](https://ruby-doc.org/stdlib-2.4.0/libdoc/yaml/rdoc/YAML/Store.html)`:提供与`PStore`相同的功能，除了它使用 [YAML](https://ruby-doc.org/stdlib-2.4.0/libdoc/yaml/rdoc/YAML.html) 来转储对象而不是编组。

因为这两个类使用相同的接口，所以我决定在我的解决方案中支持它们，并允许通过环境变量在它们之间进行选择。

## 数据模型

仅仅出于简单的原因，我决定制作实现预期特性所需的最简单的数据模型。我想出了以下三个模型:

*   用户:该模型持久化以下用户数据(`username`、`api_key`、`ldap_groups`)。
*   ApikeyIndex:该模型将 API 键与用户相关联。您可能会说 API 键已经与用户模型中的用户相关联了。是的，这是真的，但是因为我们在这里使用的是散列对象，所以使用反向索引比遍历用户对象来查找用户 API 键更容易。
*   GemIndex:该模型将推送的宝石与其所有者(推送宝石的第一个用户)相关联。

## 基于角色的授权

我对基于角色的身份验证和授权的要求非常简单，可以通过以下几点来满足:

*   下载和观看宝石是开放给任何人，无需任何认证。
*   推送、上传、拉取和删除 gems 受到 LDAP 认证的保护。
*   授权操作支持两个 LDAP 组(maintainer 和 admin)。
*   维护者只能推/拉他们自己的宝石。
*   管理员可以推/拉任何宝石。

# 功能实现

既然 Gem in a Box 使用了 [Sinatra](https://github.com/sinatra/sinatra) ，那么它也使用了 [Rack](https://github.com/rack/rack) 和 Rack 中间件。因此，我决定通过实现处理每个所需请求的中间件类来扩展解决方案。下面是对实现的中间件类及其用法的描述。

## GeminaboxApp::中间件::健康检查

这个中间件提供了一个静态健康检查端点，该端点在应用程序的根路由上可用:[http://localhost:8080/health](http://localhost:8080/health)*。*这个端点返回一个静态字符串`OK`。它可用于检查应用程序是否启动并响应`http`请求。

## **GeminaboxApp::中间件::注册**

不幸的是，端点`signup`并没有在盒子里的宝石中实现。因此，我编写了这个中间件来处理请求和收集用户数据。该中间件执行以下操作:

*   要求用户输入他们的 LDAP 凭据。这是由`[Rack::Auth::Ldap](https://github.com/Ultragreen/rack-auth-ldap)`中间件支持的。
*   如果用户凭证有效，中间件将为用户创建一个`api_key`(如果他们还没有的话)，将用户信息(LDAP 组、用户名、API 密钥)保存在用户存储中，并将`api_key`关联保存在 API 密钥存储中。最后，它将为用户显示 API 密钥。
*   如果用户凭证无效，它将继续要求输入有效凭证。

多次注册不会为同一个用户生成多个 API 密钥。只有当用户在商店中没有 API 密钥时，中间件才会生成用户 API 密钥。另一方面，LDAP 组将在用户每次注册时更新。

## GeminaboxApp::中间件::ApiKey

这个中间件做的事情几乎和`SignUp` 中间件一样，除了它处理 API 请求`/api/v1/api_key`而不是注册请求。每当用户试图使用`gem`命令行登录时，或者当用户试图在没有首先登录的情况下推送 gem 时，都会请求这个端点。

```
gem signin --host [http://localhost:9292](http://localhost:9292)
gem push myge-0.11.2.gem --host [http://localhost:9292](http://localhost:9292)
```

作为 LDAP 认证成功的结果，中间件将把用户和`api_key`数据持久化到相关的 store 对象中，并以文本格式返回`api_key`，以便将其添加到本地凭证文件中。

```
➜  : cat ~/.gem/credentials                                                                                                                                                               
---
[http://localhost:9292](http://localhost:9292): 7459f3d7-3ba6-4d44-b0f1-4e103690286c
[http://localhost:8080](http://localhost:8080): 1bf46c2f-3cf0-4f2d-9715-e9e6d0c5d604
```

## GeminaboxApp::中间件::ApiGem

Gem in a Box 提供了两个 API 端点，用于从服务器推送和拉出 Gem。这些终点是:

*   `POST /api/v1/gems`:用于将 gems 推送到服务器。每当使用以下命令行时，都会请求此端点。

```
$> gem push mygem-0.11.2.gem — host [http://localhost:8080](http://localhost:8080)
```

*   `DELETE /api/v1/gems/yank`:用于从服务器上拉(删除)宝石。每当使用以下命令行时，都会请求此端点。

```
gem yank mygem -v 0.11.2 --host [http://localhost:8080](http://localhost:8080)
```

`ApiGem` 中间件拦截上述请求的 API 调用，并执行以下操作:

*   验证 API 密钥是否存在。
*   验证是否允许 API 键推动或拉动宝石。
*   更新`GemIndex` 关联用户和 gem。如果执行的请求是`POST /api/v1/gems` 请求，并且 gem 是新的 gem，那么中间件将在`GemIndex` 存储中添加一个条目。另一方面，如果请求是一个`DELETE /api/v1/gems/yank`请求，并且这是服务器上 gem 的最后版本，那么中间件将从`GemIndex` 存储中删除索引条目。

## **GeminaboxApp::中间件::WebRequestsLdapAuth**

`WebRequestsLdapAuth` 中间件在一个盒子里截取 Gem 提供的三个 web 端点。这些端点如下所列:

*   `GET /upload`:该请求用于从 UI 查看上传的 gem。中间件只通过 LDAP 身份验证保护这个端点，不执行任何其他操作。
*   `POST /upload`:当用户点击上传 web 表单上的上传按钮时，会请求这个端点。
*   `POST /gems/.*gem`:当用户点击主页上的 gem 删除链接时，会请求这个端点。

对于最后两个请求，中间件将执行以下操作:

*   要求用户输入他们的 LDAP 凭据。
*   验证是否允许 LDAP 用户管理相应的 gem。
*   更新`GemIndex` 存储，与`ApiGem` 中间件**相同。**

# 码头支持

为了最终确定解决方案，我想添加对 Docker 的支持。因此，我在解决方案中添加了一个 Docker 文件，以便能够为 gem 服务器生成 Docker 映像。这些映像可以用来启动 Docker 容器中的 gem 服务器。下面是一个 gem 服务器的`docker-compose`文件示例，包含所有支持的环境变量。

如果您想尝试 gem 服务器，只需复制上面的`docker-compose`模板，修改 LDAP 配置，并执行以下命令:

```
$> docker-compose up -d
```

# 结论

如果你想开始开发私有 gem，运行一个 gem 服务器是必须的。运行自己的 gem 服务器有几个解决方案。目前，盒子里的宝石是最先进的，也是最接近 RubyGems 的。然而，该项目仍然缺少一些安全特性，如防止恶意代码注入(任何有权访问服务器的人都可以推送、上传或删除 gem)和基于角色的授权。

这篇文章提出了一个在盒子中扩展 Gem 并支持上述特性的解决方案。完整的源代码解决方案可以在 [GitHub](https://github.com/wshihadeh/ruby_gems) 上找到，gem 服务器的 Docker 镜像在这个库中发布[。该解决方案仍然需要一些改进和增强，任何贡献都是非常受欢迎的。](https://hub.docker.com/repository/docker/wshihadeh/rubygems)