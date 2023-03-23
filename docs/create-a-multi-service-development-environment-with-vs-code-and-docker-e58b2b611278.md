# 用 VS 代码和 Docker 创建多服务开发环境

> 原文：<https://betterprogramming.pub/create-a-multi-service-development-environment-with-vs-code-and-docker-e58b2b611278>

## 在 Visual Studio 代码中设置多容器环境

![](img/b89e640c0ce7cc5b7737bdcb7c836fc0.png)

照片由 [Guillaume Bolduc](https://unsplash.com/@guibolduc?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

假设您想要开发一个由前端、API 和 DB 组成的应用程序。您可以在同一个 Docker 容器中实现这一点，但这会限制您将来扩展的可能性，也就是说，如果您的负载增加，您需要添加额外的 API 或多个数据库服务器。

对此的解决方案是在各自的容器中开发这些服务。Visual Studio Code(VS Code)让我们不仅可以启动我们的多个服务，还可以轻松连接到每个服务，并在不同的容器中使用 VS Code 作为我们的开发工具。

让我们来看看这是如何实现的。以[这篇文章为基础](https://medium.com/@bernd.bornhausen/setting-up-visual-studio-code-with-docker-as-a-development-environment-for-python-da640978cae3)，我们可以扩展相同的概念并构建我们的多容器环境。

假设我们的架构由以下三个服务组成:

1.  前端，基于 PyWebIO
2.  中间层，一个基于 fastApi 的 API
3.  后端，一个 Redis 实例

我的开发文件夹中的文件夹结构，在本例中命名为`multicontainersetup` ，如下所示:

```
C:\DEV\MULTICONTAINERSETUP
├───api
├───frontend
└───redis
```

为了让 VS 代码将不同的文件夹识别为“Remote-Folder ”,即应该在 Docker 容器中打开的文件夹，我们需要在每个子文件夹中创建一个`.devcontainer`文件夹。以前面提到的文章中描述的文件为基础，我们可以根据需要定制每个文件夹的配置。
由于前端和 API 将使用 Python 开发，我们可以为它们使用相同的`Dockerfile` 。

前端和 api 服务的 Dockerfile

该 docker 文件必须复制到`Frontend`的`.devcontainer`文件夹和`API` 文件夹中。

必须修改`devcontainer.json`文件来使用 Docker 合成文件，以便 VS 代码可以同时启动多个 Docker 容器。在我描述`devcontainer.json`文件之前，让我们快速浏览一下`docker-compose.yml`文件。
`docker-compose.yml`文件必须放在我们项目的根文件夹中，在这里是`multicontainersetup`。

除了我稍后将解释的`redis` 服务之外，每个服务都将遵循相同的模式，对于`docker-compose.yml`文件中服务部分的`frontend`,配置将如下所示:

```
frontend:image: frontendtty: truebuild:context: ./frontend/.devcontainerdockerfile: ./Dockerfilevolumes:
- .:/workspace:cacheddepends_on:- api
- redisserverports:- "8000:8000"networks:- myNetwork
```

仔细观察我们会发现，我们正在描述一个名为`frontend`的服务，这是上面代码中的第一行，它是基于我们的`frontend/.devcontainer`文件夹中的`Dockerfile`构建的:

```
context: ./frontend/.devcontainerdockerfile: ./Dockerfile
```

我们还使这个容器依赖于我们的`api` 服务和`redisserver`服务，这样一旦我们启动了`frontend`容器，两个容器都将被启动。我还公开了端口‘8000 ’,这样一旦我开发了它，我就可以从我的主机上访问`frontend`。

```
ports:- "8000:8000"
```

使用相同的格式，`api` 服务将如下所示:

```
api:image: apitty: truebuild:context: ./api/.devcontainerdockerfile: ./Dockerfilevolumes:
- .:/workspace:cachednetworks:- myNetwork
```

请注意，我将这两个服务都连接到了一个名为`myNetwork`的网络。

因为我计划只在这个 Docker 网络中使用 API，所以我没有向主机暴露任何端口。

`docker-compose.yml`文件中的网络配置部分如下所示，将位于配置的最末端。

```
networks:myNetwork:driver: bridge
```

现在来看将成为我们后端的`redisserver` 服务。因为我们不打算在这个容器中开发，所以我们可以设置这个容器，没有`.devcontainer`文件夹和`Dockerfile`和`devcontainer.json`文件。

我不会详细介绍 redis 容器的配置，下面的配置对我来说很有用:)

```
redissever:image: redisservertty: truebuild:context: ./redis/dockerfile: ./Dockerfilevolumes:- .:/workspace:cached- ./redis/redis-data:/dataports:- "6379:6379"command:# Save if 100 keys are added in every 10 seconds- "--save 10 100"networks:- myNetwork
```

这是我正在使用的完整的 docker-compose.yml 文件。

Docker 合成文件

这两个服务的`devcontainer.json`文件非常相似，但是在`docker-compose.ym` l 文件中引用它们各自的映像。
必须为每个集装箱定制以下零件；这是`frontend` 服务的具体部分:

```
"name": "frontend","dockerComposeFile": "../../docker-compose.yml","workspaceFolder": "/workspace/frontend","service": "frontend",
```

我们需要定义名称，VS 代码将用来构建我们的环境的`docker-compose.yml`文件，我们的代码将存储在`workspaceFolder` 中的文件夹，以及我们在 Docker Compose 文件中引用的服务的名称。

包含 Python linter 配置的两个完整文件:

前端 devcontainer.json

API devcontainer.json

为了完成我们的设置，我们需要为每个服务创建`requirements.txt`文件。前端服务的一个具有以下内容，因为我的前端将基于 PyWebIO:

```
pylint
pycodestyle
black
requests
pywebio
```

API 服务的内容如下:

```
pylint
pycodestyle
black
fastapi
uvicorn[standard]
redis
```

API 将使用`fastapi` 构建，而`uvicorn` 将用于托管 API。我还安装了`redis` Python 模块来访问 redis DB。

包括文件的完整文件夹结构应该是这样的

```
C:\DEV\MULTICONTAINERSETUP
│   docker-compose.yml
│   
├───api
│   └───.devcontainer
│           devcontainer.json
│           Dockerfile
│           requirements.txt
│           
├───frontend
│   └───.devcontainer
│           devcontainer.json
│           Dockerfile
│           requirements.txt
│           
└───redis
```

使用 CTRL+SHIFT+P 或 F1 访问 VS 代码命令面板，我们选择以下命令

```
Remote-containers: Open Folder in Container
```

在下面的窗口中，我们选择 API 或前端文件夹，vs 代码将启动三个容器。如果一切正常，我们将看到容器中的一个终端，所有三个容器都将运行。