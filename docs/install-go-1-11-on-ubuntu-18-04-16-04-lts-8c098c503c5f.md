# 在 LTS 的 Ubuntu 18.04 和 16.04 上安装 Go 1.11

> 原文：<https://betterprogramming.pub/install-go-1-11-on-ubuntu-18-04-16-04-lts-8c098c503c5f>

## 利用急需的工具和组件

![](img/674d7411cf3de4f6d332fb0e51d3a833.png)

[Go](https://golang.org/) 1.11 于 2018 年 8 月 24 日发布，引入了几个真正需要的工具和组件。这包括版本化模块、实验性 WebAssembly 支持、调试改进以及对核心包和性能/运行时的微小更改。

这篇文章将介绍如何在你的 Ubuntu 18.04 LTS、16.04 LTS 和 14.04 LTS 系统上安装 Go 1.11。

## **1。安装 Go 语言**

升级以在 Ubuntu 上应用最新的安全更新。

```
sudo apt-get update
sudo apt-get -y upgrade
```

您需要下载 Go 二进制文件。你可以从他们的官方包中找到根据操作系统和架构的下载链接列表。要在 Ubuntu 64 位操作系统上安装它，请点击以下命令:

```
cd /tmp
wget [https://dl.google.com/go/go1.11.linux-amd64.tar.gz](https://dl.google.com/go/go1.11.linux-amd64.tar.gz)
```

现在，提取下载的归档文件，并将其安装到系统上的所需位置。我通常按照标准的建议把它放在`/usr/local`目录下。

```
sudo tar -xvf go1.11.linux-amd64.tar.gz
sudo mv go /usr/local
```

## **2。设置 Go 环境**

现在，让我们设置 Go 语言环境变量`GOROOT`、`GOPATH`和`PATH`。

`GOROOT` 是 Go 软件包在您系统上的安装位置。

`GOPATH` 是您工作目录的位置。例如，这里的目录是`~/go`。

打开您的`.profile`文件，并在文件末尾添加一个全局变量。根据您的 shell 配置，您可能希望将它添加到一个`.zshrc`或`.bashrc`文件中。

```
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```

## **3。更新当前 shell 会话**

```
source ~/.profile
```

这将允许您在不重启终端的情况下使用 go 命令。

## **4。验证安装**

您已经成功地在系统上安装并配置了 Go 语言。

要检查 Go 版本:

```
go version// go version go1.11 linux/amd64
```

现在，使用以下命令验证所有已配置的环境变量:

您已经成功安装了 Go 1.11。你现在可以用 Go 做一些很酷的东西。