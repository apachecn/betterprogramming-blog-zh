# 使用 Docker 和 Traefik 部署任何 SSL 安全网站

> 原文：<https://betterprogramming.pub/deploy-any-ssl-secured-website-with-docker-and-traefik-27fbeb1343d3>

## 了解如何使用 Docker 和 Traefik 部署任何 SSL 安全的网站-包括所有文件！

![](img/6a01f5a8b94c6087f6615f6b37c6d8fd.png)

由[阿里安·达尔维什](https://unsplash.com/@arianismmm?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/developer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# Docker 是什么？

Docker 是一个用于开发、发布和运行各种应用程序的平台。它使应用程序与基础设施分离开来，这样就可以交付软件，而不用担心主机或最终用户的操作系统。

要在你的机器上安装 docker，请从 Docker 官方页面下载安装包:[https://docs.docker.com/get-docker/](https://docs.docker.com/get-docker/)

要使用 Docker，您应该熟悉以下关键词/概念:**容器、Docker 文件、Docker 图像、Docker 合成**

## 容器

容器描述了一个软件单元，它合并了运行应用程序的代码和每个依赖项。它们提供了在多种不同设备上运行应用程序的快速安全的方法。此外，由于不同的隔离能力，它保护了应用程序。

## Docker 图像

Docker 映像是一个只读模板，用于创建在 Docker 平台中运行应用程序的容器。它打包应用程序和预配置的服务器环境，以创建将要部署的容器。在官方 [DockerHub](https://hub.docker.com) 上，有几个已经配置好的图像可供使用。您可以使用搜索功能查找任何映像，并通过以下方式将其部署到您的系统上:

```
docker run -it "NAME_OF_IMAGE_FROM_DOCKERHUB"
```

## 码头文件

Dockerfile 是一个纯文本文档，包含设置图像的每个命令。它包含要使用的基本映像(操作系统)和应该安装在生成的映像上的附加软件。

每当定制内容用于 Docker 容器时，都必须创建 Docker 文件并用缺少的信息填充。

例如，下面两个片段将演示 Dockerfile 文件的用法。第一个将扩展一个 NGINX 图像来服务一个简单的 HTML 文件，如第二个代码片段所示。

如果您将两个文件放在同一个文件夹中，您可以使用以下命令运行它:

```
docker build -t nginx-sample .
docker run --name test-nginx -d -p 8080:80 nginx-sample
```

现在打开你最喜欢的网络浏览器，点击`http://localhost:8080`(或者如果你在服务器上工作，点击`http://your-ip:8080`)

## Docker 撰写

Docker Compose 是一个工具，通过使用一个可以包含几个 Docker 容器的 YAML 文件，以一种简单的方式创建多容器应用程序。

与简单的 over 文件相比，它有一个很大的优势，那就是它允许对整个应用程序堆栈使用一个简单的“堆栈”文件。您可以在存储库中保存一个合成文件，并使用一个简单的命令来部署所有内容:`docker-compose up -d`

按照官方教程安装 Docker Compose:【https://docs.docker.com/compose/install/ 

之后，阅读[官方 Docker 编写指南](https://docs.docker.com/get-started/08_using_compose/)来完全理解 Docker 编写文件是如何创建的，因为这不是本文的一部分。

# 什么是 Traefik？

> ***Traefik 让网络变得无趣
> 云原生网络堆栈刚刚工作。***

Traefik 用于将传入的请求转发给 Docker 环境中部署的服务。此外，它还能够为由 *traefik* 管理的每个域自动创建[让我们加密](https://letsencrypt.org/) SSL 证书。

要在您的 Docker 环境中设置本地 Traefik 服务，您可以[下载这个 Docker 合成文件](https://gist.github.com/paulknulst/68e5e63badaa6a9ac80b4227ca07baee#file-docker-compose-traefik-yml)并执行以下命令:

1.  创建用于 Traefik 的外部网络

```
docker create network traefik-public
```

2.导出所需变量

```
export PRIMARY_DOMAIN=yourdomain.de
export TRAEFIK_SSLEMAIL=youremai@yourdomain.de
```

3.部署

```
docker-compose up -d
```

要访问 traefik 仪表板，您可以点击`[https://dashboard.yourdomain.de](https://dashboard.yourdomain.de)`并使用登录

```
username: devadmin 
password: devto
```

**要更深入地了解 Traefik，您可以阅读本文** **中关于** [**如何部署它的内容。**](https://levelup.gitconnected.com/how-to-setup-traefik-v2-with-automatic-lets-encrypt-certificate-resolver-83de0ed0f542)

# 设置和部署您的网站

## 创建一个简单的网站

在您学习了基础知识并设置了 Traefik 实例之后，您可以开始创建 Docker 文件和 Compose 文件来为您的网站创建 Docker 容器(服务)。

为了简化这个过程，我将部署一个简单的仪表板项目，这个项目是我从 GitHub 上的一个很棒的列表中获得的。要获得仪表板副本，可以[在这里下载最新的 zip 文件](https://github.com/paulknulst/personal-dashboard/archive/refs/tags/v1.0.0.zip)或者克隆存储库:

```
git clone git@github.com:paulknulst/personal-dashboard.git
```

*与原来的存储库相比，我已经调整了 index.html 和 CSS 文件，使之成为一种 SEO 优化，每行有 5 个项目，而不是 6 个。*

创建一个新文件夹(personal-dashboard)并将项目文件提取(或移动)到名为`html`的子文件夹中。现在，您应该在您最喜欢的 IDE 中打开它，将 **config.sample.json** 复制到 **config.json** 并打开它。

改变它，以满足您的需求，并提供您自己的个人链接。我将使用以下设置:

## 创建 Dockerfile 文件

Dockerfile 将在 simple-dashboard 文件夹中创建，仅包含基本图像和一个将 HTML 文件夹添加到容器中的复制命令。

## 创建合成文件

合成文件也将在简单仪表板文件夹中创建。它包含了在 Docker 环境中设置个人仪表板的所有设置。此外，它将使用 Traefik 来启用加密证书生成。

完整 Docker 编写文件将在后面解释:

在这个合成文件中，您可以看到我们在第 5 行(personal-dashboard:v1)定义了一个 ***图像*** 。此外，我们添加了一个 ***构建*** 部分，因为该映像并不正式存在。在 build 部分中，我们定义了位于同一目录中的 Dockerfile 应该用于创建映像。

***网络*** 部分(第 9–10 行)包含 Traefik 安装期间创建的外部网络。此外，在第 33–35 行中，我们特别将网络定义为这个组合服务的外部网络。

另一个非常重要的部分是 ***healthcheck*** (第 11–16 行)。对我来说，这个部分应该包含在每个 Docker 编写文件中，因为它有助于了解 Docker 容器是否正确运行，没有任何运行时错误。关于进一步的阅读，请看这篇文章，它解释了如何实现不同类型的健康检查。

这个合成文件最重要的部分是 ***标签*** 部分。此部分包含几个对 Traefik 很重要的标签。首先，我们为这个服务启用 Traefik(第 18 行),因为在我们的 Traefik 服务中，有一个选项可以不为每个 Docker 服务工作。在第 19 行中，我们定义了哪个外部网络用于 traefik。

在第 20 行，我们设置了这个服务的 ***约束标签*** 。通常这是一个可选设置，只有在 Docker 环境中部署了多个 Traefik 实例时才使用。

从第 21–25 行开始，我们为 HTTP 和 HTTPS 定义了服务的 URL。对于 HTTP 部分，我们还从 Traefik 配置中激活了一个中间件，它总是将 HTTP 请求重定向到 HTTPS(第 23 行)。

第 26 行和第 27 行的标签是为这个 Docker 服务创建自动 SSL 证书所必需的。

需要第 28 行来向 Traefik 实例“公开”内部 Docker 服务端口。对第 21/24 行中定义的 URL 的所有传入请求将被转发到该标签中提到的端口上的 Docker 服务。

第 29 行被注释掉，因为它是我个人在 Docker 环境中使用的一个额外的中间件。我没有在 Traefik 安装中包含这个中间件，但是如果你想了解它，可以看看这篇文章。

请记住，只有按照上述步骤设置 Traefik，该文件才能正常工作。如果没有，您必须调整`PRIMARY_DOMAIN`、您的 *Docker 外部网络、*和用于 Traefik 的入口点(`http`、`https`)。

## 部署网站

现在，您已经创建了所需的文件，您可以打开 CLI，切换到您的项目文件夹，并通过执行以下命令开始部署过程:

```
docker-compose up -d --build
```

部署完成后，您可以在浏览器中打开您的控制面板，如果您遵循本教程，它应该如下所示:

![](img/479f4447e8c9b55ae6d35285d586bd2c.png)

在本教程中创建的个人仪表板的屏幕截图

# 结束语

我希望您喜欢阅读我的教程，现在能够在 Docker 容器中部署自己的网站，并使用 Traefik 和 SSL 证书增强它。

请记住，您可以用其他静态网站替换 HTML 文件夹。此外，您可以使用其他服务(如网站数据库)来扩展合成文件。此外，您可以从 Docker Hub 与另一个公共图像交换图像。

为了帮助您，我编写了另一个教程，介绍如何部署一个 Kimai2 Timetracking 网站，其中也包括 Docker Hub 映像、Traefik 设置、数据库和 NGINX。

[](https://medium.com/geekculture/how-to-setup-kimai2-timetracking-locally-or-on-your-server-with-docker-compose-1287d9bc3722) [## 如何使用 Docker Compose 在本地或服务器上设置 Kimai2 时间跟踪

### 使用 Docker 或 Docker Swarm 模式在本地或您的服务器上设置一个最好的时间跟踪工具，只需大约 5 分钟…

medium.com](https://medium.com/geekculture/how-to-setup-kimai2-timetracking-locally-or-on-your-server-with-docker-compose-1287d9bc3722) 

```
**Want to Connect?**If interested feel free to connect with me on [Twitter](https://www.twitter.com/paulknulst), [LinkedIn](https://www.linkedin.com/in/paulknulst/), and [GitHub](https://github.com/paulknulst)!
```

*本文最初发表于我的博客*[*https://www . paulsblog . dev/deploy-any-SSL-secured-website-with-docker-and-traefik/*](https://www.paulsblog.dev/deploy-any-ssl-secured-website-with-docker-and-traefik/)