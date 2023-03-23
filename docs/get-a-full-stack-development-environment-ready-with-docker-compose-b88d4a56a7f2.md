# 使用 Docker Compose 准备好全栈开发环境

> 原文：<https://betterprogramming.pub/get-a-full-stack-development-environment-ready-with-docker-compose-b88d4a56a7f2>

## 开始在一个集装箱化的环境中开发您的新的全栈应用程序，以前很少或没有 Docker 经验

![](img/302d8403fac7567c91a96d36f1fa45fc.png)

由 [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/stack?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

本文将介绍如何设置开始开发全栈 web 应用程序所需的所有容器。

虽然在这里，我将使用特定的技术，但是在每个容器中运行的技术可以通过一些小的改变被您喜欢的技术所替代。

另一方面，请注意，本文将只涵盖开发的设置。本文中包含的所有 docker 文件都不是生产就绪的。

现在，让我们看看我们的基础架构将会是什么样子。为此，我将首先展示我们项目的文件结构:

```
**app**
├── docker-compose.yaml
├── api
│   ├── Dockerfile.dev
│   ├── go.mod
│   └── main.go
├── client
│   ├── Dockerfile.dev
│   └── *create-react-app files and directories*
└── nginx
    ├── default.conf
    └── Dockerfile.dev
```

在这个结构中，我们可以看到我们将使用 [Go lang](https://golang.org/) (和 web 框架 [Gin](https://github.com/gin-gonic/gin) )作为后端，使用 [React](https://reactjs.org/) 作为前端。

此外，我将添加另外两个服务，一个数据库和一个缓存。我们的 docker-compose 文件将包含一个 [Postgres](https://www.postgresql.org/) 容器，以及一个 [redis](https://redis.io/) 容器，以展示这些服务将如何集成到我们的应用程序中。

另一方面，我也决定使用一个 [NGINX](https://www.nginx.com/) 服务器。虽然这对于我们的开发环境来说不是必需的，因为我们可以使用本地机器作为开发服务器，但是这个服务器将在生产中消失，所以在我们的 docker-compose 构建中使用 NGINX 将使从这个开发构建过渡到生产就绪构建变得更加容易。

首先，我们将为我们的 React 客户机创建 docker 文件。为此，首先导航到项目的根目录(在本教程中，`app/`目录将扮演这个角色)并运行以下命令:

```
npx create-react-app client
```

一旦命令执行完毕，我们就可以让基本的 React 客户端就绪了。使用前面的命令启动它的唯一问题是，默认情况下，它会在`client/` 目录中创建一个 g it 存储库，所以请确保删除它*。*

除此之外，我们已经准备好创建我们的第一个 docker 文件，所以我们将把下面几行添加到一个名为`Dockerfile.dev`的文件中，该文件将位于客户端/目录*中。*

```
FROM node:alpineWORKDIR /appCOPY ./package.json ./RUN npm installCOPY . .CMD [“npm”,”run”, “start”]
```

虽然这是一个基本的 other 文件，但我将强调一个值得注意的最佳实践:为了避免每次更新文件时重新缓存`package.json`文件，我们将它的副本与其他文件分开。

这样，我们可以避免在每次构建时运行`npm install` 命令，这是构建 Docker 映像最耗时的步骤之一。

另一方面，在我们的构建中指定我们想要的图像版本通常是有趣的，因为它增强了我们的容器的一致性。

这一次，我选择了`node:alpine`映像，这是一个最小映像，也就是说，它包含运行 npm 的最小映像。

转到后端方面，我们先用 Go 的 [Gin](https://github.com/gin-gonic/gin) web 框架创建一个基础服务器。导航到`app/api`目录并创建一个文件`main.go` *:*

基本 Gin 服务器

我们还需要一个`go.mod`文件，它相当于 npm 中的`package.json`。

```
module github.com/<yourUsername>/<nameOfYourProject>go 1.13require github.com/gin-gonic/gin v1.5.0
```

一旦我们准备好了两个文件，我们就可以开始在 API 目录中编写我们的`Dockerfile.dev`文件:

```
FROM golang:latestENV GO111MODULE=onWORKDIR /appCOPY ./go.mod .RUN go mod downloadRUN go get github.com/pilu/freshCOPY . .CMD ["fresh"]
```

您可能已经注意到，在这个 other 文件中，我们遵循与另一个文件相同的最佳实践。

我们已经将`go.mod`副本从其他文件中分离出来，因为这将防止在没有添加新的依赖关系时执行`go mod download command` 。

另一方面，您可能已经注意到这个容器将要运行的默认命令是`fresh` 而不是`go run main.go` *。*

但是，如果您曾经使用过 Gin，您就会知道热重载不是这个框架中的默认特性。由于这是开发环境中的一个期望特性，我们将使用 [fresh](https://github.com/gravityblast/fresh) ，这是一个命令行工具，每次文件更新时它都会构建并重启一个 web 应用程序。

最后，进入 docker-compose 文件之前的唯一一步是设置 NGINX 服务器。首先创建一个`nginx/`目录，里面有一个名为`default.conf`的文件；

我们 NGINX 服务器的默认配置

简而言之，这个文件将告诉 NGINX 创建一个监听端口 80 的服务器，然后将请求重定向到它的一个上游服务器。

如果请求的端点以`/api/`，开始，NGINX 将代理把请求传递给我们的 API 服务器。

如果端点是任何其他端点，客户端-服务器将是获得请求的那个。另一条规则与 [WebSockets (SockJS)](https://github.com/sockjs/sockjs-node) 相关，但是最初，如果您决定跳过这条规则，也没什么大不了的。

NGINX 服务器的`Dockerfile.dev`将是最简单的:

```
**FROM** nginx**COPY** ./default.conf /etc/nginx/conf.d/default.conf
```

现在我们已经完成了所有的 Docker 文件，我们可以进入最后一步:Docker 组合配置。

Docker Compose 是一个用于定义和运行多容器 Docker 应用程序的工具。

在这个文件中，我们定义了用来构建容器的 docker 文件、它们必须公开的端口、它们必须定义的卷等等。

尽管这个文件乍一看似乎有点吓人，但它实际上是您可能已经熟悉的所有工具的汇编:

Docker 撰写

我想解决的第一件事是我们可以分配给 Docker 容器的不同重启策略。

有 4 种策略:*否，始终，故障时，除非停止*。请注意，我已经将 *always* 分配给 NGINX 服务器，将 *on-failure* 分配给 API 服务器，但是实际上，除非您有理由重新启动另一个服务，否则我们的应用程序中唯一感兴趣的服务是 NGINX 服务器的服务。

因为我们的 NGINX 服务器是将流量重定向到其他容器的服务器，所以当它关闭时，任何其他服务都无法工作。

此外，我已经为所有容器添加了端口映射。虽然我选择的端口大多是标准端口，但是它们也可以被更改为您喜欢的任何其他端口。

另一方面，这里添加的卷满足以下两种需求之一:它们要么支持热重装(API 和客户机容器)，要么允许数据持久性(redis 和 Postgres 容器)。

最后，使用 Docker Compose 向容器传递环境变量相当容易。

正如我们所观察到的，可以只在 docker-compose 文件中添加变量本身，或者更好的是，创建一个. env 文件来存储变量，尤其是那些不应该共享的变量(密码、密钥……)。

一旦您完成了所有需要的文件，就该运行您的项目并测试一切都正常工作了。为此，只需在根目录下运行以下命令:

```
docker-compose up --build
```

您可以通过运行以下命令来查看容器是否已成功创建:

```
docker-compose ps
```

完成开发后，您可以通过运行以下命令立即停止所有容器:

```
docker-compose down
```