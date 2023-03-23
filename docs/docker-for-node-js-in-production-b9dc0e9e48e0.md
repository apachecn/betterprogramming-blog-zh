# 生产中 Node.js 的 Docker

> 原文：<https://betterprogramming.pub/docker-for-node-js-in-production-b9dc0e9e48e0>

## 在生产环境中使用 Docker 运行 Node.js 应用程序的清单

![](img/23d1d478a3774c7fb575aa0519cd6300.png)

小阿米尔在 [Unsplash](https://unsplash.com/s/photos/container?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 1.选择正确的基础图像

为 Node.js 应用程序选择正确的基本 Docker 映像至关重要。你应该总是尝试使用[官方 Docker 图片](https://hub.docker.com/_/node)——因为它们拥有优秀的文档，使用最佳实践，并且是为最常见的用例设计的。

看官方的 [Node.js Docker 图片](https://hub.docker.com/_/node)还是有很多图片可以选择的。我总是选择可以运行我的 Node.js 应用程序的最小尺寸的图像。目前，如果您想在 64 位 Linux 上运行 Node.js 应用程序，您有以下选择:

*   节点:拉伸(最新)→ **344.36 MB**
*   节点:stretch-slim → **65.29 MB**
*   节点:lts-buster-slim→**57.57 MB**
*   节点:lts-buster → **321.14 MB**
*   节点:13 . 5 . 0-alpine 3.11→**37.35 MB**

[负责维护 Node.js 映像的 Node.js Docker 团队](https://github.com/nodejs/docker-node)基于 [Debian](https://en.wikipedia.org/wiki/Debian_version_history) 的 stretch 和 buster 映像。Docker 团队将 alpine 映像基于 Alpine Linux 发行版，这是一个带有强化内核的最小 Linux 发行版。我的建议是，如果你的应用程序在 Alpine 上工作，你应该使用 Alpine 图像作为你的基础图像，因为这可能会导致最小的图像大小。

# 2.使用非根容器用户

默认情况下，您的应用程序以 root 用户身份在容器内运行。但是，容器中的根与主机上的根不同。Docker 容器中的用户仍然受到限制。但是为了进一步减少安全攻击，您可能希望尽可能以无特权用户的身份运行容器。

大多数官方图像已经在其 Docker 图像中创建了一个非 root 用户。例如，参见 [Alpine Dockerfile](https://github.com/nodejs/docker-node/blob/ab3d54cef9f236ed9792aa4b786215db9ee7c1b6/12/alpine3.11/Dockerfile) ，它已经创建了一个名为 *node* 的组和一个名为 *node* 的用户供您使用。如果您的 Docker 文件基于 Alpine，您可以使用这个节点用户作为非根用户运行您的应用程序。有关使用节点用户的示例，请参见下面的 docker 文件。

以非 root 用户身份运行 Node.js 应用程序

在第一行中，我使用 node:12-alpine 图像作为基础图像。第 3 行的语句为我的应用程序创建了目录。我将这个新目录的所有者显式地设置为节点用户，因为我仍然运行 root 用户。注意，`WORKDIR`指令也创建文件夹，但是没有办法设置目录的所有者。`WORKDIR`指令设置工作目录。

我在第 9 行将用户设置为节点用户。`USER`指令在运行映像时设置用户名(或 UID)。Dockerfile 设置好用户后，我执行`NPM install`来安装我的 app 的依赖项。该命令将以节点用户的身份运行。在第 13 和 14 行，我将应用程序的源代码复制到工作文件夹中。在最后一行，应用程序开始使用`CMD`指令。`CMD`指令提供执行容器的缺省值。

# 3.启动和停止 Node.js 应用程序

在 Docker 容器中以正确的方式启动和停止 Node.js 应用程序至关重要。在前面显示的 Dockerfile 中，我使用`CMD`指令启动 Node.js 应用程序。当您使用`CMD`指令启动 Node.js 时，您要确保在您的应用程序内部，您可以接收来自操作系统的信号，比如`SIGINT`和`SIGTERM`，并处理它们以优雅地关闭您的应用程序。

如果你忽略这些信号并停止你的容器，Docker 将等待 10 秒(默认超时)让你的应用程序响应。如果在那个时候，你的应用程序没有响应，它会终止你的节点进程并停止容器。所以你要做的第一件事就是响应`SIGINT`和`SIGTERM`命令，优雅地关闭你的应用程序。

通过响应 SIGINT 和 SIGTERM 来优雅地停止 Node.js 应用程序

上面的代码示例展示了如何实现对`SIGINT`和`SIGTERM`信号的响应。在这个例子中，我通过调用`process.exit()`退出进程，但是这也是停止服务器和清理未完成连接的地方。

如果您无法访问源代码或不想更改应用程序的源代码，有两种不同的方法可以关闭您的应用程序。第一种是使用`--init`；这个标志向 Docker 表明应该使用一个 init 进程作为容器中的 PID 1。启动容器时，可以这样使用它:

`docker run --init -d yournodeappimage`

你的容器将直接对 Ctrl-C 或 Docker stop 命令做出反应。

另一个更持久的选择是将 [tini](https://github.com/krallin/tini) 添加到您的 docker 文件中，并将其包含在您的图像中。这也是你在使用`--init`标志的时候 Docker 在后台做的事情。您必须在您的映像中安装 tini，并使用`ENTRYPOINT`启动和包装`CMD` ——参见下面的示例。

带蒂尼的 Dockerfile

## 优雅地关闭未完成的 HTTP 连接

为了能够优雅地停止 Node.js 应用程序，最后要添加的是处理未完成的 HTTP 请求，并停止对新 HTTP 请求的响应。这将允许在使用 Docker Swarm 或 Kubernetes 之类的容器编排器时进行无停机更新。

代替开发你自己的，有一些库可以为你处理这些。我最常用的是[stop able](https://github.com/hunterloftis/stoppable)——这个库停止接受新的连接，并关闭现有的空闲连接(包括 keep-alive ),而不会终止正在进行的请求。

您必须用可停止的构造函数来包装 HTTP 服务器的创建，如下所示。

```
const server = stoppable(http.createServer(handler))
```

最后，如前所示，通过 Stoppable 的`server.close`关闭服务器来扩展关机功能。

使用 Stoppable 停止您的服务器

# 4.健康检查

Docker 文件中的`HEALTHCHECK`指令告诉 Docker 如何验证容器是否仍在工作。这可以检测到这样的情况，例如 web 服务器陷入无限循环，无法处理新的连接，即使服务器进程仍在运行。

您必须实现自己执行健康检查的功能，这是显而易见的，因为 Docker 不知道您的应用程序何时正常运行。我通常向我的服务器添加一个不同的路由，专门用于处理健康请求。

我添加了一个单独的小 Node.js 应用程序，它执行对健康端点的 GET 请求。这个小应用程序是由`HEALTHCHECK`指令使用的。

Node.js 应用程序来执行对健康端点的 GET 请求

在 Dockerfile 文件中，我使用这个小应用程序来执行健康检查。

`HEALTHCHECK --interval=21s --timeout=3 --start-period=10s CMD node healthcheck.js`

# 5.从 Node.js 应用程序记录日志

在 Docker 容器中运行时，从 Node.js 应用程序进行日志记录很简单:根据具体情况，登录到`stdout`或`stderr`。这背后的基本原理是让其他东西负责处理日志记录。让其他人负责日志记录是有意义的，因为 Docker 容器主要用在微服务架构中，在微服务架构中，责任分布在多个服务中。

我不建议直接从 Node.js 应用程序中使用`console.log`或`console.err`。相反，我会编写一个包装器或者使用一个低开销的日志框架，比如 [Pino](https://github.com/pinojs/pino) 。Pino 使用 JSON 记录到`stdout`和`stderr`，提供结构化日志记录。

# 6.使用环境变量的配置

我怀疑您已经使用了一个特定的配置对象，它为您的应用程序构造并集中了所有的配置选项，如下所示:

这样一个对象简洁地定义并集中了应用程序的所有配置选项。这种方法在 Docker 容器中运行时的问题是，所有的配置都要通过环境变量来完成。

我通常通过让环境变量覆盖配置对象`httpPort: process.env.HTTP_PORT || 3000`的默认值来解决这个问题。例如: