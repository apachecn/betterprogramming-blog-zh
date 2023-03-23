# 如何修复基于 Docker 的微服务基础设施

> 原文：<https://betterprogramming.pub/how-to-fix-your-docker-based-microservice-infrastructure-17ba34bb7561>

## 每当出现故障时，针对 Docker API 启动命令来修复您的基础设施

![](img/c7c6e26403797850d224a152ce97ea00.png)

格伦·卡斯滕斯-彼得斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

[微服务架构风格](http://searchmicroservices.techtarget.com/definition/microservices)为开发团队提供了一种更加分散的构建软件的方法，每个服务都被独立地隔离、构建、部署和管理。因此，[容器](https://medium.com/swlh/what-exactly-is-docker-1dd62e1fde38)已经成为包装单个服务的事实上一致且资源高效的标准。

然后出现了编排工具，如 [Docker Swarm](https://docs.docker.com/engine/swarm/) 、 [Kubernetes](https://kubernetes.io/) 和 [Nomad](https://www.nomadproject.io/) 。这些工具有助于自动化扩展、部署和容器管理。

尽管由于这些突破，微服务管理的复杂性正在降低，但一些挑战仍然存在，如对此类基础设施的平稳轻松监控。在[上一篇文章](/distributed-instant-logs-made-easy-with-docker-b5e7f501f045)中，我们介绍了 [Logspout](https://github.com/gliderlabs/logspout) 和一个新的 [Slack adapter](https://github.com/kalisio/logspout-slack) 作为 Docker 容器的轻量级日志监控替代。在本文中，我们将介绍 [kontrol](https://github.com/kalisio/kontrol) ，它是 Docker 容器内置健康检查的轻量级替代方案，您可以快速配置和部署它。

# 一言以蔽之

kontrol 是一个非常轻量级的应用程序，可以作为 Docker 容器或 Docker Swarm 服务运行。它针对一组资源(容器或服务)运行请求，以确定这些资源是否正常运行(也称为健康检查)。每当健康检查失败时，它*针对 Docker API 发出命令*，以便*修复*基础设施。kontrol 可以监控和修复基于 Docker 的微服务基础设施。

kontrol 包括使用[传入 Webhook](https://api.slack.com/messaging/webhooks) 的[松弛](https://slack.com/)通知。只需将 webhook URL 插入到您的环境中，并启用 Slack 集成。无论何时运行状况检查失败，您在 Slack 上指定的通道都会收到一条消息。

# 为什么选择 kontrol？

虽然 Docker 为容器提供了内置的健康检查，但在某些用例中它可能太有限了。首先，如果您想在 Slack 上得到关于健康检查失败的通知，您将需要使用第三方工具。第二，Docker 的修复操作只包括重启出错的容器本身。在复杂的微服务场景中，这可能是不够的，因为一些容器依赖于其他容器才能到达。

例如，假设您有一个使用服务 B 的服务 A。从 Docker 的角度来看，服务 A 和 B 可以被视为运行良好，而服务 B 由于某种原因(例如，网络故障、超时等)无法从服务 A 访问。).在这种情况下，从 A 的角度来看，展示服务 A 中提供服务 B *状态的健康检查端点可能会很有趣。当服务 A 中的运行状况检查失败时，您应该会得到通知，并且除了服务 A 之外，服务 B 也可能会自动重新启动。*

# 配置 kontrol

大多数配置选项来自于 [got](https://github.com/sindresorhus/got) 和[docker ode](https://github.com/apocas/dockerode)powering kontrol。`config.js`中导出的对象应该是这样的结构:

*   `docker`:用于初始化 dockerode 的选项。
*   `jobs`:健康检查和通知/修复任务的映射，通过其键识别每个任务。
*   `cron`:[CRON 模式](https://github.com/kelektiv/node-cron)来调度它。
*   `delay`:调度任务前的延迟秒数。
*   `notify`:可选函数，当健康检查失败时使用`error`对象作为输入，或者当健康检查失败后恢复健康时使用 none 作为输入(返回发送通知的 Slack 消息有效载荷)。
*   `heal`:可选函数，以`docker`对象(即 dockerode 实例)和`_` (lodash 实例)作为输入。执行 Docker 命令来修复基础设施。
*   所有其他选项都被发送到运行状况检查请求的 get 实例。

以下是可用于自定义行为的环境变量:

*   `CONFIG_FILEPATH`:您的配置文件路径是`config.js`
*   `PORT`:服务器端口(默认为`8080`)
*   `SLACK_WEBHOOK_URL`:你的 Slack webhook 网址

默认的`[config.js](https://github.com/kalisio/kontrol/blob/master/config.js)`是一个很好的例子。它主要检查 kontrol 容器本身，并在失败时重启它，就像 Docker 所做的那样。在测试模式下，kontrol 容器将随机失败，并显示一个`500`状态代码，因此只要您不手动终止它，容器就会重新启动。要构建并启动该示例，请执行以下命令:

```
git clone [https://github.com/kalisio/kontrol.git](https://github.com/kalisio/kontrol.git)
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/xxx
docker-compose build
docker-compose up
```

# 控制微服务

如果您使用 Docker Swarm，这里有一个更完整的示例，作为 Docker 合成文件，以便在管理器上部署它来监控和修复您的服务:

在下面的配置示例中，我们将假设一个应用程序正在使用分布式服务 A 和 B。即使从 Docker 的角度来看服务 A 和 B 是健康的，我们也会使用专用的健康检查端点来测试从应用程序的角度来看它们是否也是可到达的。如果失败，将会发出通知，并且目标服务将会自动重新启动:

# 结论

我希望您会发现这个解决方案非常简单和有用。如果是这样，请立即在新的 kontrol [存储库](https://github.com/kalisio/kontrol)上打开问题或 PRs。

如果你对构建一个基于微服务的平台感兴趣，你可能想看看一些开源项目，比如:

*   Kaabah ，一个构建和运营 Docker 群基础设施的解决方案。
*   [Kargo](https://kalisio.github.io/kargo/) ，一个基于 Docker 的解决方案，用于在 Docker Swarm 基础设施上部署服务。

我在这篇文章中介绍的是 Kargo 内置监控栈的[部分。](https://kalisio.github.io/kargo/reference/environment.html#kontrol)