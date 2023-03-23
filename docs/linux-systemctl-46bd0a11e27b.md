# 了解 Linux 系统 ctl

> 原文：<https://betterprogramming.pub/linux-systemctl-46bd0a11e27b>

## 如何在 Linux 中使用 **systemctl 命令**

![](img/e96009de14be001c9994e71733a46fff.png)

[TBIT](https://pixabay.com/es/photos/contador-cinta-grabadora-de-cassette-949233/) 在 [Pixabay](https://pixabay.com/es/photos/contador-cinta-grabadora-de-cassette-949233/) 上的照片

下面是来自维基百科的 Systemd 的定义:

> Systemd 是一个软件套件，它为 Linux 操作系统提供了基本的构建模块。它包括 systemd“系统和服务管理器”，一个用于引导用户空间和管理用户进程的 init 系统。systemd 旨在跨 Linux 发行版统一服务配置和行为。

在你对 **Systemd** ，**，**着迷之前，也看看反对 Systemd 的[论据。我个人觉得很好管理。由于我并不精通 Linux，它帮助我少犯错误，并且它防止我因为懒惰和不熟悉而错过做一些事情。我使用一个名为 **systemctl 的命令。检查你的 Linux 发行版是否支持它。**](http://without-systemd.org/wiki/index.php/Arguments_against_systemd)

```
# which systemctl
/bin/systemctl
```

# 便捷的命令

## 启动应用程序

```
sudo systemctl start application
#Example
sudo systemctl start nginx
```

## 停止应用程序

```
sudo systemctl stop application
#Example
sudo systemctl stop nginx
```

## 重启应用程序

```
sudo systemctl restart application
#Example
sudo systemctl restart nginx
```

## 重新加载应用程序

```
sudo systemctl reload application
#Example
sudo systemctl reload nginx
```

## 检查状态

```
sudo systemctl status application
#Example
sudo systemctl status nginx
```

## 状态输出示例

```
**●** nginx.service - A high performance web server and a reverse proxy serverLoaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)Active: **active (running)** since Wed 2019-01-30 11:29:16 IST; 1 weeks 0 days agoDocs: man:nginx(8)Main PID: 909 (nginx)Tasks: 2 (limit: 1152)CGroup: /system.slice/nginx.service├─909 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;└─911 nginx: worker processJan 30 11:29:15 ubuntu-s-1vcpu-1gb-blr1-01-monitoring systemd[1]: Starting A high performance web server and a reverse proxy server...Jan 30 11:29:16 ubuntu-s-1vcpu-1gb-blr1-01-monitoring systemd[1]: nginx.service: Failed to parse PID from file /run/nginx.pid: Invalid argumentJan 30 11:29:16 ubuntu-s-1vcpu-1gb-blr1-01-monitoring systemd[1]: Started A high performance web server and a reverse proxy server.
```

## **检查是否激活**

如果您正在使用类似 [Zabbix](https://medium.com/@gokulnk/understanding-zabbix-f2a83eeb1221) 的监控服务，并且需要检查服务是否处于活动状态，您可以使用:

```
# systemctl is-active nginxactive
```

# 准备重启

尽管我们采取了所有的预防措施，还是会有事情失败的情况。由于调试可能会花费更多的时间，并可能使您的应用程序或服务停止运行，我们可能会被迫重启我们的服务器。因此，确保重启时一切正常至关重要。

## 列出所有加载的单元

```
systemctl list-units -all | grep loaded | awk '{print $1;}'
```

## 列出所有启用的设备

```
systemctl list-unit-files| grep enabled | awk '{print $1;}' > enabled.txt
```

大多数时候，我们需要确保我们使用的所有服务都在启动脚本中。

## 列出所有加载的服务

```
systemctl list-units -all | grep service | grep loaded | awk '{print $1;}'
```

## 列出所有启用的服务

```
systemctl list-unit-files | grep service | grep enabled | awk '{print $1;}' > enabled.txt
```

要找到已加载但未启用的服务列表，我们可以执行以下操作:

```
systemctl list-units -all | grep service | grep loaded | awk '{print $1;}' > loaded.txt
systemctl list-unit-files | grep service | grep enabled | awk '{print $1;}' > enabled.txt
diff -y loaded.txt enabled.txt
#If you want a quick glance of missing ones you can also use
diff -y loaded.txt enabled.txt | grep '<'
```

我知道这不是万无一失的，但它能让您快速浏览丢失的服务。

# Docker，Ansible 等人。

确保您的服务器即使在重新启动后也能正常工作，让您晚上睡得更香。

我们正在考虑将我们的工作流程转移到 Docker 和 Ansible。这需要一些时间。在那之前，我们将使用一个通用的设置、像这样的工具和一个像 [Zabbix](https://staging.app.learningpaths.io/#/highlights) 这样的监控工具。希望对你也有帮助。