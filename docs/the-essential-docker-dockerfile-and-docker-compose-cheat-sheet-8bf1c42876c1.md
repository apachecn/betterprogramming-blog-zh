# 基本 Docker、Dockerfile 和 Docker 组成备忘单

> 原文：<https://betterprogramming.pub/the-essential-docker-dockerfile-and-docker-compose-cheat-sheet-8bf1c42876c1>

## 所有基本的 Docker CLI、Dockerfile 和 Docker 在一个地方编写命令，并带有简单的示例

![](img/219769bf878073db8121b785b3f1d86b.png)

图片来源:Docker.com

[Docker](https://docker.com) 已经成为每个软件开发者的必备工具。如果你还没有听说过 Docker，它是一个免费的、强大的、可靠的创建和部署容器的工具，适用于 Linux、macOS 和 Windows。来自[官方 Docker 文档](https://www.docker.com/resources/what-container):

> “容器是一个标准的软件单元，它将代码及其所有依赖项打包，以便应用程序能够快速可靠地从一个计算环境运行到另一个计算环境。
> 
> Docker 容器映像是一个轻量级、独立、可执行的软件包，包含运行应用程序所需的一切:代码、运行时、系统工具、系统库和设置

![](img/5450cada33eaf69d8495e9957af32ed1.png)

docker.com 码头建筑公司

有了 Docker，开发者可以忘记部署应用程序时的烦恼。您的应用程序被打包成一个映像，这个映像作为一个容器部署在您的服务器或云上。使用 Docker Compose，您甚至可以编排几个服务的部署，这些服务共同构成一个应用程序。

![](img/ee9580285ba030a3e7726800d2a947a7.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) 拍照

# Docker CLI 命令

Docker 提供了一个名为 Docker Dashboard 的 UI，您可以在其中执行一些任务，但使用 Docker 最常用的方式是通过它的 CLI，所以让我们在这里看看一些最有用和最常用的命令的示例。

## 从映像创建并运行容器

要创建并运行一个名为“my_beautiful_container”的带有官方 Ubuntu 映像的容器，我们只需使用`[docker run](https://docs.docker.com/engine/reference/run/)`命令:

```
**docker run** -d — name my_beautiful_container ubuntu  *# run the container as a background process***docker run** -it — name my_beautiful_container ubuntu /bin/bash  *# run the container and launch an interactive shell on it*
```

当使用`run`命令时，我们还可以将一个容器端口映射到一个主机端口(这对 web 服务器非常重要),例如:

```
**docker run** -p 1234:80 ubuntu  *# map the port 80 in the container to 1234 in the host machine*
```

就其本质而言，容器不提供数据持久性。如果我们需要在重启后持久保存容器中的一些数据，我们必须使用卷，这意味着将主机文件夹挂载到容器文件系统中:

```
**docker run** -v ~/my_folder:/root ubuntu  *# mount the host machine folder ~/my_folder into the /root directory in the container*
```

## 在运行的容器中执行命令

如果我们有一个名为“my_beautiful_container”的正在运行的容器，并且我们想在其上运行一个命令，我们可以使用`[docker exec](https://docs.docker.com/engine/reference/commandline/exec/)`命令:

```
**docker exec** -d my_beautiful_container touch /root/hello  *# execute the command touch /root/hello and finish***docker exec** -it my_beautiful_container /bin/bash  *# open an interactive shell in my_beautiful_container*
```

## 将文件复制到容器中/从容器中复制文件

使用`[docker cp](https://docs.docker.com/engine/reference/commandline/cp/)`命令，您可以将文件或文件夹从您的主机复制到您的容器，反之亦然:

```
**docker cp** ~/myfile.txt my_beautiful_container:/root/myfile.txt  *#host -> container***docker cp** my_beautiful_container:/root/myfile.txt ~/myfile.txt  *#container -> host*
```

## 管理容器

从 Docker CLI 中，您可以轻松地列出您的主机上运行的所有容器:

```
**docker ps**  *# show all running containers* **docker ps** -a  *# show all containers*
```

您还可以重命名、启动、停止和删除特定容器:

```
***# Delete containers* docker rm** my_beautiful_container
**docker container** prune  *# remove all stopped containers****# Start/stop containers* docker start** my_beautiful_container
**docker stop** my_beautiful_container***# Rename a container* docker rename** my_beautiful_container my_ugly_container  *# my_beautiful_container -> my_ugly_container*
```

或者查看其日志、映射的端口和内部运行的进程:

```
***# Show the logs of a container*
docker logs** my_beautiful_container***# Show the processes inside a container*
docker top** my_beautiful_container***# Show the mapped ports of a container*
docker port** my_beautiful_container
```

想知道每个容器使用了多少资源吗？只需键入以下内容:

```
docker stats
```

## 管理图像

每当你第一次运行一个容器时，Docker 会自动下载所有需要的图像。要列出电脑或服务器上下载的所有图像:

```
docker images
```

要从 Docker hub(Docker 官方映像库)下载一个映像到您的主机，您可以使用`[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)`命令，从本地文件加载它，`[docker load](https://docs.docker.com/engine/reference/commandline/load/)`:

```
**docker pull** ubuntu  *# download the image “ubuntu” from DockerHub*
**docker load** -i ubuntu.tar  *# load the image stored in the file “ubuntu.tar”*
```

要删除单个图像或所有未使用的图像:

```
**docker rmi** ubuntu  *# delete the image “ubuntu” from the computer*
**docker image prune** -a  *# remove all unused images*
```

## 管理卷

通过将容器目录映射到主机内部的文件夹，卷是持久化数据的最佳方式。使用`[docker volume](https://docs.docker.com/engine/reference/commandline/volume/)`命令，您可以轻松地列出所有已创建的卷，并删除那些没有被任何容器或特定容器使用的卷。

```
**docker volume** ls  *# list all volumes*
**docker volume** rm my_dirty_volume  *# delete a volume called my_dirty_volume*
**docker volume** prune  *# delete all unused volumes*
```

# 用 Dockerfile 文件创建图像

`[Dockerfile](https://docs.docker.com/engine/reference/builder/)`是一组构建 Docker 映像的指令。在下面的例子中，我将向您展示如何基于 Ubuntu 创建图像:

注意，对于每条指令，都会生成一个中间图像，因此在使用`run`指令时，尝试将所有命令连接在一起非常重要。

现在，我们将构建图像，为参数 USERNAME 提供一个值，并通过在`Dockerfile`所在的文件夹中运行以下命令，以名称“johndoe/my_awesome_image”存储它。

```
**docker build** . -t johndoe/my_awesome_image --build-arg USERNAME=john
```

# 使用 Docker Compose 部署多个容器

来自[官方文档](https://docs.docker.com/compose/#:~:text=Compose%20is%20a%20tool%20for,the%20services%20from%20your%20configuration):

> “Compose 是一个用于定义和运行多容器 Docker 应用程序的工具。使用 Compose，您可以使用 YAML 文件来配置应用程序的服务。然后，只需一个命令，您就可以从您的配置中创建和启动所有服务。”

在下面的例子中，我将向您展示如何部署一个全栈 web 应用程序，包括前端(容器`frontend`)和后端(容器`api`和`database`)。容器`database`使用 MySQL 镜像，`api`使用 NodeJS 镜像，`frontend`使用基本的 Apache HTTP 服务器。对于数据库，设置一个卷来保证持久(`db-data`)；对于前端，主机中包含 HTML、JS 和 CSS 文件的文件夹被装载到容器中。

对于前端，端口 80 映射到主机的端口 1234，而在数据库和 API 中，不需要打开任何端口，因为容器通过网络`database-api`和`frontend-api`互连。注意对于数据库，凭证是如何在一个名为`mysql.env`的外部文件中设置的。

现在，为了构建映像和部署这三个服务，我们只需在`docker-compose.yml`文件所在的文件夹中运行:

```
**docker-compose** build .
**docker-compose** up
```

我希望你喜欢这篇 Docker 常见操作的总结。我知道 Docker 是一个非常复杂的工具，所以这里我只介绍了基本的东西。要了解更多详细信息，请不要犹豫，在[官方文档](https://docs.docker.com)中搜索。

# 资源

*   安装码头工人:[https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
*   DockerHub 知识库:[https://hub.docker.com](https://hub.docker.com)
*   码头工人参考文件:[https://docs.docker.com/reference/](https://docs.docker.com/reference/)
*   案卷参考:【https://docs.docker.com/engine/reference/builder/ 
*   Docker Compose 参考: [https://docs.docker.com/compose/compose-file/](https://docs.docker.com/compose/compose-file/)
*   Dockerfile 示例: [https://gist.github.com/parireiro/bd39bc15d4f4e5ef118d75a4c038bea3](https://gist.github.com/gbarreiro/bd39bc15d4f4e5ef118d75a4c038bea3)
*   Docker Compose 示例: [https://gist.github.com/parireiro/775f111453d88b220d2fb1963e5ab08c](https://gist.github.com/gbarreiro/775f111453d88b220d2fb1963e5ab08c)