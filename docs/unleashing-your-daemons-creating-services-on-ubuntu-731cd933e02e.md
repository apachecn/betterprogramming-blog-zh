# 释放你的守护进程:在 Ubuntu 上创建服务

> 原文：<https://betterprogramming.pub/unleashing-your-daemons-creating-services-on-ubuntu-731cd933e02e>

## 这不是黑魔法，但也够接近了

![](img/b336b0dcaa1b34f6a0984b8b1a4cdc6e.png)

切斯特·韦德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

最近，我联系了几家公司，询问他们今年夏天是否在招聘新的实习生。我还没有完成计算机科学学位，但是我想感受一下大学以外的真实世界。作为一项挑战，其中一家公司要求我构建并部署一个小型 RESTful API。

构建 API 非常简单——没有什么是一个雄心勃勃的计算机系学生不能处理的。一旦我完成了对最终产品的润色，我就通过 SFPT 把我的 API 的源代码上传到我希望部署它的服务器上。然后我 SSH 说服务器并运行 API，它工作了！但是我一退出服务器，API 就停止了。在公司审查我的 API 之前，我无法一天 24 小时保持与服务器的 SSH 会话。我需要 API 在服务器上自主运行。

我需要释放一个精灵。

虽然释放一个守护进程听起来很奇特和令人兴奋，但我很抱歉地告诉你，它实际上恰恰相反。我们在这里要对抗的守护进程(或服务)只是后台进程，它们自己运行，不需要用户的交互。然而，如果我说当我的 API 作为后台进程运行时，我感觉自己就像一个恶魔猎手，这并不夸张。

玩笑归玩笑，如果你知道自己在做什么，创建一个守护进程是一件非常简单的事情。让我们开门见山吧！

# 创建服务

所有服务都位于`/etc/systemd/system`中，因为它们依赖于`systemd`提供的库来正常运行。为了创建一个服务，我们必须在那个目录中创建一个`.service`文件，可以用这个命令来完成:

```
$ sudo nano /etc/systemd/system/mydaemon.service
```

`mydaemon`应为您的守护进程的名称。现在让我们用下面的内容填充`.service`文件:

```
[Unit]Description=My first daemon![Service]User=<yourUser>#Code to execute
#Can be the path to an executable or code itselfWorkingDirectory=/home/ubuntu/mydaemonExecStart=/home/ubuntu/mydaemon/executableType=simpleTimeoutStopSec=10Restart=on-failureRestartSec=5[Install]WantedBy=multi-user.target
```

不要忘记将`<yourUser>`改为您服务器上的实际用户！注意`WorkingDirectory`和`ExecStart`是如何指向同一个文件夹的。您的服务必须保持这种方式才能正确运行。目前，该目录不存在。让我们创造它！

# 为服务创建 Bash 脚本

首先，让我们为可执行文件创建一个目录。该目录应为`WorkingDirectory`中指定的目录:

```
$ mkdir mydaemon
```

现在进入目录，让我们创建我们的可执行文件！

```
$ cd mydaemon
$ sudo nano executable
```

可执行文件必须包含以下标题:

```
#!/bin/sh
```

否则，它不会作为 bash 脚本运行。在这个文件中，编写启动服务的指令。在我的例子中，当我部署一个 Java 应用程序时，我的可执行文件如下所示:

```
#!/bin/sh
java -jar myAppi.jar
```

现在，给可执行文件正确的权限是非常重要的。文件的权限规定了谁可以读取、写入和执行该文件。由于服务是由操作系统在后台运行的，所以我们必须将读取和执行权限授予“其他用户”这可以通过以下命令完成:

```
$ sudo chmod 005 executable
```

现在唯一剩下的就是召唤我们的精灵了！

# 正在启动服务

与召唤一个真正的恶魔不同，这可以使用以下命令轻松完成:

```
$ sudo systemctl daemon-reload$ sudo systemctl enable mydaemon$ sudo systemctl start  mydaemon
```

这样，您的服务应该可以正常运行了！您可以通过以下方式检查您的服务状态:

```
$ sudo systemctl status mydaemon
```

如果您的服务有任何问题，您可以尝试使用服务日志进行调试，可以通过以下方式访问:

```
$ sudo journalctl --unit=mydaemon
```

# 最后的想法

服务是在服务器上部署应用程序的关键，我希望本教程能够让您释放守护进程并成功部署应用程序。

召唤快乐！

感谢您的阅读。