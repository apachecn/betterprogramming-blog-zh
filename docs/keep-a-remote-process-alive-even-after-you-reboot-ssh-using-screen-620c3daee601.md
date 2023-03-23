# 保持远程进程活动，即使在使用 Screen 重启 SSH 之后

> 原文：<https://betterprogramming.pub/keep-a-remote-process-alive-even-after-you-reboot-ssh-using-screen-620c3daee601>

## 重新启动客户端计算机，并且仍然可以访问正在运行的进程

![](img/ef07156c04973e24ff4e8643dc13f689.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Sai Kiran Anagani](https://unsplash.com/@_imkiran?utm_source=medium&utm_medium=referral) 拍摄的照片。

很有可能你现在正在家里工作。这意味着你的家用机器可能不够强大，无法完成某些任务。为了解决这个问题，你将不得不连接到你的办公机器，但是即使你在你的机器上开发你的项目，在 2020 年你将不可避免地需要使用云。无论是数据收集、模型训练还是部署，您都需要它。

您可能会使用云的原因之一是，您想要运行的流程很长。有时，这个过程甚至会持续几天。你并不总是希望你的机器连续运行这么长时间。

使用 [tmux](https://github.com/tmux/tmux/wiki) 可以在关闭 SSH 连接后保持您的终端进程运行。这有时甚至比其他选择更好。

但是假设您想在重启客户端后让进程在远程计算机中保持活动，那么最好使用[屏幕](https://www.gnu.org/software/screen/)。

我将通过描述我所做的来解决我的问题，为整个过程提供一个教程。

# 问题是

我正在创建一个数据集，这个过程的一部分需要将近 33 个小时来运行。我不想也不能用我的电脑来完成这项任务。但是我有一台远程电脑。如果我通过一个简单的 SSH 连接运行这个脚本，这个进程将会被 SSH 连接终止。

所以我在想办法解决，结果用了 Screen。

# 解决方案

首先，您需要通过 SSH 连接到远程计算机:

```
ssh user@remote.adresss
```

然后你必须安装屏幕:

```
sudo apt install screen
```

您可以通过键入以下命令来启动会话:

```
screen -S session_name
```

现在，您可以执行任何您想要的脚本，并启动您想要运行的进程。当一切运行时，您可以通过按 CTRL + A + D 来分离会话。

这个过程仍然在运行，你可以自由地退出 SSH，做任何你想做的事情。

你可以随时回来重新联系。当您重新连接时，您可以通过键入以下命令列出所有正在运行的会话并找到您想要重新连接的会话:

```
screen -ls
```

然后重新连接到所需的会话:

```
screen -r session_name
```

当您不再需要会话时，您可以键入:

```
screen -XS session_name quit
```

杀了它。

这就是我能够在 33 小时内访问运行过程的方式，而不用一直打开我的笔记本电脑。