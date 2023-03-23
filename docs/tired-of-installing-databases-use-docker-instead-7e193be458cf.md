# 厌倦了安装数据库？请改用 Docker。

> 原文：<https://betterprogramming.pub/tired-of-installing-databases-use-docker-instead-7e193be458cf>

## 用一个 shell 命令运行数据库

![](img/1f95970e9093778f7b99eaf5b8717963.png)

由[乔希·拉科尔](https://unsplash.com/@joshrako?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果您曾经使用过数据库，您可能会同意安装和设置过程非常漫长，并且在您的数据库准备好进行开发之前，通常需要修复大量的错误。

你花在让你最喜欢的数据库工作上的所有时间可以更好地用于实际的软件开发，而不是浪费在调试上。

这就是码头工人来救援的地方。如果你一直生活在岩石下，Docker 是一个开源的容器化平台，它使开发人员能够将应用程序打包到易于使用的标准化容器中，并毫不费力地分发它们，而不用关心系统兼容性问题。

在本文中，我将向您展示如何使用 Docker 在任何机器上无缝地运行数据库实例，而不需要进行痛苦的设置，并提供一个简单的自动化脚本。

请注意，我将使用 PostgreSQL 数据库管理系统作为示例，因为这是我在开发中通常使用的系统，但是这些步骤和概念也适用于 MySQL 等其他数据库。

# 安装 Docker

第一步是在你的机器上安装 Docker。由于安装过程高度依赖于您的操作系统，我将把您重定向到官方下载页面，在那里您可以找到 macOS、Windows 和 Linux 的详细说明:

[](https://docs.docker.com/get-docker/) [## 获取码头工人

### 更新 Docker 桌面条款 Docker 桌面在大型企业(超过 250 名员工或…

docs.docker.com](https://docs.docker.com/get-docker/) 

安装完成后，您可以继续下一步。注意，当连接到 Docker 守护进程时，可能会出现一些错误。在这种情况下，Docker 进程可能没有运行，所以启动它。或者，您可能会得到一个关于缺少连接到 Docker 套接字的权限的错误。在这种情况下，您可能需要将您的用户添加到`docker`组。无论如何，最终的故障排除超出了本文的范围，所以让我们继续。

# 用 Docker 启动 Postgres

如果您希望使用不同的数据库，您可以根据自己的选择更改 Postgres。

从 Postgres 开始，您只需从 [Docker Hub](https://hub.docker.com/) 中拉出`postgres`图像。这就像在控制台中键入以下命令一样简单:

```
docker pull <image>
docker pull postgres
```

或者，您可以直接运行 Postgres 容器，如果图像还没有被缓存，Docker 会自动为您提取图像:

```
docker run --rm -it -p <port mapping> [--name <optional container name>] -e POSTGRES_PASSWORD=<password> -e POSTGRES_USER=<user> [-e POSTGRES_DB=<optional database name>] <image>docker run --rm -it -p 5432:5432 --name "my_pg" -e POSTGRES_PASSWORD="postgres" -e POSTGRES_USER="postgres" -e POSTGRES_DB="my_db" postgres
```

为了更好地理解这个命令:

*   `--rm`退出时自动移除容器，省略保存容器。
*   `-it`允许你从控制台与容器交互。
*   `-p`指定端口映射(`5432`是默认的 Postgres 端口)。
*   `--name`指定容器名称，对以后的管理有用。或者，Docker 会分配一个随机的名称。
*   `-e`指定一个环境变量，在本例中是数据库用户和密码。

如果您希望使用不同于最新版本的 Postgres，您可以在映像名称后指定它，如下所示:

```
<image>:<version>
postgres:13
postgres:latest
```

# 管理数据库

此时，您已经有了一个可以连接的正在运行的 Postgres 实例。现在，您可以使用以下命令将 bash 控制台连接到正在运行的容器:

```
docker exec -it <container id> bash
```

或者:

```
docker exec -it <container name> bash
```

要获取容器 id 或名称，您可以使用以下内容列出所有活动容器:

```
docker ps
```

要停止容器，可以在启动 Postgres 实例的交互终端中按下`Ctrl+C`。或者，您可以运行以下命令:

```
docker stop <contaier id>
```

# 进一步自动化

到目前为止，您已经具备了在 Docker 容器中开始使用开发数据库服务器所需的一切。但是，您可以使用一个简单的数据库设置脚本来创建最终的表、索引和其他结构，从而节省更多的时间。

下面是一个非常简单的 bash 脚本，它首先启动包含您选择的图像的 Docker 容器，并创建一个具有指定名称的数据库(如果它还不存在的话)。然后，它尝试连接到正在运行的实例，并应用指定的 SQL setup 命令。

这是 bash 中的一个数据库设置脚本模板。然而，为了更好地控制容器和数据库实例，您可能想看看 [Dockerfile](https://docs.docker.com/engine/reference/builder/) 或 [Docker Compose](https://docs.docker.com/compose/) 。

# 最后的话

您应该考虑使用 Docker 来运行数据库服务器的容器化实例，而不需要直接在系统上安装任何东西，而不必经历设置新的开发数据库的痛苦。

就像创建容器一样简单，您可以使用以下命令删除它们:

```
docker ps -a # list the containers
docker rm <container>
```

或者通过以下方式删除图像:

```
docker images # list the images
docker image rm <image>
```

而不用担心删除数据库后可能会在系统上留下任何垃圾。

顺便说一下，您也可以考虑在数据库服务器旁边的 Docker 容器中运行其他应用程序。此外，您甚至可以选择将您的整个开发环境容器化，让不同的、专门的容器已经打包了您开始开发所需的一切，而无需安装任何额外的软件或担心依赖性和冲突。

有关在 Docker 容器中开发的更多信息，请查看 Visual Studio 代码指南。

> 你的时间是最有价值的资产，你永远也不会嫌不够。不要让时间白白流逝。

我希望你喜欢这篇文章。如果你有什么要补充的，请在评论中分享你的想法。感谢阅读！

如果您对其他有用的工具感兴趣，以节省开发人员的时间，您应该看看下面的故事:

[](/10-awesome-free-productivity-tools-and-websites-for-developers-47310707fda2) [## 10 个非常棒的免费开发工具和网站

### 检测内存错误，创建令人惊叹的用户界面，等等

better 编程. pub](/10-awesome-free-productivity-tools-and-websites-for-developers-47310707fda2)