# 对接 Rails 应用第 2 部分:自动化

> 原文：<https://betterprogramming.pub/dockerizing-rails-applications-part-2-automation-3092975fa4bb>

## 通过使用 Makefiles 简化 Rails 应用程序的 Docker 构建

![](img/811c29287f12c8af09a79bffb0b1d555.png)

照片由[奥斯汀·尼尔](https://unsplash.com/@arstyy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

[](https://medium.com/faun/dockerizing-rails-applications-part-1-writing-the-dockerfile-dc32aa25a0da) [## Dockerizing Rails 应用程序第 1 部分:编写 Dockerfile

### 让我们开始编写一个优化的 Dockerfiles

medium.com](https://medium.com/faun/dockerizing-rails-applications-part-1-writing-the-dockerfile-dc32aa25a0da) 

在我之前的[文章](https://medium.com/faun/dockerizing-rails-applications-part-1-writing-the-dockerfile-dc32aa25a0da)中，我讨论了一些编写 Docker 文件的最佳实践，比如使用一个`entrypoint`和减少每个 Docker 文件的`RUN`指令数量——以及使用定制的 Docker 基本映像。

通过提供完成这项工作所需的命令，本文结尾简要提到了构建和推送 Docker 映像。下面将再次介绍这些命令。

```
$> docker build -t ${IMG}:${IMG_TAG} Dockerfile
$> docker push ${IMG}:${IMG_TAG}
```

构建图像的 Docker 命令简单易用；但是，这可能会非常漫长和复杂，尤其是在构建 Docker 映像需要额外步骤的情况下，例如:

*   将构建参数传递给`docker build`命令
*   在构建 Docker 映像之前安装应用程序依赖项
*   生成要附加到 Docker 图像的标签
*   在构建 Docker 映像之前，需要编译源代码

以上几点会使构建 Docker 映像变得更加复杂，因此,`docker build`命令会更加复杂和冗长。此外，在管理多个应用程序的情况下，这意味着每个应用程序将具有不同的唯一命令集来构建其 Docker 映像，尤其是如果这些应用程序是使用不同的编程语言构建的。

解决这个问题的一个想法是将构建 Docker 映像的复杂性隐藏在所有 Docker 服务的统一接口之后。该工具或界面的要求如下:

*   构建和推送 Docker 映像应该通过简单的命令来完成
*   构建和推送 Docker 映像应该在不同的服务中以相同的方式完成

我们可以简单地通过使用以前的一个叫做 [make](https://www.gnu.org/software/make/manual/make.html#Makefiles) 的工具来实现这个接口。该命令通常安装在 macOS 和 Linux 系统上，用于管理和编译源代码过程，以及定义编译和安装软件应用程序所需的命令。 [Makefiles](https://www.gnu.org/software/make/manual/make.html#Introduction) 是定义编译和构建源代码指令的文件，这些指令将被`make`命令使用。

为了解决我们的问题，我们将编写一个 Makefile 来定义如何构建和推送 Docker 映像，然后我们将使用`[make](https://www.gnu.org/software/make/manual/make.html#Makefiles)`命令来执行实际的过程，并调用所需的`docker`命令。

[Makefile](https://www.gnu.org/software/make/manual/make.html#Introduction) 文件的第一部分应该是所有支持的参数配置项目的列表。这是一个让 [Makefile](https://www.gnu.org/software/make/manual/make.html#Introduction) 更具可读性和灵活性的好主意。

可以使用环境变量覆盖这些配置项。例如，我们可以设置一个默认的名称空间，然后构建 Docker 图像，并使用带有额外环境变量的相同的`make`命令将它们推送到另一个名称空间——例如`make build`和`NAMESPACE=overwritten make build`。

下面是其中一些配置项目的列表，以及对每个项目的描述。

*   `NAMESPACE`:项目名称空间将被用作 Docker 图像的名称空间
*   `REGISTRY`:用于托管 Docker 映像的 Docker 注册中心的 URL
*   `BRANCH`:用于构建 Docker 映像的 Git 分支。Docker 图像将用分支名称标记。例如，从主分支构建的 Docker 图像将被标记上`master`标签，而从`develop`分支构建的图像将被标记上`develop`标签。

下面是一些应该由`make`命令定义和使用的配置项目列表。这些变量的值要么是静态值，比如`image_name`，要么是使用 Git 命令自动生成的，比如`commit_hash`。

*   `IMAGE_NAME`:这将定义 Docker 图像名称，通常可以与应用程序名称相同
*   `IMAGE_FULL_NAME`:这是包含在注册表和名称空间中的完整 Docker 映像名称
*   `BRANCH_TAG`:这是将用于标记 Docker 图像的分支标记。这个项目依赖于分支，但是它确保该值可以用作标记——也就是说，该标记不包括任何特殊字符，如`/`。
*   `COMMIT_HASH`:用于构建 Docker 映像的分支中的最后一个提交散列。我们可以使用这个项目来标记 Docker 映像，并向 Docker 映像添加一个标签，这样就可以更容易地找到 Docker 映像中运行的代码版本。
*   `COMMAND`:Docker 镜像支持的默认命令。对于 Rails 应用程序，这可能是`rails server`命令。
*   `PORT`:服务需要的端口。这些端口将暴露给主机系统。
*   `CONTAINER_ENV`:应用默认环境变量将链接到由`make`命令创建的容器
*   `BUILD_PARAMS`:`docker build`命令的选项列表
*   `BUILD_ARGS`:支持的 Docker 列表`--build-arg`

下一步是开始定义`Makefile`支持的命令。下面是一组建议的 make 命令列表，这些命令可以使构建、推送和测试 Docker 映像变得更加容易:

*   `make config`:该命令将用于执行构建 Docker 映像所需的所有操作。这些操作可能是为构建过程安装必要的包，编译包的源代码，或者生成配置文件。下面是一个 Rails 应用程序的建议`config`实现，它将所有 gem 打包到本地供应商目录中。

*   `make build`:该命令将用于在主机节点上构建并标记 Docker 映像。下面是 Rails 应用程序的一个建议实现，它构建 Docker 图像并用`COMMIT_HASH`和`BRANCH_TAG`标记它。

*   `make shell`:该命令将从生成的 Docker 映像创建一个新的容器，并在容器内打开一个 shell 会话。下面是一个在临时容器中启动一个`sh` shell 的实现。

*   `make run`:该命令将用于从前台生成的 Docker 图像创建一个容器，并执行应用默认命令。下面的代码片段显示了一个`run`命令的实现，它将启动一个临时容器，并执行容器内变量`COMMAND`中定义的命令。

```
run:
  docker run --rm --name ${NAME}-${BRANCH_TAG} ${CONTAINER_ENV} -it ${PORT} ${IMAGE_FULL_NAME}:${COMMIT_HASH} ${COMMAND}
```

*   `make start`:该命令将用于从后台生成的 Docker 图像创建一个容器，并执行应用程序的默认命令。下面的代码片段显示了一个`start`命令的实现，它将使用变量`COMMAND`中定义的命令在后台启动一个容器。

*   `make stop`:该命令将停止由`make start`命令创建的容器。这可以简单地通过使用`docker rm -f`命令来完成。

```
stop:
  docker rm -f ${NAME}-${BRANCH_TAG}
```

*   `make push`:该命令用于将生成的 Docker 图像的标签从主机节点推送到 Docker 注册表。下面的实现将把 branch 标签和提交 hashtags 推送到 Docker 注册中心。另外，如果使用的分支是`master`分支，`push`命令也会推送`latest`标签。

*   `make release`:该命令将用于自动执行`config`、`build,`和`push`命令。基本上，它会调用这些子命令中定义的任务。

```
release: config build push
```

*   `make clean`:该命令将用于删除使用`config`和`build`命令生成的所有文件或 Docker 图像。下面的`clean`命令实现删除了由`build`命令生成的 Docker 图像，以及由`config`命令创建的 Ruby gemset。

完整的`Makefile`实现如下所示。该实现可用作 Docker `Makefiles`的模板，并用于生成 Docker 图像。

生成文件

# **结论**

Makefiles 有助于简化 Docker 映像的构建，并将复杂的 Docker 命令隐藏在简单易用的界面之后。它们有助于标准化跨多个项目构建 Docker 映像的方式，因此，它们有助于创建简单的 CI/CD 集成管道。

[](https://medium.com/faun/dockerizing-rails-applications-part-3-ci-cd-integration-9f2dcd84780f) [## Dockerizing Rails 应用程序第 3 部分:CI/CD 集成

### 让我们使用 CI/CD 工具来自动创建 docker 映像

medium.com](https://medium.com/faun/dockerizing-rails-applications-part-3-ci-cd-integration-9f2dcd84780f)