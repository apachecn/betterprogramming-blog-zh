# 如何在 macOS 上的 Docker 容器中使用持久化本地数据运行 PostgreSQL

> 原文：<https://betterprogramming.pub/how-to-run-postgresql-in-a-docker-container-on-macos-with-persistent-local-data-440e7172821e>

## *设置 PostgreSQL 并跨容器在本地主机上保存数据的分步指南*

![](img/c8378b74faea2b023adcee658b2639f9.png)

照片由 [Linus Mimietz](https://unsplash.com/@linusmimietz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

我回来了。在漫长而疲惫的 2020 年之后，一年多没有写作，我回来了。

我已经启动了一个新的软件创业公司,并且我正在着手开发一个我希望会成功的新产品。在这个过程中，我将学习新的东西，尝试不同的技术和不同的做事方式，并在此分享它们。

我有 LAMP stack 开发的背景，多年来一直依赖 MySQL 作为我的应用程序数据的基础。在过去的一年里，我探索了转向 PostgreSQL(又名 Postgres)的好处。作为其中的一部分，我得到了一些非常简单的指令，用持久数据在 Docker 容器中设置 Postgres，这样在容器启动、停止、重启等过程中不会丢失任何东西。

我们走吧！

# **下载并安装 Docker**

如果你的 Mac 上还没有安装 Docker，[从稳定渠道安装最新版本](https://docs.docker.com/docker-for-mac/install/)。

# **创建您的 PostgreSQL 数据文件夹**

在 Mac 上创建一个本地目录来存储您的数据。我们会将 Docker 容器绑定到这个本地文件夹，这样，无论您何时关闭容器(或重启计算机)并再次启动它，您的数据都会持久保存。

*注意:我的个人偏好是将我的开发项目收集到一个名为* `*Develop*` *的文件夹中。你可以根据自己的喜好随意调整位置。*

打开终端(或使用 Finder)并创建以下文件夹:

```
mkdir /Users/[your_username]/Developmkdir /Users/[your_username]/Develop/postgres_datamkdir /Users/[your_username]/Develop/postgres_data/13.2
```

*注意:您正在创建一个* `*/13.2*` *版本文件夹，因为这是我们将在本文中安装的版本。如果需要的话，你可以简单地添加一个* `*/12.0*` *文件夹，并让两个容器运行不同版本的 PostgreSQL。在这种情况下，您只需将每个容器绑定到适当的本地文件夹。*

# 建立一个本地码头网络

Docker 内置了 DNS。因此，我们将为 Docker 容器创建一个本地网络。如果您遵循了我关于为您的开发环境创建 Docker 容器的其他文章，那么只要所有的容器都被添加到同一个网络中，那么您的所有容器都将能够通过名称而不是 IP 地址相互通信。我的例子中的所有容器都是同一个网络的一部分。

您可以随意命名您的网络。由于我们正在构建一个开发环境，我将把我的命名为 *dev-network* 。

打开终端并输入以下命令:

```
docker network create dev-network
```

# 创建 PostgreSQL 容器

现在，从 DockerHub 上的 PostgreSQL 13.2 官方存储库中创建一个映像和容器。在下面的命令中，记住用您的 Mac 用户名替换`[your_username]`，并用您最喜欢的数据库根密码替换`[your_password]`。

*注意:以下是一条由于长度原因而换行的命令。确保复制并粘贴整个内容。*

```
docker run --restart always --name postgres13.2 --net dev-network -v /Users/[your_username]/Develop/postgres_data/13.2:/var/lib/postgresql/data -p 5432:5432 -d -e POSTGRES_PASSWORD=[your_password] postgres:13.2
```

以下是该命令中每个参数的含义:

*   `--restart always`会在 Docker 启动的任何时候重启这个容器，比如笔记本电脑重启，或者 Docker 关闭后又重新启动。如果您想每次都启动自己的容器，请不要使用这个参数。
*   `--name postgres13.2`将名称“postgres13.2”分配给容器实例。这是它在 Docker 仪表板中的显示方式。如果您计划在 Mac 上的容器中运行多个版本，在名称中添加版本号会很方便。
*   `--network dev-network`会将此容器加入您创建的本地码头网络。
*   `-v /Users/[your_username]/Develop/postgres_data/13.2:
    /var/lib/postgresql/data`会将容器卷(`/var/lib/postgresql`)中的数据文件夹绑定到您在上一步中在笔记本电脑上创建的本地文件夹，这样，如果容器出于任何原因关闭并重新启动，数据将会保留。
*   `-p 5432:5432`会将容器(5432)的 Postgres 端口绑定到 Mac 上的同一个端口。这使得在 Postgres 中将该容器视为“本地主机”变得更加容易。
*   `-d`将在分离模式下运行该容器，以便它在后台运行。
*   `-e POSTGRES_PASSWORD=[your_password]`设置一个环境变量(在本例中，是容器内的 PostgreSQL root 密码)。
*   `postgres:13.2`表示官方 DockerHub Postgres 版本标签 13.2 就是要安装的。如果你正在创建一个新的实例(例如`:12.0`)，只需将`:13.2`替换为不同的版本，并确保你已经在 Mac 上创建了一个匹配的数据文件夹，这样你就可以绑定到它。

除非您已经将 Postgres Docker 映像下载到您的笔记本电脑上，否则当您第一次运行上面的命令时，它会下载它。之后的后续运行会快很多。

您可以运行以下命令来查看您的 Docker 容器是否正在运行:

```
docker ps
```

您也可以打开 Docker 仪表板。它也会出现在那里。

就是这样！您的新 PostgreSQL 13.2 实例现在正在运行。

# 还有呢！

查看[我的其他文章](https://medium.com/@crmcmullen),找到更多在 Mac 上设置开发环境的有用指南！

此外，继续检查回来。我有一些关于 PHP API 开发的好主意，我将很快与大家分享。