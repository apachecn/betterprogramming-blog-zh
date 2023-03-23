# 鲜为人知但功能强大的 Unix 命令来提高您的技术技能

> 原文：<https://betterprogramming.pub/lesser-known-but-powerful-unix-commands-to-polish-your-tech-skills-b2ceec3e078a>

## 带有简单示例的简便 bash 命令

![](img/5f94b30fb485dcdc5919ec660ae2f5db.png)

[阳光](https://unsplash.com/@ray027?utm_source=medium&utm_medium=referral)照在[的 Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上。

在我们的软件工程旅程中，我们已经看到 bash 命令是多么强大。在这个快速教程中，我将向您展示一些可能会派上用场的有用命令。

# 否认命令

在进入`[disown](https://en.wikipedia.org/wiki/Disown_(Unix))`命令之前，让我们了解一下它与`[nohup](https://linux.die.net/man/1/nohup)`的关系。

我想你们大多数人已经熟悉了`nohup`命令。对于那些不是的人来说，它代表“不挂电话”它阻止正在运行的进程接收`[SIGHUP](https://en.wikipedia.org/wiki/SIGHUP)` 信号。这样，即使您关闭终端会话，您的脚本仍将运行。

`nohup`的一个常见用法:想象你需要在远程服务器上执行一个长时间运行的脚本。如果您失去了与服务器的连接，您的脚本将会停止。这可能非常令人沮丧！为了避免这个潜在的问题，您可以利用`nohup`命令。

例如:

```
$ nohup ./your-script.sh & 
```

注意`nohup`没有把进程放在后台。您可以在最后提供`ampersand (&)`命令来实现这个结果。

`disown`和`nohup`有什么关系？

假设您已经在没有`nohup`的情况下启动了远程服务器上的脚本。然后你意识到用`nohup`运行它会更明智，因为执行会花费很长时间。

这里是`disown`命令来救援的时候。它是一个[内置的](https://en.wikipedia.org/wiki/Shell_builtin) bash 命令，帮助您将一个已经运行的进程放在`nohup`下。您可以从活动任务表中删除任务或标记任务，以保护它们不受`SIGHUP`信号的影响。

它接受三个参数:

*   `-a` —如果没有传递作业 ID，则删除所有作业。
*   `-h` —标记每个作业 ID，以便在 shell 接收到`SIGHUP`信号时，`SIGHUP`不会被发送到作业。
*   `-r` —删除处于“正在运行”状态的作业。

## 使用演示

为了演示该命令的用法，创建一个名为`show-time.sh`的测试文件，并粘贴以下内容:

```
*#!/bin/bash* while true; do
sleep 5
date +"Hi, the current time is %F %T"
done
```

通过运行以下命令使脚本可执行:

```
$ chmod +x show-time.sh
```

启动它以查看输出:

```
$ ./show-time.sh
```

在最初的五秒钟延迟后，你会每五秒钟看到当前的日期和时间。

让我们看看如何否认该流程，使其在`nohup`下运行:

*   通过按键盘上的`Ctrl + Z`暂停正在运行的脚本:

```
$ ./show-time.sh
Hi, the current time is 2020–11–30 22:07:03
[1]+  Stopped                 ./show-time.sh
```

*   键入`bg`，一个[作业控制](https://en.wikipedia.org/wiki/Job_control_(Unix)#Commands)命令，将进程带到后台:

```
$ bg
```

*   使用`jobs`命令列出活动作业，包括进程 ID:

```
$ jobs -l
[1]+ 15791 Stopped                 ./show-time.sh
```

*   如果是第一个作业，执行`disown -h {jobid}`或`disown -h %1`:

```
$ disown -h 15791
```

*   您可以验证作业是否已不在作业表中:

```
$ jobs -l
```

该命令不会给你任何结果。

*   关闭终端窗口并打开一个新窗口。检查作业是否仍在运行:

```
$ ps -e | grep 'show'
15791 ?        00:00:00 show-time.sh
```

如您所见，即使我们终止了终端，该进程仍在运行！

# at 命令

有时，我们希望定期运行作业。为此，我们将使用`[crontab](https://en.wikipedia.org/wiki/Cron)`。但是，如果我们只需要在指定的时间执行一次作业呢？`[at](https://en.wikipedia.org/wiki/At_(command))`命令提供了一个简单的解决方案。

如果您的系统上没有安装，您可以从终端安装。

*   对于 Ubuntu/Debian 发行版:

```
sudo apt install at
```

*   对于 CentOS/Fedora 发行版:

```
sudo yum install at
```

## 使用演示

*   让我们从现在开始安排两分钟的`show-time.sh`脚本:

```
$ at now + 2 minutes -f show-time.sh
job 10 at Mon Nov 30 22:44:00 2020
```

请注意，您也可以使用`pipe`命令来获得相同的结果:

```
$ ./show-time.sh | at now + 2 minutes
```

您也可以安排命令而不是脚本:

```
$ echo “hello world” > at.txt | at now + 3 minutes
```

三分钟后，这个命令应该会创建一个名为`at.txt`的新文件，并将“hello world”写入该文件。

检查输出:

```
$ cat at.txt
hello world
```

# 批处理命令

如果您想在系统负载足够低(通常低于 1.5)时调度和运行一个任务，那么`[batch](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/batch.html)`命令非常有用。您可以通过调用`atd`守护进程来修改默认的负载值。

## 使用演示

让我们用另一个例子来看看`batch`是如何工作的。

*   执行`top`命令检查系统负载:

```
$ batch
top — 23:11:26 up 4:22, 1 user, **load average: 0,31**, 0,42, 0,46
```

我建议为`batch`命令使用一个单独的终端标签。这样可以观察系统负载，做实验。

*   执行`batch`命令。系统会提示您提供操作。您可以在这里指定多个命令。为了简单起见，让我们将“hello world”打印到一个名为`hello.txt`的新文本文件中，然后创建一个空的`tst.txt`文件:

```
$ batch
at> echo "hello world" > hello.txt
at> touch tst.txt
at> <EOT>
job 14 at Mon Nov 30 23:07:00 2020
```

请注意，当您准备退出命令提示符时，您必须按`Ctrl + D`。

*   让我们检查文件是否已经创建:

```
$ ls
hello.txt tst.txt
```

系统负载足够低，所以文件创建时没有任何问题。

# 摘要

我们已经看到了为什么`disown`、`at`和`batch`命令很方便以及如何使用它们。

关键要点:

*   `disown`从作业表中删除作业，这样即使当前 shell 终止，您的进程也是活动的。
*   `at`计划在特定时间只运行一次的作业。
*   `batch`当系统资源不足时执行作业。

我建议浏览文档，了解更多关于这些命令的特性。

我希望你喜欢这个教程，并能把你的新知识应用到实践中。感谢您的阅读和快乐编码！