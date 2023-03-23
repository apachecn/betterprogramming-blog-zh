# 如何让 Docker 很好地使用您的 Python 数据科学包

> 原文：<https://betterprogramming.pub/how-to-get-docker-to-play-nicely-with-your-python-data-science-packages-81d16f1080d2>

## 数据科学项目分类指南

![](img/94a17425f6c7c0711349dd5fd1cea1c9.png)

克里斯·利维拉尼在 Unsplash[上的照片](https://unsplash.com/s/photos/data-science?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

谈到 Python 包管理，您有两种选择来处理您的 Python 依赖项。最广泛使用的是 Conda、pip 和 pyenv。

在本文中，我们将探讨在 Docker 容器中对数据科学应用程序进行 Docker 时可以使用的两种方法:默认情况下 Python 附带的传统 pip 和 Conda 方法。

为了了解如何对您的数据科学项目进行分类，您需要了解 Conda 和 pip 之间的主要区别。尽早做出最佳决策将防止未来的返工。

## 下面的例子说明了什么

本文不是关于 Docker 如何工作，而是如何:

*   在探索 pip 和 Conda 之间的差异时，开始构建您的 Docker 形象。
*   了解如何将 Docker 配置为从私有 PyPI 服务器拉取
*   探索在 Docker 中为数据科学 Python 应用程序切换 Python 版本的想法

# 皮普是什么？

pip 是 Python 3.4 及更高版本中默认的标准包管理系统。由 pip 管理的包是使用 [Setuptools](https://pypi.org/project/setuptools/) 构建的，Setuptools 是一个包开发过程库，旨在方便打包 Python 项目。通常如果你浏览 GitHub Python 项目，你会注意到。一个位于项目根的`setup.py`——比如这个位于 [Keras](https://github.com/keras-team/keras/blob/master/setup.py) Git 仓库的例子。

pip 安装 Python 包——通常，当您阅读任何在线文献时，都会遇到这种情况，但这并不意味着您不能将其他应用程序与 pip 捆绑在一起，并利用 Python 子进程来完成一些非 Python 安装。

最近，我做了一个[GAMS](https://www.gams.com/)-包装器 Python-installer 包，允许在 Windows、Linux 和 MAC 上安装 GAMS。这个包托管在一个私有的 PyPI 服务器上。MAC 安装通过 DMG 映像执行，而 Linux 安装通过可执行的 Linux 二进制包执行。Windows 安装通过无人值守安装来执行，还可以选择执行静默(或*安静*)安装。这种进行 pip 安装的方法不是标准软件包过程的一部分，但是我决定通过 pip 来标准化我所有的软件包安装。

pip 包通常托管在 PyPI 上。这可以是开源或私有 Python 包的私有或公共存储库。对于那些不熟悉 Python 包存储库的人来说，当你想到 PyPI 时，会想到 RubyGems for Ruby、Packagist for PHP、Maven for Java、CPAN for Perl 和 npm for Node.js

pip 有局限性，比如缺少包隔离。这意味着开箱即用，您不能以孤立的方式运行多个 Python 版本。这就是 [virtualenv](http://www.virtualenv.org/) 通过允许具有不同(并且经常*冲突*)需求的多个 Python 项目在同一台计算机上共存来解决这个非常具体的问题的地方。

# 什么是蟒蛇？

Anaconda 是 Python 和 R 发行版。它旨在提供 Python 数据科学所需的一切。

把它想象成你的瑞士军刀，把所有数据科学的东西都整合到一个地方。Anaconda 有自己的包管理器，类似于名为 conda 的 pip。

虽然 pip 可以安装除 Python 包之外的其他东西，但这不是设计出来的，也不是 pip 构建方式的一部分。Conda 通过允许 Conda 包支持非 Python 库依赖项来解决这个问题，如 HDF5、MKL 和 LLVM，它们的源代码中没有`setup.py`，也不将文件安装到 Python 的站点包目录中。

与 pip 相反，Conda 通过设计提供了每个环境的隔离。不使用隔离很难使用康达。默认情况下，当您使用 Conda 时，它会附带一个安装它的基础环境。通常鼓励您根据自己的需要创建并激活一个新的特定于应用程序的环境。

如果您正在处理与数据科学相关的应用程序，Conda 使它成为 Python 中理想的包管理器。通过康达 YAML 文件无缝切换 Python 环境的能力意味着，如果需要切换某些包甚至 Python 版本，您不必担心更改 Docker 配置。

让我们来看一个例子，使用 Docker 和几个数据科学包来设置 Conda 和 pip

# 先决条件

通过在终端中执行以下命令，确保您安装了 Docker:

```
$ docker --version
```

这将产生以下输出:

```
Docker version 19.03.5, build 633a0ea
```

为了便于说明，让我们创建我们的示例`helloworld.py`。在这个例子中，我们将展示一个使用 Dask 框架的简单程序，Dask 框架是一个轻量级数据科学分布式计算框架，允许您执行比内存计算更大的计算。

本质上，我们可以用 Dask 并行化普通的 Python 代码或 scale [pandas](https://pandas.pydata.org/) NumPy 库。或者我们可以在现有的 Python 工作流中增加并行性。可以把它想象成 PySpark 的轻量级版本，放在您喜欢的数据科学软件包之上。

helloworld.py 利用 Dask 框架进行数据科学并行计算

# pip 方式

下面的例子说明了如何配置一个额外的私有 PyPI 服务器来处理 Docker 中的软件包安装，方法是将`pip.config`配置为全局配置，允许 pip 读取其他 PyPI 软件包服务器。

我们使用的大多数 Python 包都是开源的外部包，但它们是需要使用私有服务器的实例。

Dockerfile pip 示例

在我们上面的 Docker 文件中，我们使用 Python 3.6 的`slim-buster`。`slim-buster` variant Docker 图像缺少通用包的层，因此图像尺寸往往更小。

请记住，Docker 的大小是受你的应用程序中有多少内容影响的。通常，如果构建速度很重要，但不一定等同于更好的应用程序性能，那么大小只对 CI/CD 有影响。

在我们的 Docker 文件中，我们使用 ARGs 来定义变量，以免在 Docker 文件中暴露安全凭证。参数仅在构建 Docker 映像期间可用，在创建映像和启动容器之后不可用(`ENTRYPOINT`、`CMD`)。为了避免 ARG 的过渡性质，我们将 ARG 值赋给 ENV 值。在 Docker 构建期间，从引入 ENV 值的那一行开始，容器和`RUN`命令都可以使用 ENV 值——在 Docker 容器的生命周期中也是如此。

下面是 pip `requirements.txt`，它由两个定制包和通用数据科学包组成。Pip 不支持 PyTorch 和 TensorFlow 开箱即用之类的一些包——这正是康达擅长的地方。在对项目进行 Dockerizing 之前，最好在虚拟环境中模拟运行您的代码，以便按顺序获取依赖项，并使用以下命令将依赖项导出到文件中:

```
$ pip freeze > requirements.txt
```

requirements.txt

下面的命令允许您在传递附加构建参数的同时构建 Docker 映像。在这个例子中，我们传递了允许我们对私有 PyPI 服务器进行身份验证的凭证细节。如果没有，可以省略这些参数。

```
$ docker build \
--build-arg ARTIFACTORY_USERNAME={YOUR_USERNAME} \
--build-arg ARTIFACTORY_SECRET_TOKEN={YOUR_SECRET_TOKEN} \
--no-cache  -t helloworld:latest .
```

`docker build`将生成以下输出:

Dockerfile 构建 pip

如果您想尝试一下`helloworld.py`示例，请确保您删除了 r `equirements.yml`或`environment.yml`文件中的 GAMS 和 CPLEX 条目，因为这两个包用于演示如何从私有 PyPI 服务器安装 pip。

执行`run`命令让您的`helloworld.py`运行起来:

```
docker container run helloworld
```

![](img/513b6776201e4a778737d3ac3da9d906.png)

执行`docker run helloworld`输出

# 康达之路

Conda 的方式与 pip 的方式略有不同，尽管在内部，Conda 将使用 pip 来解决它无法通过 Conda 包管理器解决的依赖关系。为了在 Anaconda 中使用 Docker，我们将利用 Miniconda Docker 映像。

```
FROM continuumio/miniconda3:4.7.12
```

这是 Anaconda 的最小版本，只包含 conda 及其依赖项。默认情况下，它包含 Conda 包管理器和 Python 3.7。使用 Miniconda，我们可以在同一个 Docker 映像中安装不同的 Python 版本。这在需要不同的数据科学微服务在不同版本的 Python 上工作的情况下是有意义的。让我们继续定义一个与 pip `requirements.txt`等价的`environment.yml`文件。

environment.yml

我们的`environment.yml`文件的 pip 部分可以包含一个私有的 pip 包或任何其他可能不受 Conda 支持的包。

pip 将自动解析在私有 PyPI 服务器中定义的包，如果从我们在`/etc/`文件夹中的`pip.conf`文件中定义的`extra-index-url`中找到的话。

Conda 通过使用[通道](https://docs.conda.io/projects/conda/en/latest/user-guide/configuration/use-condarc.html#config-channels)也有类似的概念。Conda 通道是存储包的位置——可以是解析为 URL 存储库的 Conda 定义的位置。它们是托管和管理包的基础。

```
RUN echo "[global]*\n*extra-index-url= https://$ARTIFACTORY_USERNAME:$ARTIFACTORY_SECRET_TOKEN@artifactory.com/api/pypi/simple" >> /etc/pip.conf
```

Conda 中的 Dockerfile 文件示例

执行我们之前在 pip 示例中使用的相同 Docker `build`命令将产生以下输出:

如果您在 Docker 构建过程中遇到过以下警告…

> **"警告**:您的环境文件中有 pip 安装的依赖项，但是您没有将 pip 本身列为您的 conda 依赖项之一。Conda 可能不会使用正确的 pip 来安装您的软件包，它们可能会出现在错误的位置。请添加一个显式 pip 依赖项。我在给你加一个，还在唠叨你。”

…那么这就是你如何去解决它。按照建议将 pip 添加到您的`environment.yml`文件中。在 Conda 的最新版本中，pip 被删除了——因此，您需要显式地添加它。

将 pip 配置为依赖项的 Conda environment.yml 文件

要运行您的 Docker 实例，请执行下面的命令，这是我们之前的 pip 变体的一个小变体。注意标志`-d`使您能够将 Docker 实例作为守护进程运行。如果忽略该标志，您的代码将不会触发并退出—因此，您需要通过按 Ctrl+Z 来中断正在运行的 Python 脚本，从而退出程序。

```
$ docker container run -d helloworld
```

如果需要使用其他 Python 包，可以使用 Conda search 命令，如下图所示。这假设您的 Docker 运行在守护进程状态。

```
$ docker ps# prints the list of running docker containers with their corresponding docker container ids$ docker exec -it {CONTAINER_ID} /bin/bash# command above allows you to enter the container by executing an interactive bash shell$ conda search python# above command list out all available python versions 
you may proceed to exit the container by typing. if you wish to export your package such as how pip freeze works you can achieve the same thing with the below command $ conda env export --from-history > environment.yml# --from-history allows exporting of top level dependencies. If you would like the full blown conda export remove this argument$ exit# Followed by$ docker stop {CONTAINER_ID}# which will terminate the executing docker instance.  
# Update the environment.yml with whichever python version you wish to use
```

# 结论

我希望您发现 Docker 练习对开始使用 Python 数据科学包的 Docker 探索有用。有时让所有的包都正常运行需要一些耐心——因为一些包可能有相互冲突的依赖关系。永远记住只安装你需要的 pip 或 conda 软件包。如果不使用它，就扔掉它，以尽量减少 docker 图像的膨胀。

**有用的工具**

*   [pipdeptree](https://github.com/naiquevin/pipdeptree) :查看您环境中软件包的依赖树
*   [检查-pip-依赖关系](https://github.com/dracos/check-pip-dependencies)或[pip-冲突-检查](https://github.com/ambitioninc/pip-conflict-checker) r:找到依赖关系冲突的根源
*   [pip-autoremove](https://pypi.org/project/pip-autoremove/) :通过列出顶级需求，帮助您从环境中删除未使用的孤立依赖项
*   [conda-depgraph](https://github.com/omegacen/conda-depgraph) :绘制 conda 依赖图的命令行实用程序
*   [peep](https://github.com/erikrose/peep) :加密保证包，确保你总是得到相同的未经篡改的 pip 包，有助于确保你的 docker 镜像的安全性

快乐编码。