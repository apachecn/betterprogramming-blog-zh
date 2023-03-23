# 带有 GitHub 动作的 Docker 部署

> 原文：<https://betterprogramming.pub/docker-deployments-with-github-actions-7e59bb532505>

## 通过 GitHub 操作管理 Docker 群组和 Docker 组合部署

![](img/0c0e94174fe7938def82859af8a3d98e.png)

克里斯·萨博尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

有许多持续集成和交付工具可用于运行 CI/CD 管道，如 Jenkins、Travis CI 和 CircleCI。当谈到 GitHub 上托管的项目时，还有另一个本地选项可用，那就是 [GitHub workflows](https://docs.github.com/en/actions/configuring-and-managing-workflows) 。

GitHub 工作流可以用来实现几个目标，比如构建 [Docker](https://www.docker.com/get-started) 映像，运行单元测试，甚至部署到远程服务器。工作流可以由 [GitHub 事件](https://help.github.com/en/actions/reference/events-that-trigger-workflows)以及[外部事件](https://help.github.com/en/actions/reference/events-that-trigger-workflows#external-events-repository_dispatch)触发。例如，可以创建一个工作流，一旦您在存储库上创建了一个新的版本，这个工作流就会被执行。此外，相同的工作流可以配置为由外部 API 调用触发。

因为 GitHub 工作流可以使用本机 GitHub 事件触发，而无需定义 webhooks，所以它是一个比其他工具和选项更容易使用和定义的工具。

GitHub 工作流是在 YAML 格式文件中定义的，这些文件描述了在工作流中需要执行哪些操作或步骤。你可以在文章“[使用 Docker 容器构建 GitHub 动作](https://medium.com/better-programming/build-github-actions-using-docker-containers-c57a97be60e2)”中找到更多关于 GitHub 工作流和 GitHub 动作的内部信息。

由于 GitHub 的动作可以用来替换其他 CI 工具，并提供一种简单直接的方式来定义 CI 任务，所以我决定使用它们来管理和定义我在 GitHub 上托管的项目的 CI/CD 管道。

我想要构建的管道之一是使用 Docker 命令将 Docker 服务部署到远程服务器的管道。不幸的是，我在 [marketplace](https://github.com/marketplace?type=actions&query=docker+deployment+) 中找不到任何 GitHub 动作来满足我的需求，并帮助我执行 Docker 部署到远程服务器。因此，我决定构建自己的 GitHub action 来部署 Docker 服务。

在本文中，我将一步一步地解释我为管理 docker 服务部署而构建的 Github 动作的内部结构和细节。

# 行动要求

如前所述，我首先在 GitHub actions 中寻找一个可以用来部署 Docker 服务的现有操作，但是我找不到满足我需求的操作。以下是我正在寻找的能够实现我的 CI/CD 管道所需的功能。

*   支持两种不同的部署模式:

→ Docker swarm:支持从预定义的 Docker 栈文件部署 Docker swarm 服务。

→ Docker Compose:支持使用 Docker Compose 部署 Docker 容器。

*   使用 Docker API 从 GitHub workers 远程部署 Docker 服务，而无需将任何文件复制到远程服务器。
*   支持没有 Docker API 的部署:在这种情况下，Docker 合成文件将被复制到远程服务器，Docker 命令将使用 SSH 协议在服务器上执行。
*   允许在部署 Docker 容器之前将 Docker 映像拉至远程服务器。
*   在推出新版本的容器之前，执行预部署任务。例如，在创建新的 Docker 容器之前，运行数据库模式迁移来更新应用程序数据库。

# 使用 GitHub 动作

实现的 GitHub 动作可以像所有其他动作一样使用。操作支持许多必需的参数以及许多可选的参数，这些参数可用于影响操作或部署过程的行为。以下是所有必需参数的列表:

*   `remote_docker_host`:远程服务器地址。此参数的值必须遵循以下格式:user@host。并且主机需要是公共可访问的，即它必须具有公共 IP。GitHub 动作将使用 SSH 协议与远程服务器通信。
*   `ssh_public_key`:远程服务器的公钥。在 SSH 成功连接到服务器后，这个密钥将被添加到`known_hosts`文件中。
*   `ssh_private_key`:用于连接远程服务器的私钥。这个密钥的公共部分需要添加到远程服务器上的`authorized_hosts`文件中。
*   `args`:将用于启动部署的`docker`或`docker-compose`命令的参数。

除了上述强制参数之外，该操作还支持以下可选参数。这些参数中的每一个都有一个默认值，如果没有提供值，将使用该默认值:

*   `pre_deployment_command_args`:预部署命令的参数。默认值是一个空字符串。
*   `docker_prune`:触发`docker-prune`命令的布尔输入。默认值为`false`。
*   `pull_images_first`:展开前拉动`docker images`。默认值为`false`。
*   `deployment_mode`:展开模式 `docker-swarm`或`docker-compose`。默认为`docker-compose`。
*   `copy_stack_file`:将堆栈文件复制到远程服务器，并从服务器部署。默认为`false`。
*   `deploy_path`:堆栈文件将被复制到的路径。默认`~/docker-deployment`。
*   `stack_file_name`:使用了 Docker 堆栈文件。默认为`docker-compose.yaml`。
*   `keep_files`:保存在服务器上的文件数量。默认值为三。

# 使用动作的示例

开发的动作可以以多种方式使用，并实现不同的目标。下面是可以使用该操作的四种用例的简要描述。

**案例一:**使用 Docker API 将 swarm 服务部署到远程 Docker swarm 集群。

**案例二:**使用 Docker API 将`docker-compose`栈部署到远程 Docker 服务器。

**情况三:**通过在 swarm 集群上运行部署命令，将 swarm 服务部署到远程 Docker swarm 集群。

**案例四:**通过在 Docker 节点上运行命令，将`docker-compose`栈部署到远程 Docker 服务器。

# 结论

GitHub actions 是在 GitHub 上实现原生 CI/CD 管道的最佳工具之一。市场包含数百个(如果不是数千个的话)可用于构建所需管道的操作。此外，构建新的操作并在市场上发布它们以支持新的 CI/CD 用例是非常简单和直接的。

这篇文章展示并解释了我构建的一个 GitHub 动作来帮助部署 docker 服务和容器到 docker 群和 Docker 节点。