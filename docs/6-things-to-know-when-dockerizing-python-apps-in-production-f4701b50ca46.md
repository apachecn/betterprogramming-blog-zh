# 在生产中整理 Python 应用程序时需要知道的 6 件事

> 原文：<https://betterprogramming.pub/6-things-to-know-when-dockerizing-python-apps-in-production-f4701b50ca46>

## 在生产环境中使用 Python 运行 Docker 容器的清单

![](img/dc2eecf5f7fccc91ca6c03bbb1cc1865.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 J. Kelly Brito 拍摄的照片。

在[的上一篇文章](https://medium.com/better-programming/docker-for-node-js-in-production-b9dc0e9e48e0)中，我写了一份在生产环境中使用 Docker 运行 Node.js 应用程序的清单。

最近忙着开发 Python 应用。如果你将 Node.js 和 Docker 与 Python 和 Docker 进行比较，你会发现很多东西是相同的。但是也有值得注意的差异。

因此，我重写了我的清单。这个是为了在生产环境中用 Python 运行 Docker 容器。

你可以在这个 GitHub 库中找到所有的例子[。](https://github.com/PatrickKalkman/python-docker)

# 1.选择正确的基础图像

为您的 Python 应用程序选择正确的基本 Docker 映像至关重要。这取决于应用程序，但请始终尝试使用[官方 Docker 图像](https://hub.docker.com/_/node)，因为它们拥有出色的文档，使用最佳实践，并且是为最常见的用例而设计的。

看官方 [Python Docker 图片](https://hub.docker.com/_/python)的话还是有很多图片可以选择的。大多数时候，我选择最小尺寸的图像来运行我的 Python 应用程序。如果您想在 64 位 Linux 上运行 Python 3.8 应用程序，以下是一些选项:

*   python:3.8-buster→**883 MB**
*   **python:3.8-瘦克星→ 114 MB**
*   **python:3.8-alpine → 43 MB**

**Docker 团队将 Alpine 映像基于 Alpine Linux 发行版，这是一个带有强化内核的最小 Linux 发行版。如果你的应用程序在 Alpine 上工作，你应该用它作为你的基本图像，这样可以得到最小的图像尺寸。**

# **2.使用非根容器用户**

**默认情况下，您的应用程序以 root 用户身份在容器内运行。但是，容器中的根与主机上的根不同。Docker 限制容器中的用户。但是为了减少安全攻击面，您可能希望尽可能以无特权用户的身份运行容器。**

**一些官方映像已经在它们的 Docker 映像中创建了一个非 root 用户，但是大多数情况下，您必须自己在 Docker 文件中添加用户。有关添加和使用 Python 用户的示例，请参见下面的 docker 文件:**

**以非 root 用户身份添加和运行 Python 应用程序**

**在第一行，我使用 python:3.7.8-alpine 图像作为基础图像。第 6 行的语句添加了 Python 用户。第 9 行的指令为我的应用程序创建了目录。我显式地将新的目录所有者设置为 Python 用户，因为我仍然作为 root 用户运行。**

**`WORKDIR`指令设置工作目录并创建文件夹。但是，没有办法设置目录所有者。**

**我在第 12 行将用户设置为 Python 用户。`USER`指令在运行映像时设置用户名(或 UID)。Dockerfile 选择用户后，我执行`pip install`来安装我的应用程序的依赖项。该命令将以 Python 用户的身份运行。在第 22 行，我将应用程序的源代码复制到工作文件夹中。**

**在最后一行，应用程序使用了`CMD`指令。这提供了执行容器的缺省值。**

# **3.启动和停止 Python 应用程序**

**在 Docker 容器中以正确的方式启动和停止 Python 应用程序至关重要。在之前显示的 Dockerfile 文件中，我使用`CMD`指令启动应用程序。**

**当您使用 exec 形式的`CMD`指令启动 Python 应用程序时，您需要确保能够从应用程序内部的操作系统(例如`SIGINT`和`SIGTERM`)接收信号，并处理它们以正常关闭应用程序。**

**如果你忽略这些信号并停止你的容器，Docker 将等待十秒钟(默认超时)让你的应用程序响应。如果你的应用没有响应，它会终止你的 Python 进程并停止容器。因此，您要做的第一件事就是对`SIGINT`和`SIGTERM`命令做出反应，然后优雅地关闭您的应用程序:**

**通过对 SIGINT 和 SIGTERM 做出反应，优雅地停止您的 Python 应用程序**

**上面的代码示例显示了如何实现对`SIGINT`和`SIGTERM`信号的响应。在这个例子中，我通过调用`stop()`退出进程，但是这也是清理资源的地方。**

**如果您无法访问源代码或不想更改应用程序的源代码，有两种不同的方法可以关闭您的应用程序。**

**首先是使用`--init`。这个标志向 Docker 表明它应该使用一个 init 进程作为容器中的`PID 1`。启动容器时，可以这样使用它:**

```
docker run --init -d yourpythonappimage
```

**然后你的容器将直接对`Ctrl-C`或`Docker stop`命令做出反应。**

**另一个更持久的选择是将 [tini](https://github.com/krallin/tini) 添加到您的 docker 文件中，并将其包含在您的图像中。这也是当你使用`--init`标志时 Docker 在后台做的事情。在您的映像中安装 tini，并使用`ENTRYPOINT`启动和包装`CMD`。请参见下面的示例:**

**带蒂尼的 Dockerfile**

# **4.健康检查**

**Docker 文件中的`HEALTHCHECK`指令告诉 Docker 如何验证容器是否仍在工作。这可以检测到这样的情况，例如 web 服务器陷入无限循环，即使服务器进程仍在运行，也无法处理新的连接。**

**您必须实现执行健康检查的功能，因为 Docker 不知道您的应用程序何时正常运行。我通常为我的服务器添加一个不同的路由，专门用于处理健康请求。**

**有几个包可以帮助用 Python 实现健康检查。我在下面列出了两个:**

*   **[健康检查](https://pypi.org/project/health-check/)**
*   **[py-健康检查](https://pypi.org/project/py-healthcheck/)**

**这些包中的大多数实现了健康检查的接收端。您仍然需要将实际的请求添加到 Dockerfile 文件中。如果您的 Python 应用程序中有一个 HTTP 端点，那么您可以使用`curl`来执行健康检查。例如，通过使用下面的`HEALTHCHECK`命令:**

```
HEALTHCHECK --interval=21s --timeout=3s --start-period=10s CMD curl --fail http://localhost:3000/ || exit 1
```

# **5.从 Python 应用程序记录日志**

**在 Docker 容器中运行时，从 Python 应用程序进行日志记录很简单:根据具体情况，记录到`stdout`或`stderr`。**

**这背后的基本原理是让其他东西来处理日志记录。让其他东西记录日志是有意义的，因为我们通常在微服务架构中使用 Docker 容器，在微服务架构中，您可以在多个服务之间分配职责。**

**我不推荐使用`print`从你的 Python 应用程序中写入`stdout`或`stderr`。相反，我会利用标准库中可用的优秀日志功能。**

**如果您仍然使用`print`函数来记录日志，请使用`-u`参数启动 Python。这确保 Python 不会缓冲消息。因此，在 Docker 中启动 Python 应用程序的命令如下所示:**

```
CMD ["python", "-u", "main.py"]
```

# **6.使用环境变量的配置**

**当你的应用在 Docker 容器中运行时，Docker 希望你使用环境变量来完成所有的配置。有一个名为 [python-dotenv](https://pypi.org/project/python-dotenv/) 的优秀库可以提供帮助。**

**这个库从名为`.env`的文件中读取所有的键值对，并将它们添加到环境变量中。在您的 Python 应用程序中，您可以像读取标准环境变量一样读取它们。**

**一个`.env`文件包含许多键值对，每一个都在单独的一行上:**

```
# development settings
DB_ADDRESS=localhost:2323
DB_TIMEOUT=4000
LOG_LEVEL=DEBUG
```

**阅读和使用`dotenv`很简单。请参见下面的示例:**

**使用 dotenv 进行阅读环境设置**

**在第 10 行，`.env`被加载。在`.env`文件中定义的环境变量`LOOP`被加载并存储在`self.loops`中。当我们使用`os.getenv`读取变量时，我们也指定了一个默认值。您可以使用这些默认变量来设置开发值。**

# **结论**

**这些是我在 Docker 容器中运行 Python 应用程序的技巧。你可以在 GitHub 库中找到所有的例子。**

**感谢您的阅读。如果你有更多的建议，请告诉我。我喜欢学习。**