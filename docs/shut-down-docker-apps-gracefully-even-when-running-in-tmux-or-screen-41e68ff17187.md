# 优雅地关闭 Docker 应用程序，即使在 tmux 或 Screen 中运行

> 原文：<https://betterprogramming.pub/shut-down-docker-apps-gracefully-even-when-running-in-tmux-or-screen-41e68ff17187>

## 当应用程序在 tmux 或 Screen 中运行时，处理信号以终止它们

![](img/b83a7aa6438710ea3d9cbb67c1c18713.png)

[小胡子仙人掌](https://unsplash.com/@mustachescactus?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/exit?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

# 信号如何工作

首先，简单的事实:`docker stop`和`kubectl delete`向容器的 PID 为 1 的进程发送一个`TERM`信号。如果在 Kubernetes pod 中运行多个容器，每个容器都会收到信号。

`TERM`信号告诉进程终止，大多数应用程序像预期的那样处理它:它们关闭。

但是在你的`entrypoint.sh`中，还有其他应用程序通常使用的有用信号和你可以用于自己目的的信号。

*   `HUP`通常用于向程序发出重新加载配置的信号
*   `USR1`和`USR2`通常用于打印统计数据或状态信息
*   `INT`当您在终端中点击 C-c 以中断进程时发送

当您在子进程或 tmux 或 Screen 会话中运行实际的应用程序时，您可以捕获信号并向您的应用程序发送自定义命令来关闭它，或者打印统计数据或其状态。

```
#!/bin/bashtrap 'myapp --reload' HUP
trap 'tmux send-keys status C-m' USR1
trap 'screen -X stuff $(echo -e "stats\r\n")' USR2
```

当你用`entrypoint.sh`中的`exec`启动你的应用时，事情看起来就不一样了。使用`exec`会导致进程接管当前进程，这意味着您的`entrypoint.sh`不再运行。它甚至不会执行脚本的其余部分。这也意味着您在`entrypoint.sh`中设置的任何`trap`不再存在。你的应用程序必须处理信号。

# 需要知道的事情

*   `docker stop`在`KILL`激活您的 1 号 PID 之前只需等待十秒钟。
*   `kubectl delete`默认为发出`KILL`信号前 30 秒，可通过`terminationGracePeriodSeconds`进行配置。
*   `tmux send-keys`允许发送特殊字符，更方便自动化:`tmux send-keys Hello C-m My name is DrPsychick C-m`。
*   `tmux kill-server`是杀死所有`tmux`会话的最快方法。
*   `screen -X stuff "..."`让你直接输入数据到屏幕标准输入，就像你已经输入了一样。
*   `echo -e "\r\n"`可作为`stuff` : `screen -X stuff $(echo -e "quit\r\n")`屏幕的`ENTER`。
*   `kill -TERM 0`向组中的所有进程发送信号(所有子进程和父进程本身)。
*   `kill -TERM -1`向 PID 大于 1 的所有进程发送信号。
*   `kill -TERM %1`(在`bash`中)发送信号到**第一个**后台工作
*   `exec myapp`将使`myapp`接管 1 号 PID，它必须自己处理信号。

# 做中学

我学到的:

*   最好简单地使用`bash`或`sh`作为入口点脚本(如果有的话)。它们会自动清理它们的子进程。知识共享系统
*   另一种方法是直接启动一个处理信号的二进制程序，这将是现代服务的情况，但它也应该“收获”僵尸儿童。
*   如果您无法避免运行需要终端的应用程序，请使用`tmux`。
*   `trap`实施快速，易于理解。它可以调用脚本中定义的函数，这使得它非常强大。你还需要什么？

这里是我的小 Docker 图像 repo 来玩陷阱和信号。它包括用`docker`和`kubectl`运行它的例子(在我的例子中运行`minikube`)。

它运行一个`tmux`会话和一个`screen`会话，这两个会话都写入一个共享的日志文件，这个日志文件被`tail`打印到 stdout。每一个共同的信号都被捕获并触发不同的动作。

[](https://github.com/DrPsychick/docker-signal-demo) [## DrPsychick/docker-信号-演示

### 在 docker 容器演示/游乐场中处理信号，在 docker / kubernetes 中处理信号…

github.com](https://github.com/DrPsychick/docker-signal-demo) 

# 摘要

*   您可以在任何容器中对`TERM`做出反应，并且在 Kubernetes 环境中，您可以配置宽限期来确保您的应用程序在被终止之前干净地关闭。
*   您可以对其他信号做出反应，并在您的应用程序中触发自定义操作或命令。
*   您可以轻松地将信号传递给子流程，或者向您的`tmux`和`screen`会话发送命令。

感谢您的宝贵时间！

# 进一步阅读

为什么你应该坚持把`bash`叫做`entrypoint.sh`:

[](https://blog.phusion.nl/2015/01/20/docker-and-the-pid-1-zombie-reaping-problem/) [## Docker 和 PID 1 僵尸收割问题

### 在构建 Docker 容器时，您应该意识到 PID 1 僵尸收割问题。那个问题会导致…

blog.phusion.nl](https://blog.phusion.nl/2015/01/20/docker-and-the-pid-1-zombie-reaping-problem/) 

使用`my_init`翻译信号，摘自本文:

[](https://tasdikrahman.me/2019/04/24/handling-singals-for-applications-in-kubernetes-docker/) [## 为在 kubernetes 中运行的应用程序处理信号

### 当基于 linux 的系统中的设备断电时，人们可以想出处理这一事件的方法…

塔斯迪克拉赫曼.我](https://tasdikrahman.me/2019/04/24/handling-singals-for-applications-in-kubernetes-docker/) 

`my_init:`[https://github . com/phusion/base image-docker/blob/rel-0 . 9 . 19/image/bin/my _ init](https://github.com/phusion/baseimage-docker/blob/rel-0.9.19/image/bin/my_init)

受到 Marco Pracucci 等人和优秀文章的启发。

 [## 用 Kubernetes 优雅地关闭豆荚

### 由于自动扩展策略、pod 或部署删除，Marco Pracucci Docker 容器可以随时终止…

pracucci.com](https://pracucci.com/graceful-shutdown-of-kubernetes-pods.html) [](https://cloud.google.com/blog/products/gcp/kubernetes-best-practices-terminating-with-grace) [## Kubernetes 最佳实践:优雅终止|谷歌云博客

### 在容器出现之前，大多数应用程序运行在虚拟机或物理机上。如果一个应用程序崩溃了，它需要相当长的时间…

cloud.google.com](https://cloud.google.com/blog/products/gcp/kubernetes-best-practices-terminating-with-grace) 

*   **画面:**[https://man7.org/linux/man-pages/man1/screen.1.html](https://man7.org/linux/man-pages/man1/screen.1.html)
*   **tmux:**https://man7.org/linux/man-pages/man1/tmux.1.html
*   **信号和陷阱:**[https://www.tutorialspoint.com/unix/unix-signals-traps.htm](https://www.tutorialspoint.com/unix/unix-signals-traps.htm)
*   **kill[%]PID:**[https://stack overflow . com/questions/14197470/how-does-trap-kill-work-in-bash-on-Linux](https://stackoverflow.com/questions/14197470/how-does-trap-kill-work-in-bash-on-linux)