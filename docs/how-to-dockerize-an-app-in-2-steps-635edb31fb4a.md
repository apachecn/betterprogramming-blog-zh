# 如何通过 2 个步骤将应用程序分类

> 原文：<https://betterprogramming.pub/how-to-dockerize-an-app-in-2-steps-635edb31fb4a>

## 使用 Docker 和 Node.js 启动容器化的应用程序

![](img/d16bce641501bc7952ddedf11a45f7f5.png)

弗兰克·麦肯纳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 要求

*   [码头工人](https://docs.docker.com/install/)
*   [Node.js](https://nodejs.org/en/)

如果您已经有了应用程序，请随意跳到步骤 2。

# 第一步。创建应用程序

对于这一部分，我们将使用 Express 创建一个基本的应用程序来生成一个简单的 web 服务器，而不使用视图引擎。

![](img/84f88c6278cccb4498348a760be5b370.png)

您应该得到以下文件结构:

![](img/9ae513fe26bdabee6d3ca722e952836d.png)

让我们对应用程序做一些更改。首先，我们将更改服务器正在监听的端口。

导航至`bin/www` *。*

改变这一点:

![](img/7b59584d9d4e2ac1fc5a59c3e6277e55.png)

对此:

![](img/3e3b8b96000aed71ecccc5a5e17b8a3a.png)

我们所做的只是将端口号从`3000`改为`8080`。

接下来，我们将编辑`/users` 路线的响应。导航到`routes/users.js`T21。

改变这一点:

![](img/afce8874eee46a268150e0922f5ec07c.png)

对此:

![](img/e0a15eb729ba196db8c0742de8108080.png)

现在，运行您的服务器:

![](img/a08e22711e66bd683683acd127cb7deb.png)

并在[邮差](https://www.getpostman.com/)上测试你的服务。

![](img/f1deb498eac31999459c80778f122221.png)

我们现在有一个工作服务！

# 第二步。创建 Docker 图像

首先，我们将向根目录添加一个 Dockerfile 文件。

![](img/def402c4898868383f321f113c983b8d.png)

您的 docker 文件应该如下所示:

![](img/b8377bc1be59adff91ba6a71665e0d30.png)

注意，`CMD [“node”, “./bin/www”]` 是运行应用程序的命令。如果您有不同的命令(如`node server.js` ) *，请更改该命令。*

接下来，在根目录下运行以下命令来构建并标记 Docker 映像。

![](img/828c6c70e39aa58b30c8e5eb6ab8a787.png)

您应该会得到这样的结果:

![](img/8aeba3373b8ee0ade0a2b7852dfe7523.png)

通过运行以下命令查看您的图像:

![](img/b1961cbf215b02ed9e06dc07851dc021.png)

您应该能够看到构建图像的列表。

![](img/bf9470f726195a99a83392ad72b705be.png)

现在，让我们运行容器！

![](img/bcacbdb7d6ec32485bd8dcd4336acaec.png)

这里，我们将服务器端口`8080`映射到容器端口`49160`。

让我们在 Postman 上测试我们的容器:

![](img/742f7b99a331a56f4536eedace1f773d.png)

太好了！

现在我们正在与 Docker 容器中的服务进行通信！