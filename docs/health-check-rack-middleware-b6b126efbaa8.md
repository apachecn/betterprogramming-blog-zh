# 构建健康检查机架中间件

> 原文：<https://betterprogramming.pub/health-check-rack-middleware-b6b126efbaa8>

## 确保您的应用程序全面健康

![](img/2d1223e599d1cb065b3a39b775a5accd.png)

由[嘘奈杜](https://unsplash.com/@hush52?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

为软件应用程序实现健康检查端点是软件开发过程中的一种模式，也是最佳实践之一。运行状况检查端点可用于多种目的。以下是运行状况检查端点的一些使用案例列表:

*   监控系统:健康检查端点可以用作监控系统的数据源，用于跟踪服务状态。基于端点的响应，可以采取行动。例如，如果其中一个端点响应 503 错误代码，则向紧急情况管理器发送 SMS 通知。
*   负载平衡器:负载平衡器或反向代理可以利用健康检查端点来包括或排除服务池中的节点或服务器。例如，以一个负载平衡器为例，它在运行给定服务的一组节点之间平衡负载。如果其中一个节点上的服务停止，负载平衡器将自动停止向该节点转发请求，因此最终用户不会受到影响。
*   容器集群:像`swarm`或`kubernetes`这样的容器集群解决方案也可以从服务健康检查端点获得一些好处。例如，`swarm`使用服务健康检查来区分健康的服务和不健康的服务，从而重启不健康的服务或根据服务配置采取其他措施。
*   软件部署:运行状况检查端点还可用于自动执行部署流程，并通过检查服务依赖关系并在部署前等待它们准备就绪，或者在新版本不正常的情况下回滚到旧的运行状况良好的服务版本，来实现零停机部署。一个很好的例子是为 Docker 服务推出新的更新。我们可以启动新容器，检查新容器的健康状况，只有在它们健康的情况下才继续替换过程，而不是一步到位地用新容器替换旧容器。
*   安装验证:健康检查端点对于调查用例也有很大的帮助，特别是如果这些端点不仅报告服务本身的健康状况，还报告其依赖项的健康状况。一个例子是一个依赖于`Redis`、`MySQL`和`RabbitMQ`的服务，它提供健康端点来报告服务和这些依赖之间的连接状态。

由于这些用例，几乎每个服务都需要实现健康检查端点。健康检查端点的实现非常简单，可以只是一个返回服务状态的静态请求。

然而，在几个服务中实现健康检查可能会引入一些可维护性问题，因为我们在所有受支持的服务中复制代码，如果我们决定更新健康端点(出于某种原因)，我们将需要对所有服务进行相同的更改。或者在新服务的情况下，我们需要花一些时间来为新服务实现健康检查和测试。

此外，特别是在多个团队致力于实现健康检查的情况下，我们可能会在所有支持的服务中出现不一致的健康检查端点。

因此，尝试将健康检查端点实现到软件库中，并将该库与所有支持的服务集成在一起是很有意义的。在 Rails/Rack 软件应用程序的情况下，这个任务可以通过构建一个 [rack 中间件](https://github.com/rack/rack)来轻松完成。web 请求首先由 web 服务器处理，如`puma`或`unicorn`。这些服务器将请求代理给应用服务器。请求将由集成的中间件处理，然后由 web 框架处理，如`Sinatra`或`Rails`。该特性有助于为健康检查提供快速响应，因为它不需要由 web 框架处理，并且可以由中间件层提供给客户端。

在本文中，我将描述构建 rack 中间件的尝试，该中间件为使用 Rack 的 Ruby 应用程序提供健康检查端点。

# 中间件特性

下面是我的 Rails 应用程序所需的健康检查中间件的需求列表:

*   中间件支持多个健康检查端点，这些端点覆盖了服务的健康和依赖性。
*   中间件是可配置的。
*   中间件的响应应该是`JSON`响应。

为了完成列表中的第一项，我决定支持以下健康检查端点:

*   `\health` —静态健康检查端点，可用于检查应用服务器的状态(是否运行)。该端点的响应应该是下面的`JSON`:

*   `/db_health` —该端点用于检查数据库连接的服务状态，并提供关于服务数据库的一些信息，例如数据库中有多少个表以及[延迟的作业](https://github.com/collectiveidea/delayed_job)信息。以下是预期的`JSON`响应:

*   `/tcp_dependencies_health` —该端点应该报告所有`TCP`依赖项的状态(或者`up`或者`down`)。依赖关系需要使用遵循以下命名约定的环境变量进行配置:`TCP_DEPENDENCY_##`。这些环境变量的示例如下所示:

```
# ${VAR_NAME}=${host_or_ip}:${port}
TCP_DEPENDENCY_00=mysql:6604
TCP_DEPENDENCY_01=redis:9089
```

下面是使用上述变量配置的服务的预期`JSON`响应:

*   `/http_dependencies_health` —此端点应报告所有`HTTP`依赖项的状态。此端点的响应将包括每个已配置依赖项的状态代码以及 HTTP 健康检查响应的正文。需要使用遵循以下命名约定的环境变量为每个服务配置 HTTP 依赖关系:`HTTP_DEPENDENCY_##`。这些环境变量的示例如下所示:

```
#${VAR_NAME}=http://${host_or_ip}:${port}/${health_endpoint}
HTTP_DEPENDENCY_00=[http://localhost:3000/health](http://localhost:3000/health)
HTTP_DEPENDENCY_01=[http://localhost:80/health](http://localhost:80/health)
HTTP_DEPENDENCY_02=[http://localhost:3090/health](http://localhost:80/health)
```

下面是使用上述变量配置的服务的预期`JSON`响应:

# 履行

我实现了上面的特性，并发布了一个 Rack middleware gem，它封装了所描述的健康检查端点。你可以在 [GitHub](https://github.com/wshihadeh/deep_health_check) 和 [RubyGems](https://rubygems.org/gems/deep_health_check) 上找到宝石。将 gem 与 Rack and Rails 应用程序集成和使用非常简单，只需执行以下步骤即可完成:

*   向 Rails 应用程序添加 gem 可以通过向 gem 文件添加以下代码行来完成:

```
gem 'deep_health_check'
```

*   通过将此行添加到`rails`的`application.rb`(添加到`rack`的应用程序类)来使用应用程序中的中间件:

```
config.middleware.insert_after Rails::Rack::Logger, DeepHealthCheck::MiddlewareHealthCheck
```

*   通过环境变量配置`TCP`和`HTTP`依赖关系。中间件可以为每个`TCP`和`HTTP`依赖项处理多达 100 个依赖项。`TCP`依赖项应该遵循以下格式:`${host_or_ip}:${port}`。`HTTP`依赖项应该是有效的 HTTP URL。以下是这些配置的一个示例:

```
TCP_DEPENDENCY_00=127.0.0.0:8080
TCP_DEPENDENCY_01=127.0.0.0:8080 HTTP_DEPENDENCY_00=http://127.0.0.0:8080/health
HTTP_DEPENDENCY_01=http://127.0.0.0:8080/health
```

上面的环境变量需要对正在运行的应用程序实例可用，这些配置通常可以存储在运行具有所有其他配置(如数据库 URL)的应用程序的服务器上。对于容器，这些环境变量可以在部署期间传递给容器。

# 保护运行状况检查端点

一些中间件支持的端点提供关于数据库或应用程序依赖关系的信息。这些信息不应该向公众公开，因为它可能会给应用程序带来安全风险，使其容易受到攻击。

因此，需要保护这些端点，以减少因暴露它们而带来的安全风险。这个任务可以简单地通过使用基本的 auth 凭证保护这些端点来实现(当然，还有其他选项)。[本页](https://github.com/wshihadeh/deep_health_check/blob/master/NGINX.md)提供了使用基本身份验证凭证完成此任务所需的所有必要步骤。

# 结论

运行状况检查端点可用于许多目的和使用情形，例如监控或零停机软件部署。大多数应用程序都需要执行健康检查。本文描述了一种为 Ruby/Rack 应用程序实现和集成健康检查端点的方法。