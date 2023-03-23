# Docker 容器重启备忘单

> 原文：<https://betterprogramming.pub/5-brilliant-ways-to-docker-restart-containers-successfully-1a8dc22022f2>

## 重启 Docker 容器的 5 种简单方法

![](img/723fe8405e22c8af1d5613194ccb8681.png)

拉尔夫·哈特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

让我们像专家一样运行 Docker 容器。

首先，学习如何利用这五个 Docker 重启容器技巧来保持 Docker 容器的运行。

开始将 Docker 容器作为单个进程运行需要几个命令。

# **1。如何列出每个正在运行的 Docker 容器**

```
docker ps
```

# **2。如何列出(所有)正在运行的 Docker 容器**

```
docker ps -a
```

# **3。如何使用 Docker 分离模式将每个 Docker 容器作为后台进程运行**

```
docker run -d 
```

# **4。如何运行每个 Docker 容器并将输出分配给你的终端？**

```
docker run -d -p 80:80 --name <IMAGE_NAME> -t <IMAGE_NAME>
```

# **5。如何在退出后重启 Docker 容器？**

```
docker update --restart unless-stopped <CONTAINER_ID>
```

这是一笔大交易。如果您的实例/服务器关闭，您的容器现在将自动启动！

```
docker update --restart unless-stopped $(docker ps -q)
```

让我们确保列出的所有集装箱都得到相同的待遇。参见上面的例子。我们将结合我们在前几个技巧*中学到的内容。*

# 6.清理你的 Docker 系统文件

```
docker system prune 
```

在提示符下输入-Y 来清理 docker 文件

```
WARNING! This will remove:- all stopped containers- all networks not used by at least one container- all dangling images- all build cacheAre you sure you want to continue? [y/N] y
```