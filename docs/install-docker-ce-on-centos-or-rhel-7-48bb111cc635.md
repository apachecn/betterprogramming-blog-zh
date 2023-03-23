# 在 CentOS 和 RHEL 7 上安装 Docker CE

> 原文：<https://betterprogramming.pub/install-docker-ce-on-centos-or-rhel-7-48bb111cc635>

## 在 CentOS 和 RHEL 7 上安装 Docker CE 的作弊脚本

![](img/dd883633d55c4401cf5a33ec556a55d3.png)

# 在 CentOS 7 上安装

确保您的系统是最新的:

```
$ sudo yum update -y
```

下载并执行 Docker 团队正式提供的安装脚本:

```
$ curl -fsSL https://get.docker.com/ | sh
```

完成了。

# 安装在 RHEL 7 上

确保您的系统是最新的:

```
$ yum update -y
```

与 CentOS 不同，您不能使用官方安装脚本在 RHEL 7/8 上安装 Docker CE，因为这是不允许的。当您尝试这样做时，会得到以下错误:

```
RHEL is only allowed to use Docker EE.
```

因此，您需要付出更多的努力来解决这个问题。

完成了。

# 设置权限并启用 Docker 服务

为了能够使用 Docker，您必须将您的用户添加到一个`docker`组中，以给予他们适当的权限:

```
$ sudo usermod -a -G docker $USER
```

然后在启动时启动并启用 Docker 服务(如果需要):

```
$ sudo systemctl start docker
$ sudo systemctl enable docker
```

# 作弊脚本

完成上述所有步骤后，我编写了两个单独的脚本来简化安装。复制粘贴时间快乐！