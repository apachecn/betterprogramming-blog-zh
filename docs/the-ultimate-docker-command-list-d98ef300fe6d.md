# 终极 Docker 命令列表

> 原文：<https://betterprogramming.pub/the-ultimate-docker-command-list-d98ef300fe6d>

## Docker 命令的精选列表，可以节省您调试和查找堆栈溢出的时间

![](img/8f9ad4ae515372c62f1ae530765f40f1.png)

在 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上 [Grzegorz Walczak](https://unsplash.com/@grzegorzwalczak?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 的照片

# 目录

```
#1\. Docker Build
#2\. Running Docker Containers
#3\. Debugging Docker Containers
  ○ Docker on AWS ECS
#4\. Cleaning Up Docker Images
#5\. Pulling Docker Images from a Remote Registry
#6\. Exporting and Importing Physical Docker Images
#7\. Final Thoughts
```

# 介绍

Docker 容器已经从一项小众技术转变为我们环境中的绝对必需品。有时候，作为开发人员，我们花了大量的时间来调试或使用本该帮助我们提高工作效率的工具。每一次新的技术浪潮，都有数量惊人的移动部件需要学习。

你们中有多少人花了一两天的时间来尝试安装 Docker 集群，或者得到了一段使 Docker 容器无法启动的代码？对于大多数开发人员来说，很多时间都花在了与配置的协商上——寻找 bug 似乎比实际推出新功能花费的时间还要多，尤其是当您工作的环境相对较新或不成熟的时候。

我们中的一些人没有幸运地拥有稳定的 CI/CD 流程环境。对于那些属于这个阵营的人，这篇文章是给你的。这篇文章成了经验的副产品。就像你一样，我也经历了几天的调试。这篇文章旨在补充 Docker 主要技术文档网站上的资料，同时也侧重于您在日常使用 Docker 时最有可能与之交互的常见命令。

有关全面可选标志和参数的更详细列表，请参考 Docker [手册](https://docs.docker.com/engine/reference/commandline/cli/)。请注意，根据您的 Docker 系统配置，您可能需要在每个`docker` 命令前加上`sudo`。

**提示:**每个 Docker 命令都有内置文档。学会使用它。键入`docker run --help`将生成以下帮助文档。

![](img/215f0da648440164a4dfc4957fb4cb6d.png)

我希望这篇指南能帮助你在调试和使用 Docker 的兔子洞的战壕中导航。阅读命令时，请注意命令附带的解释。

# 码头工人建造

```
$ docker build \
--build-arg ARTIFACTORY_USERNAME=timothy.mugayi \
--build-arg ARTIFACTORY_SECRET_TOKEN=AP284233QnYX9Ckrdr7pUEY1F \
--build-arg LICENSE_URL='[https://source.com/license.txt'](https://math-engine-qed.s3.amazonaws.com/app/GAMS/license/dev/gamslice.txt?AWSAccessKeyId=AKIA4H32CCVXJWXVR7HA&Signature=%2BEwLxP0NS8LsdculF8a72HKPUIs%3D&Expires=1614488484') \
--no-cache  -t helloworld:latest .
```

这将用可选的构建参数构建一个 Docker 映像。Docker 将默认缓存 docker 文件的第一次构建或后续构建的结果，这些结果基于通过 docker 文件中的`run`命令添加到图像的新层，从而使后续构建更快。

如果您不需要这样做，您可以附加一个无缓存参数，就像我们在上面的示例中所做的那样。如果您想了解如何在构建过程中使用 ARGs 和 Docker 环境变量，您可以阅读我的另一篇文章

[](https://medium.com/better-programming/how-to-get-docker-to-play-nicely-with-your-python-data-science-packages-81d16f1080d2) [## 如何让 Docker 很好地使用您的 Python 数据科学包

### 数据科学项目分类指南

medium.com](https://medium.com/better-programming/how-to-get-docker-to-play-nicely-with-your-python-data-science-packages-81d16f1080d2) 

注意:Docker 命令可以通过名称或 Docker 容器 ID 来执行。`<CONTAINER>`可与集装箱 ID 或集装箱名称互换。

# 运行码头集装箱

```
$ docker start <CONTAINER>
```

启动现有容器。我们假设已经下载并创建了容器。

```
$ docker stop <CONTAINER>
```

停止现有正在运行的 Docker 容器。

```
$ docker stop $(docker container ls -aq)
```

如果你有多个 Docker 容器在运行，并且你希望停止所有的容器，输入`docker stop`和所有容器 id 的列表。

```
$ docker exec -ti <CONTAINER> [COMMAND]
```

在特定容器中运行 shell 命令。

```
$ docker run -ti — image <IMAGE> <CONTAINER> [COMMAND]
```

码头工人`run`和`start`之间有着非常明显的区别。Docker `run`本质上做了两件事:(1)创建一个图像的新容器，以及(2)执行该容器。如果你想重新运行一个失败或退出的容器，那么使用`docker start`命令。

```
$ docker run -ti — rm — image <IMAGE> <CONTAINER> [COMMAND]
```

这是一个有趣的命令，用于同时创建和启动一个容器。它还在其中运行一个命令，然后在执行完命令后删除容器。

```
docker run -d  <IMAGE>:<IMAGE_TAG>Usage: 
   docker run -d  helloworld:latest
```

如果您希望在分离状态下启动`docker run`命令——例如，作为后台的 Linux 守护进程——您可以将`-d`添加到`run`命令中。

```
$ docker pause <CONTAINER>
```

暂停特定容器中运行的所有进程。

```
$ docker ps -a
```

上面的命令列出了以前运行过的所有 Docker 映像。一旦确定了要运行的映像，请执行下面的命令。确保更改容器 ID，以反映初始`docker ps -a`命令显示的结果。

```
$ sudo docker run {container ID} -e AWS_DEFAULT_REGION=us-east-1 \
e INPUT_QUEUE_URL="[https://sqs.us-east-1.amazonaws.com/my_input_sqs_queue.fifo](https://sqs.us-east-1.amazonaws.com/my_input_sqs_queue.fifo)" \
e REDIS_ENDPOINT="redis.dfasdf.0001.cache.amazonaws.com:8000" \
e ENV=dev \
e DJANGO_SETTINGS_MODULE=engine.settings \
e REDIS_HOST="cmgadsfv7avlq.us-east-1.redis.amazonaws.com" \
e REDIS_PORT=5439 \
e REDIS_USER=hello \
e REDIS_PASSWORD=trasdf**#0ynpXkzg
```

上面的命令演示了如何运行一个 Docker 映像，其中多个环境变量作为参数传入，其中`\`是一个换行符。

```
$ docker run -p <host_public_port>:<container_port>
```

如果你发现自己不得不暴露码头。run 命令采用参数 p 进行端口转发。其中`host_` public_port 是您希望 docker 转发 container_port 的机器端口。对于多个端口，附加另一个-p 参数。

```
$ docker run -p <host_public_port1>:<container_port1> -p <host_public_port2>:<container_port2>
```

# 调试 Docker 容器

使用`docker ps`获取当前正在运行的现有容器的名称。

```
$ docker history <IMAGE> example usage:$ docker history my_image_name
```

显示特定图像的历史。当您想要深入了解并详细了解 Docker 图像的形成过程时，这些信息非常有用。让我们暂时离开这个话题，因为有必要真正理解这个命令的作用。关于司令部的文献很少。

当我们谈论 Docker 时，图像是建立在层之上的，层是 Docker 图像的构建块。每个容器都由一个带有可读/写层的图像组成，可以把它想象成一个持久化的状态或文件。在此之上，附加了其他只读层。当 Docker 文件中的命令被执行时，这些层(也称为*中间图像*)被生成。

如果您在 docker 文件中有一个`from`、`run`和/或`copy`指令，然后有`build`个图像，那么这个`run`指令将导致创建一个具有自己的图像 ID 的层。该图像/图层将显示在`docker history`下，带有图像 ID 和图像创建日期。后续指令将导致另一个条目，以此类推。`CREATED BY`列将大致对应于 Dockerfile 文件中的一行，如下图所示。

![](img/ff51edc5a09ca17401dcd02b3602a7a6.png)

“停靠历史”命令的图示

```
**$** docker images
```

列出当前存储在机器上的所有图像。

```
$ docker inspect <IMAGE|CONTAINER ID>
```

Docker `[inspect](https://docs.docker.com/engine/reference/commandline/inspect/)`显示关于特定 Docker 对象的底层信息。存储在该对象中的数据在调试情况下非常有用，例如，交叉检查 Docker 挂载点。

请注意:该命令获取两个主要响应——图像级别的详细信息和容器级别的详细信息。您可以从该命令中获得一些启示:

*   创建时的容器 ID 和时间戳
*   当前状态(在尝试确定容器是否停止以及停止的原因时很有用)
*   Docker 映像信息、文件系统绑定和卷信息，以及挂载
*   环境变量—例如，传递到容器中的命令行参数
*   网络配置:IP 地址和网关以及 IPv4 和 IPv6 的辅助地址

```
$ docker version
```

显示 Docker 的版本包括当前安装在机器上的客户机和服务器版本。

是的，你基本上没看错。Docker 是一个客户端-服务器应用程序。守护进程(长期运行的 Linux 后台服务)是服务器，CLI 是众多客户机中的一个。Docker 守护进程公开了一个 REST API，许多不同的工具可以通过这个 API 与守护进程进行对话。

Docker 版本输出

![](img/b1643ceeca15809f0e19dbddfa1b159e.png)

这里有一个客户机-服务器架构是如何设置的图像

## AWS ECS 上的 Docker

一般来说，有时您需要进入一个正在运行的 Docker 容器来调试或交叉检查配置是否正确。

使用命令`docker exec -it <container ID> /bin/bash`获得 shell 访问权限。如果您发现自己试图查找一个无法运行的 Docker 映像，例如，如果您正在使用 AWS ECS 集群，您会得到一个模糊的错误消息，如下所示。

老实说，这可能是由许多事情引起的，例如(1)您的代码有问题——例如，抛出了一个未捕获的异常，并且您的 Docker 容器在启动时死亡，(2)如果您在 EC2 实例上使用 ECS 集群，磁盘空间不足——也就是说，如果您没有使用 ECS 放置类型 [Fargate，](https://aws.amazon.com/fargate/)或者(3)您现有的 Docker 容器超出了您的 EC2 可用内存。

```
Essential container in task exited
```

执行下面的命令来识别最近运行失败的 Docker 容器。如果您的帐户有`sudo`权限，请省略`sudo`。对于给定的输出，使用它来重新运行容器，以查看失败的原因。

```
$ sudo docker ps -a --filter status=dead --filter status=exited --last 1
```

## 如有疑问，请重新启动 Docker 服务

```
$ sudo service docker stop$ sudo service docker start# on a MAC you an use the docker utilty or alternatively run $ killall Docker && open /Applications/Docker.app
```

这不需要进一步解释。

# 清理 Docker 图像

```
$ docker system prune
```

Docker 采用保守的方法来清理未使用的对象，如图像、容器、卷和网络。

这些对象通常不会被删除，除非您明确要求 Docker 这样做。因此，如果这些对象没有被删除，这可能会很快开始占用大量空间。因此，定期运行以下命令来清理未使用的 Docker 映像非常重要。

```
docker kill <CONTAINER>
```

终止现有的运行容器。

```
$ docker kill $(docker ps -q)
```

终止当前正在运行的所有容器。

```
$ docker rm <CONTAINER>
```

删除当前未运行的特定容器。如果图像存在于远程注册表中，图像不会受到影响。

```
$ docker rm $(docker ps -a -q):
```

删除所有当前没有运行的容器。

```
$ docker logs my_container
```

获得对容器日志的访问(对调试有用)。

# 从远程注册表中提取 Docker 图像

## 码头枢纽

Docker Hub 是 Docker 提供的一项服务，用于查找和共享打开的容器图像。

如果您希望将图像从那里拖到您的本地注册表，只需运行 Docker `run`命令，后跟图像路径即可。下面的命令演示了提取和运行版本稳定的 R Rocker 映像。

```
$ docker run --rm -p 8787:8787 rocker/verse
```

Docker 最初会尝试检查这个映像在您的本地机器上是否可用。如果它没有从 Docker Hub 存储库下载图像，这是现成的。

```
$ docker pull rocker/verse
```

如果您只想提取图像而不运行容器，那么`docker pull`就足够了。

```
docker login --username={DOCKERHUB_USERNNAME} --email={DOCKERHUB_EMAIL}
```

要登录 Docker Hub，可以运行上面的命令，该命令会提示您输入密码。

## 自定义 Docker 注册表

```
$ docker login your.docker.host.com
Username: foo
Password: ********
Email: user@myemail.com
```

如果您从一个需要认证的通用定制 Docker 注册中心获取，那么`docker login`命令允许您从任何 Docker 注册中心获取，如上图所示。注意，在执行上述操作时，它会在您的`~/.docker/config.json`文件中创建一个条目。连接`~/.docker/config.json`以修改认证细节。

## 亚马逊弹性容器注册中心

Amazon Elastic Container Registry(ECR)是一个完全托管的 Docker 容器注册表，允许开发人员存储、管理和部署 Docker 容器映像。亚马逊 ECR 与[亚马逊弹性容器服务(ECS)](https://aws.amazon.com/ecs/) 无缝协作。如果您发现自己需要从 ECR 注册表中提取图像，请遵循以下命令。

您需要为您的 IAM 用户配置 AWS CLI[和一个密钥。](https://aws.amazon.com/cli/)

Amazon ECR 要求 IAM 用户访问密钥具有 IAM 策略允许的权限(`ecr:GetAuthorizationToken`)，然后您才能向注册中心认证并提取任何图像。或者，您可以利用 [Amazon ECR Docker 凭证助手](https://github.com/awslabs/amazon-ecr-credential-helper)实用程序。下面的方法假设您正在使用 AWS CLI 并配置了所有权限。

```
$ aws ecr list-images --repository-name=twitter-data-engine-core$ aws ecr describe-images —- repository-name=twitter-data-engine-core
```

`get-login`命令生成一个长 Docker 登录命令。复制并执行它。在您尝试从 AWS ECR 执行 Docker 映像提取之前，需要进行身份验证。

```
$ aws ecr get-login —- region us-east-1 —- no-include-email --profile {AWS_NAMED_PROFILE_NAME}# profile argument is optional provided your using the default aws cli profile. if you have multiple named profiles in your aws /.aws/credentials file then you need to explicity set the named profile to pick the onen you wish to use$ docker login -u AWS -p {YOUR_TEMPORARY_TOKEN}$ docker pull 723123836077.dkr.ecr.us-east-1.amazonaws.com/twitter-data-engine-core:build-9
```

# 导出和导入物理 Docker 图像

```
$ docker save your_docker_image:latest > /usr/local/your_docker_image.tar$ docker load < /usr/local/your_docker_image.tar
```

如果你曾经有一个需要，并希望出口到磁盘和加载回 Docker 图像，那么上述将做到这一点。

例如，当您想要通过替代介质(而不是 Docker 注册表)将 Docker 图像从一台机器传输到另一台机器时，导出到文件非常有用。出于安全原因，您可能对某些环境的访问受到限制。这可能会使您无法进行注册表间的迁移，因此，这是一个很有用的命令，很容易被遗忘。

# 最后的想法

为了确保本指南保持相关性，我们会不时地对其进行定期更新，以确保其跟上 Docker 中的新变化和新功能——因此，请不时查看。

干杯，编码快乐！