# 使用 Docker Compose 开发 Local Node.js 的 9 个技巧

> 原文：<https://betterprogramming.pub/9-tips-for-local-node-js-development-using-docker-compose-19789f44158>

## 创造良好的地方发展环境

![](img/c0d709cba6af7d2b350546d1aaba9414.png)

里卡多·戈麦斯·安吉尔在 [Unsplash](https://unsplash.com/s/photos/composition?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

Docker Compose 为设计和开发容器解决方案提供了一个很好的本地开发设置。无论您是测试人员、开发人员还是 DevOps 操作员，Docker Compose 都能满足您的需求。

如果你想使用 Docker Compose 为 Node.js 创建一个优秀的本地开发和测试环境，我有以下 10 个技巧。

# 1.在 Docker 合成文件中使用正确的版本

`docker-compose.yml`文件是一个 YAML 文件，它为 Docker 应用程序定义了服务、网络和卷。文件的第一行包含`version`关键字，并告诉 Docker Compose 您正在使用哪个版本的文件格式。

有两个主要版本可以使用，版本 2 和版本 3；两者都有不同的用例。

Docker Compose 开发团队为本地开发创建了第 2 版，并创建了第 3 版以兼容容器编排器，如 [Swarm](http://swarmframework.org/) 和 Kubernetes。

当我们谈论本地 Node.js 开发时，我总是使用最新的版本 2，在我写作的时候，版本 2.4。

```
version: **"2.4"**
services:
  web: 
```

# 2.正确使用装订架

关于绑定挂载，我的第一个技巧是始终使用相对路径从主机挂载 Node.js 源代码。

使用相对路径允许其他开发人员使用这个合成文件，即使他们在主机上有不同的文件夹结构。

```
volumes:
  - **./src**:/home/nodeapp/src
```

## 使用命名卷装载数据库

几乎所有 Node.js 应用程序都是使用 Linux 容器部署到生产环境中的。如果你使用 Linux 容器并在 Windows 或 Mac 上开发你的应用程序，你不应该绑定安装你的数据库文件。

在这种情况下，数据库服务器在读写数据库时必须跨越操作系统边界。相反，您应该使用命名卷，并让 Docker 处理数据库文件。

使用命名卷挂载 MongoDB 数据库

`volumes:`关键字定义了 docker-compose 文件的命名卷。这里，我们定义了命名卷`workflowdatabase`，并在`workflowdb`服务中使用它。

## 使用委派配置提高性能

我总是将*委托*配置添加到我的卷挂载中，以提高性能。通过在绑定挂载上使用委托配置，您告诉 Docker 它可能会延迟来自容器的更新在主机中出现。

通常，对于本地开发，在容器中执行的写入不需要立即反映在主机上。委派标志是 Docker Desktop for Mac 特有的选项。

```
volumes:
  - ./src:/home/app/src:**delegated**
```

根据您需要的容器和主机之间的一致性级别，还有另外两个选项需要考虑，`[consistent](https://docs.docker.com/docker-for-mac/osxfs-caching/)`和`cached`。

# 3.正确处理你的节点模块

由于操作系统的不同，你不能将`node_modules`目录从你的 macOS 或 Windows 主机绑定挂载到你的容器中。

一些 npm 模块在 npm 安装期间执行动态编译，这些从 macOS 动态编译的模块不会在 Linux 上运行。

有两种不同的解决方案可以解决这个问题:

1.  通过 Docker 容器填充主机上的`node_module`目录。

您可以通过使用`docker-compose run`命令运行`npm install`来通过 Docker 容器填充主机上的`node_module`目录。这将使用容器的操作安装正确的 node_modules。

比如一个标准的 Node.js app，有下面的`Dockerfile`和`docker-compose.yml`文件。：

Node.js 应用程序的标准 docker 文件

标准 Docker-Compose.yml 文件

通过执行命令`docker-compose run workflowengine npm install`，我通过运行 Docker 容器在主机上安装 node_modules。

这意味着主机上的 node_modules 现在用于 Dockerfile 的架构和操作系统，并且*不能再从您的主机上使用。*

2.使用空绑定挂载隐藏主机的 node_modules。

第二个解决方案比第一个更灵活，因为您仍然可以像在 Docker 容器中一样从主机上运行和开发应用程序。这就是所谓的 node_modules *卷技巧*。

我们必须更改 docker 文件，以便 node_modules 安装在比 Node.js 应用程序高一个目录的位置。

`package.json`被复制并安装在`/node`目录中，而应用程序被安装在`/node/app`目录中。Node.js 应用程序从当前应用程序文件夹向上查找`node_modules`目录。

来自主机的 node_modules 与应用程序源代码位于同一个文件夹中。

为了确保来自主机的 node_modules 不会将挂载绑定到 Docker 映像中，我们使用这个`docker-compose`文件挂载一个空卷。

volumes 部分中的第二条语句实际上对主机隐藏了 node_modules 目录。

# 4.使用 Docker 合成工具

如果你想在用 Docker Compose 开发时运行你的工具，你有两个选择:使用`docker-compose run`或者使用`docker-compose exe`。两者表现不同。

`docker-compose run [service] [command]`从服务的映像启动一个新容器并运行命令。

`docker-compose exec [service] [command]`在该服务的当前运行容器中运行命令。

# 5.使用 nodemon 进行文件监视

我总是使用`[nodemon](https://www.npmjs.com/package/nodemon)`来查看文件更改和重启 Node.js。当您使用 Docker Compose 进行开发时，您可以通过以下 Compose run 命令安装`nodemon`来使用`nodemon`:

```
docker-compose run workflowengine npm install nodemon —-save-dep
```

然后在`docker-compose.yml`文件中的`workflowengine`服务下添加`command`。您还必须将`NODE_ENV`设置为 development，以便安装 dev 依赖项。

# 6.指定服务的启动顺序

Docker Compose 在启动其服务时不使用特定的顺序。如果您的服务需要特定的启动顺序，您可以在 docker-compose 文件中使用`depends_on`关键字来指定。

使用`depends_on`您可以指定您的服务 A 依赖于服务 B。Docker Compose 在服务 A 之前启动服务 B，并确保在启动服务 A 之前可以通过 DNS 访问服务 B。

如果您使用的是 Docker Compose YAML 的第 2 版，`depend_on`可以与`HEALTHCHECK`命令结合使用，以确保您所依赖的服务已经启动并且运行正常。

# 7.健康检查与 depends_on 结合使用

如果您希望您的服务在您所依赖的服务已经启动并且健康之后启动，您必须将`depends on`与健康检查结合起来。

下面的例子来自上一篇文章， [*迷你视频编码器第 1 部分*](https://itnext.io/mini-video-encoder-implementation-part-1-1fce5fb081c1) ，其中我展示了如何使用 [MongoDB](https://www.googleadservices.com/pagead/aclk?sa=L&ai=DChcSEwiR1JPD2ZXnAhVKiI8KHbY1AYcYABAAGgJzYg&ohost=www.google.com&cid=CAESQOD2wdx_BER9wND2sFdQQXRBJBPKMSdnx1aeaQPgkbn_1mXJ3ad6tg70BNQDfPchaHlH8rii8t0Ek-Q0cXH0WcM&sig=AOD64_3PMrAoKeHLeQx3adsyj1D_Pph2bA&q=&ved=2ahUKEwjClo3D2ZXnAhVExTgGHQv8C24Q0Qx6BAgNEAE&adurl=) 和 [Fastify](https://www.fastify.io/) 。

将 depends_on 与健康检查相结合

您必须将`condition: service_healthy`添加到`depends_on`中，以表明您所依赖的服务在启动该服务之前应该是健康的。

为 MongoDB 数据库指定的健康检查确保数据库服务器在报告健康状态之前已经启动并正在接受连接。

# 8.使用扩展字段缩小撰写文件

您可以使用环境变量和扩展字段增加合成文件的灵活性。可以使用`environment`关键字设置环境变量。

例如，更改 API 正在监听的数据库或端口的连接字符串。请参阅我的文章[*Node.js with Docker in production*](https://medium.com/better-programming/docker-for-node-js-in-production-b9dc0e9e48e0)了解如何在 node . js 应用程序中配置和使用环境变量。

扩展字段允许您在撰写文件中定义一个文本块，该文本块可以在同一个文件中重复使用。通过这种方式，您可以减小合成文件的大小，使其更加干燥。

我使用语法`<<: *base-service-template`定义了一个包含`build`和`networks`的模板，它们在每个服务上都是相同的。我将定义的模板注入到服务定义中。

# 9.添加反向代理服务

一旦在组合文件中定义了多个公开 HTTP 端点的服务，就应该开始使用反向代理。您可以开始执行主机头路由，而不必管理 HTTP 端点的所有端口和端口映射。

您可以使用 DNS 名称在不同的服务之间进行路由，而不是使用不同的端口。容器解决方案中最常用的反向代理是 [NGINX](https://github.com/jwilder/nginx-proxy) 、 [HAProxy](https://hub.docker.com/_/haproxy) 和 [Traefik](https://containo.us/traefik/) 。

## 使用 NGINX

如果你打算使用 NGINX，我推荐 Jason Wilder 的[jwilder/NGINX-proxy](https://github.com/jwilder/nginx-proxy)Docker 容器。Nginx-proxy 使用 [docker-gen](https://github.com/jwilder/docker-gen) 根据您的 Compose 文件中的服务生成 Nginx 配置模板。

每次您在撰写文件中添加或删除服务时，Nginx-proxy 都会重新生成模板并自动重启 Nginx。自动重新生成和重新启动意味着您总是拥有最新的反向代理配置，包括您的所有服务。

您可以通过将`VIRTUAL_HOST`环境变量添加到服务定义中来指定服务的 DNS 名称。

使用 jwilder/nginx-proxy 作为服务的反向代理

Nginx-proxy 服务安装 Docker 套接字，这使它能够响应容器的添加或删除。在`VIRTUAL_HOST`环境变量中，我使用了`*.localhost`域。

Chrome 自动将`.localhost`域名指向 127.0.0.1。

## 使用 Traefik

Traefik 是一个专门的开源反向代理容器映像，用于基于 HTTP 和 TCP 的应用程序。

在我们的 Docker Compose 中使用 Traefik 作为反向代理或多或少与 Nginx-proxy 相同。Traefik 提供了一个基于 HTTP 的仪表板，向您显示由 Traefik 处理的当前活动路由。

Traefik 使用`labels`而不是环境变量来定义您的 DNS 名称。参见上面的例子。

Traefik 提供的功能比上面显示的多得多，如果您感兴趣，我会引导您访问他们的[网站](https://docs.traefik.io/)，该网站提供了关于其他功能的完整文档，例如负载平衡、自动请求和更新[让我们加密](https://letsencrypt.org/)证书。

感谢您的阅读，希望这九个技巧对使用 Docker Compose 进行 Node.js 开发有所帮助。如果你有任何问题，请随时回复。