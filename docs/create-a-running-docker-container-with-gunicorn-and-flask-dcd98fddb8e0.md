# 用 Gunicorn 和 Flask 创建一个正在运行的 Docker 容器

> 原文：<https://betterprogramming.pub/create-a-running-docker-container-with-gunicorn-and-flask-dcd98fddb8e0>

## 可以想象的最简单的容器设置。从这里开始

![](img/6ff6dff7061030597cba1baed2ea5449.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Mirko Blicke](https://unsplash.com/@mirkoblicke?utm_source=medium&utm_medium=referral) 拍摄的照片。

让我们从创建一个最小的 [Flask](https://flask.palletsprojects.com/en/1.1.x/) 应用程序开始:

简单烧瓶应用

接下来，让我们编写运行 [Gunicorn](https://gunicorn.org/) 服务器的命令:

参数几乎是不言自明的:我们告诉 Gunicorn，我们希望生成两个工作进程，每个进程运行两个线程。我们还接受来自外部的连接，并覆盖 Gunicorn 的默认端口(8000)。

我们的基本文档:

Dockerfile 文件

让我们建立自己的形象:

```
docker build -t flask/hello-world .
```

并运行:

```
docker run -p 8003:8003 flask/hello-world
```

因为我们发布了端口，所以现在可以访问该应用程序:

```
$ curl localhost:8003
Hello world!
```

# 通过 Minikube 使用本地 k8 运行

假设您的机器上已经运行了 Minikube，请按照下面的步骤操作。

为了使用本地 Docker 映像，您需要运行:

```
eval $(minikube docker-env)
```

请注意，您应该在想要使用的每个终端上这样做。

接下来，构建图像:

```
docker build -t marounbassam/hello-flask:v1 .
```

在`kubectl`上运行并公开部署:

```
$ kubectl run hello-flask --image=marounbassam/hello-flask:v1 --port=8003 --image-pull-policy=IfNotPresent
$ kubectl expose deployment hello-flask --type=NodePort
```

现在我们可以到达应用程序的终点了:

```
curl $(minikube service hello-flask --url)
```

# 奖金 Makefile

让我们创建一个简单的 Makefile，它允许我们构建、运行和删除我们的映像/容器:

生成文件

现在我们应该可以运行了:

```
# build Docker image
make build
# run the container
make run
# destroy it
make kill
```

# 摘要

将生产就绪的烧瓶应用程序容器化既快速又简单。通过添加一个 WSGI 服务器(Gunicorn ),我们的部署变得更加稳定，并且能够一次处理更多的请求——而且速度很快。