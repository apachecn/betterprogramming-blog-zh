# 如何在 CentOS 7 上安装 Git v2

> 原文：<https://betterprogramming.pub/install-git-v2-on-centos-7-49448deede19>

## 在 CentOS 7 上安装并替换默认的过时 Git 版本

![](img/8ebf82b4a8fedb3c80469d0335a4ec8d.png)

[斯蒂夫·约翰森](https://unsplash.com/@steve_j?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/cent?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

尽管 CentOS 7 是最受欢迎的 Linux 发行版之一，但它的大多数软件包都已经过时了，包括一些重要的软件包，比如 Git。CentOS 7 基本存储库中提供的 Git 版本是 v1.8，而许多现代编程工具或语言需要 Git v2+才能运行。

在本文中，我将带您完成安装和用最新版本替换过时的 Git 版本所需的简单步骤。

# 安装 IUS 知识库

IUS 库提供了一些软件的新版本(Git 等)。)在官方 CentOS 存储库中。

IUS 资源库中的软件包与官方资源库中的软件包命名不同，这有助于避免无意的冲突或软件版本更新。

```
$ sudo yum install -y [https://centos7.iuscommunity.org/ius-release.rpm](https://centos7.iuscommunity.org/ius-release.rpm)
```

# 安装 yum-插件-替换

该插件提供了一个`yum replace`命令，用于替换指定的包，同时安装任何所需的依赖项。

我用它来用 IUS 仓库中提供的新版本替换旧的 g it 版本。

```
$ sudo yum install -y yum-plugin-replace
```

# 安装 Git v2 并替换 Git v1

IUS 存储库以名称`git2u-all`提供 Git v2。使用`yum replace`命令将其替换为默认包`git`:

```
$ sud yum replace -y git --replace-with git2u-all
```

就是这样！请访问`git version`查看更新。