# 整理 Python 应用程序时需要掌握的 6 个概念

> 原文：<https://betterprogramming.pub/6-concepts-to-master-when-dockerizing-python-applications-e5f5a6a87845>

## 映像变体、增量构建、内部缓存等。

![](img/c0da2feddc1c1323e5cafa2de206e71b.png)

照片由 [Ragnar Vorel](https://unsplash.com/@sonuba?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

不久前，我开始尝试为 Python 应用程序构建极简容器图像的各种方法。因此，我想分享一些我一路走来学到的技巧和诀窍。

本文涵盖了以下关键概念:

*   图像变体
*   工作目录和结构
*   增量构建
*   内部缓存
*   Pip 安装缓存
*   Pip 从 setup.py 安装

# 1.图像变体

除了 Python 版本，Docker Hub 中还托管了它的多个变体。

## 标准

开发人员通常基于他们想要的 Python 版本来使用 defacto 图像变体。它表示如下:

```
FROM python:<version>
```

例如，您将在 Python 版的 docker 文件中使用以下代码行:

```
FROM python:3.7
```

## 微小的

除此之外，还有另一个叫做`slim`的变种，它只包含运行`Python`所需的最小软件包。以下是命令:

```
python:<version>-slim
```

如果您的应用程序不需要外部依赖，并且只使用 Python 就可以开箱即用，那么这个变体是一个不错的选择。

## 阿尔卑斯山的

如果你关心的主要是最终图像的大小，你应该使用基于 Alpine Linux 项目的`alpine`版本。基本映像大约 5MB，比其他任何发行版都小。

```
python:<version>-alpine
```

不过这个镜像内部用的是 [musl libc](https://musl.libc.org) 而不是 [glibc 和 friends](https://www.etalabs.net/compare_libcs.html) 。这可能是一个问题，因为大多数 Linux 发行版都是基于标准 C 库(glibc)的 GNU 版本。因此，您将需要编译和构建大多数重要的库，比如来自源代码的`matplotlib`。这可能会导致各种不必要的问题，并导致最终的图像要大得多，如果你使用其他变体。

## windowsservercore

对于那些使用 Windows 10 专业版/企业版(周年纪念版)或 Windows Server 2016 的用户，可以选择`windowsservercore`版本，使用以下代码:

```
python:<version>-windowsservercore
```

有关在 Windows 上运行 Docker 的更多信息，请查看官方文档:

*   [Windows Server 快速入门](https://msdn.microsoft.com/en-us/virtualization/windowscontainers/quick_start/quick_start_windows_server)
*   [Windows 10 快速入门](https://msdn.microsoft.com/en-us/virtualization/windowscontainers/quick_start/quick_start_windows_10)

# 2.工作目录和结构

容器图像具有不同的文件夹结构，其中之一如下:

```
.
├── app
│   ├── __init__.py
│   └── main.py
├── Dockerfile
└── requirements.txt
```

在这个结构中，`app`文件夹包含了您的项目的所有相关文件和文件夹。随后，您的 docker 文件应该如下所示:

```
FROM python:3.7WORKDIR /appCOPY ./requirements.txt requirements.txtRUN pip install -r /requirements.txtCOPY ./app /appCMD ["python", "main.py"]
```

有些开发人员会将项目文件放在另一个子目录中。比如下面是官方 FastAPI 文档提供的 [DockerFile](https://fastapi.tiangolo.com/deployment/docker/#dockerfile) :

```
FROM python:3.9WORKDIR /codeCOPY ./requirements.txt /code/requirements.txtRUN pip install --no-cache-dir --upgrade -r /code/requirements.txtCOPY ./app /code/appCMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "80"]
```

该 DockerFile 将`code`文件夹设置为工作目录，并通过`app.main:app`运行主文件。一般来说，这个 docker 文件在大多数用例中应该工作得很好。但是，如果在应用程序中调用自定义 Python 模块，以这种方式编写 DockerFile 会导致相对导入问题，如下所示:

```
ImportError: Cannot import name <module>
```

如果您使用标准的文件夹结构，`app`文件夹应该是工作目录，您将运行`main:app`。这有助于解决相关的导入问题。

# 3.增量构建

Docker 容器是从上到下递增构建的。在每一行代码上，它都会添加一层。后续层将被添加到彼此之上。层数越多，最终图像就越大。因此，您应该尽量减少 RUN 命令的数量，并尽可能将它们作为一个命令堆叠起来。例如，您的 docker 文件中没有两个不同的 RUN 命令，如下所示:

```
RUN python -m pip install -U pip
RUN pip install -r requirements.txt
```

您应该将它们合并成一个，如下所示:

```
RUN python -m pip install -U pip && pip install -r requirements.txt
```

# 4.内部缓存

到目前为止，您应该已经注意到需求文件和项目文件是分开复制的，而不是从一开始就复制所有的东西。DockerFile 文件编写如下:

*   复制需求文件
*   安装 Python 包
*   复制应用程序文件

```
COPY ./requirements.txt requirements.txtRUN pip install -r /requirements.txtCOPY ./app /app
```

Docker 有自己的内部缓存，如果文件自上次构建以来没有变化，它将重用同一层。因为需求文件和安装不会经常改变，所以您可以用这种方式编写 DockerFile，以便在每次重新构建容器映像时重用相同的层。因此，您将节省大量时间，因为您不再需要等待`pip install`进程运行。

# 5.Pip 安装缓存

此外，每次通过 pip install 安装软件包时，pip 都会存储自己的缓存。这个缓存通常对 Docker 映像没有用。因此，您应该使用`--no-cache-dir`标志来运行您的命令，以防止任何缓存。

```
pip install --no-cache-dir -r requirements.txt
```

这样做的话，容器图像的尺寸会稍微减小。

# 6.Pip 从 setup.py 安装

并非所有 Python 包都可以直接从 pypi 安装。有些存储库要求您克隆存储库并通过 setup.py 安装它。

请看下面的示例命令，该命令用于通过 setup.py 克隆存储库并安装软件包:

```
git clone --recursive https://github.com/user/package.git
cd package-master && pip install .
```

因此，您需要在 docker 文件中添加代码 install git 依赖项，如下所示:

```
RUN apt-get -y install git
```

然后，运行以下命令:

*   克隆存储库
*   切换到包含 setup.py 的工作目录
*   安装软件包
*   移除文件夹，因为我们不再需要它

```
RUN git clone --recursive https://github.com/user/package.git && cd package-master && pip install --no-cache-dir . && rm -R /app/package-master
```

或者，您可以预先在项目文件中克隆存储库，并像往常一样复制它，如下面的代码所示:

```
COPY ./package-master package-master
RUN cd package-master && pip install --no-cache-dir . && cd .. && rm -r package-master
```

这种方法减少了安装 git 依赖项的需要，这将减少最终的映像大小。缺点是，每当有更新并且您希望使用最新版本时，您需要将存储库重新克隆到您的项目文件中。

# 结论

让我们回顾一下你今天所学的内容。

本文首先提供了 DockerHub 中提供的 Python 图像的不同变体的例子。

然后，介绍了工作目录和文件夹结构。它概述了将项目文件放在子目录中的缺点。

它继续深入解释了 Docker 的增量构建和内部缓存特性。随后，它还提到了通过在 pip 安装期间删除缓存和预先克隆存储库来减小容器映像大小的技巧。

感谢阅读。祝你有美好的一天！

# 参考

1.  [DockerHub — Python 官方图片](https://hub.docker.com/_/python/)
2.  [集装箱中的 FastAPI 码头工人](https://fastapi.tiangolo.com/deployment/docker/)