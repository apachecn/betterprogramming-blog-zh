# 如何在容器中运行一个简单的 Flask 应用程序

> 原文：<https://betterprogramming.pub/how-to-run-a-simple-flask-application-in-a-container-691fbbd91fcf>

## 面向各种操作系统的开发人员

![](img/afcb23c4267248157d17dff641f36e93.png)

照片由[威廉·胡克](https://unsplash.com/@williamtm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

您正在测试一个理论，并希望快速启动一个 Python 应用程序容器。也许您只是在学习如何将一个简单的 Python 应用程序容器化。欢迎阅读这篇文章，在这里我们将做到这一点。

第一件事是保持我们家的秩序。在您的工作站上安装 Python。我假设您使用的是基于 Unix 的系统。

创建一个文件夹来托管应用程序，并创建第一个`main.py`文件。因为我们不想将我们的应用程序与系统范围的 Python 库混合在一起，所以安装 pipenv 使我们能够在当前目录下安装这个应用程序的包。然后我们将使用 pipenv 安装 flask、gunicorn 和 requests 包。这将创建一个包含应用程序依赖关系的 Pipfile。

```
$ mkdir singlepageflask $ touch main.py#install pipenv
$ python3 -m pip install pipenv# install dependencies; flask, gunicorn and requests packages
$ python3 -m pipenv install flask gunicorn requests
```

我们将创建一个入口脚本，在您想要运行此应用程序时使用。

```
$ touch appentry.sh
$ chmod +x appentry.sh
```

添加以下内容，以便在脚本运行时启动 gunicorn wsgi 服务器。gunicorn 命令只是调用`main.py`文件中的 app 变量，并监听`port 8000`上的所有地址。

```
#!/bin/bash
python3 -m gunicorn main:app -w 2 --threads 2 -b 0.0.0.0:8000
```

通过运行`./appentry.sh`进行本地测试。

测试 docker 文件在本地运行后，创建一个 docker 文件。

注意，在我们的`appentry.sh`中，我们声明我们的应用程序应该从任何地址`0.0.0.0`监听。这将使容器能够接受来自外部的请求。

使用 Podman 构建应用程序并启动容器，同时将主机`port 8000`映射到容器`port 8000`。

```
#build the container image
$ podman build -t singlepage .#start a container using the image and map to host on port 8000
$ podman run -d -p 8000:8000 --name singlepage singlepage#Test the endpoint;
$ curl http://localhost:8000/jokeme
```

通过向`[http://localhost:8000/jokeme](http://localhost:8000/jokeme)`发送请求，使用 curl 命令或您最喜欢的 API 测试应用程序从您的工作站测试应用程序。

你可以在我位于 [branch singleflask](https://github.com/Wainaina3/publications.git) 的 git 仓库中找到完整的代码。

对于 Windows 用户，您可能会遇到容器找不到`appentry.sh`脚本的问题。这是因为 windows 使用 CRLF 行尾，而 Unix(容器是基于 Unix 的)使用 LF 行尾。

因此，当您在 windows 上创建脚本并尝试在容器上运行它时，它无法正确解释脚本，从而导致令人困惑的“没有找到这样的文件或目录”错误。

要纠正这一点，请将`appentry.sh`脚本的行尾从 CRLF 转换为 LF。