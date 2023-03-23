# 使用 Syslog 集中您的 Docker 日志记录

> 原文：<https://betterprogramming.pub/docker-centralized-logging-with-syslog-97b9c147bd30>

## 集中记录

## 了解我们的系统及其成功或失败的最佳方式是通过大量的日志记录

![](img/73e0138dc0967c73892e5bf16618aa9a.png)

由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

# **背景**

在我最近几年参与的一个项目中，我的任务是将一个集中式日志记录系统与我们使用的应用程序堆栈集成在一起(遵循微服务架构)。我想到的第一个想法是构建一个 ELK (Elasticsearch，Logstash，Kibana)堆栈来完成这项任务。然而，事实证明，由于项目资源的限制，这不是一个选项。

因此，我调查了所有可以帮助我实现这个特性而又不与我们的局限性相冲突的选项和想法。我最终提出了以下想法:

*   直接收集 Docker 日志。
*   使用系统日志收集日志。

正如我已经说过的，第一个想法是建立一个 ELK 栈来收集日志。然而，由于该项目的预算和资源限制，这不是一个选项。

另一个想法是将日志转发到托管服务，如 [logz.io](https://logz.io/) 。这一选择也是不可能的，因为生产服务器没有互联网接入，再加上同样的预算限制。

第三个想法是设置一个`Logrotate`脚本，它执行以下操作来生成日志文件:

*   从`/var/lib/docker/containers/*`收集 Docker 日志。
*   用容器名而不是 id 来重命名文件。
*   压缩日志文件。

这个想法的第二步是建立一个 Cron 作业，将日志文件复制到一个中央服务器，在那里可以加载和检查日志。

这个想法有很多缺点，更像是重新发明轮子。最大的缺点之一是，如果进行新的部署和替换容器，日志将会丢失。

这就是为什么我开始寻找一种替代解决方案，帮助我们将日志从服务器收集到一个集中的服务器，而不会丢失日志。幸运的是，Docker 支持多个日志驱动程序(完整列表见)。其中一个日志驱动程序是 Syslog，它默认安装在 Linux 系统上(不需要安装额外的软件)。出于这个原因，我决定用 Syslog 实现一个集中式日志系统。

# **实施**

仅为了简单起见，我们假设托管微服务的基础架构由以下节点组成:

*   托管服务的双节点 Docker 集群。
*   一个节点用于存储日志。

## 日志服务器配置

为了配置日志服务器并准备好从 Docker 主机收集日志，我必须执行以下步骤:

*   确保安装了 Syslog，或者使用以下命令安装它:

```
$> yum update -y
$> sudo yum install rsyslog rsyslog-doc
```

*   列出正确的 TCP 端口。系统日志配置文件`/etc/rsyslog.conf`中必须有下面一行:

```
$ModLoad imtcp $InputTCPServerRun 514
```

*   更新系统日志和`journald`的日志速率限制配置。下面的配置应该添加到`/etc/rsyslog.conf`文件中，以避免日志消息过多时日志丢失。更多信息见[系统日志文件](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imjournal.html)。

```
$imjournalRatelimitInterval 0
$imjournalRatelimitBurst 0
```

*   另外，需要在`journald`配置文件`/etc/systemd/journald.conf`中将速率限制配置设置为`0`。

```
RateLimitInterval=0
```

默认情况下，Syslog 会将所有日志存储在`/var/log/messages`中。但是，我们希望根据容器名称来分离日志，以便于调查。要配置 Syslog 服务器将不同容器中的日志分离到不同的文件中，我们需要执行以下步骤:

*   决定并创建保存所有日志的文件夹。

```
$> mkdir /var/log/dockerlfs
```

*   收集 Docker 守护进程的日志，并将其保存到磁盘。下面的 Syslog 配置规则(存储在`/etc/rsyslog.d/docker_daemon.conf`中)将 Syslog 保存所有属于从`docker`启动到`/var/log/dockerlfs/daemon.log`的程序的日志。

*   收集 Docker 容器日志，并将其保存到单独的日志文件中。下面的系统日志配置规则将根据容器名称，为每个正在运行的容器将日志保存到单独的文件中。规则应该存储在`/etc/rsyslog.d/docker_container.con`中。该规则依赖 Docker 主机用`container_name`标记所有日志。

下一步是设置一个`Logrotate` 规则来循环日志，避免服务器上的大日志文件。旧日志可以由系统管理员存档。下面的规则将每天轮换服务器上的日志，或者如果文件大小超过 20MB。它还将只保留每个容器的最后 30 个日志文件。

作为服务器配置的最后一步，我们需要重启服务以确保我们的配置被加载。

```
$> systemctl restart rsyslog
$> systemctl restart systemd-journald
```

# 配置 Docker 主机

现在我们已经完成了 Syslog 服务器的配置，我们可以继续并开始配置 Docker 主机，以将日志从服务器转发到 Syslog 服务器。

需要做的第一件事是重新配置 Docker 守护进程，使用`syslog`日志驱动程序代替`journald`，并用容器名标记日志。为了实现这个目标，我们需要修改位于`/etc/docker`下的 Docker 守护进程配置文件。`daemon.json`应包括以下内容:

变量`SYSLOG_SERVER_IP`应该替换为系统日志服务器 IP。变量`ENV_NAME`应该替换为环境名称(测试、试运行或生产)。使用上面的配置，Docker 会将日志直接转发到 Syslog 服务器。如果系统日志服务器关闭或者没有有效的连接，我们可能会丢失一些上面配置的日志。

为了改进我们的日志记录系统并避免丢失日志，我们将进行以下更改:

*   将日志从 Docker 转发到本地服务器。
*   配置本地系统日志，将日志转发到集中式日志服务器。

要将日志从 Docker 转发到本地 Syslog 服务器，我们只需从`/etc/docker/daemon.json`中删除下面一行，或者用`127.0.0.1`替换`SYSLOG_SERVER_IP`:

```
"syslog-address": "tcp://${SYSLOG_SERVER_IP}:514",
```

一旦我们完成了 Docker 配置，我们需要重启 Docker 守护进程。

```
$> systemctl restart docker
```

下一步是更新 Docker 节点上的 Syslog 配置，以便能够在本地存储 Docker 日志，并将它们转发到中央服务器。

**系统日志和日志配置**

我们需要文件`/etc/systemd/journald.conf`中`journald`的以下配置项:`RateLimitInterval=0`。

我们需要文件`/etc/rsyslog.conf`中 Syslog 的以下配置项:

```
$ActionQueueFileName fwdRule1
$ActionQueueSaveOnShutdown on
$ActionQueueType LinkedList
$ActionResumeRetryCount -1$imjournalRatelimitInterval 0
$imjournalRatelimitBurst 0
```

*   `ActionQueueFileName` : 为假脱机文件添加唯一名称前缀。
*   `ActionQueueSaveOnShutdown` : 关机时将消息保存到磁盘。
*   `ActionQueueType`:异步运行。
*   `ActionResumeRetryCount`:*I*n 如果主机关闭，则限制重试次数。

使用上面的配置，Syslog 将继续尝试向中央服务器发送日志，直到日志被目标服务器捕获。然而，到目前为止，我们还没有在 Docker 主机上配置本地 Syslog 服务器来将日志转发到任何其他服务器。因此，从 Docker 收集的所有日志都将保存在默认文件`/var/log/messages`中。

将日志从 Syslog 转发到另一台服务器非常简单。您只需要在`/etc/rsyslog.conf`文件的末尾添加以下规则，并用有效的日志服务器 IP 替换`SYSLOG_SERVER_IP`。这个规则主要检查所有的日志，并根据标签和程序名过滤它们。如果消息标签包含名为`container_name`或`programname`的标签，则从`docker`开始。然后将日志转发给`SYSLOG_SERVER_IP`。

我们可以通过在 Docker 服务器上本地保存日志的副本来改进上面的规则。通过下面的更改，我们的系统日志规则会将日志发送到远程系统日志服务器，并且会在运行 Docker 的各个服务器上本地保存一份日志文件的副本。

因为我们现在在 Docker 服务器上有了日志文件的副本，所以添加`Logrotate`规则来轮换这些文件是有意义的。我们可以使用用于服务器节点的相同规则。

最后，Syslog 和`journald`都需要重启才能获得新的配置。

```
$> systemctl restart rsyslog
$> systemctl restart systemd-journald
```

# 结论

集中式日志记录系统和堆栈可以通过许多不同的方法和工具来实现。Syslog 就是其中一种方法——当运行环境存在一些限制，并且缺乏管理和运行 ELK 等更复杂堆栈的经验时，它尤其有用。

[](https://levelup.gitconnected.com/centralize-your-docker-logging-with-fluentd-a2b7e0a379ce) [## 使用 FluentD 集中您的 Docker 日志记录

### 使用 FluentD 开始将容器日志传送到集中的日志服务器

levelup.gitconnected.com](https://levelup.gitconnected.com/centralize-your-docker-logging-with-fluentd-a2b7e0a379ce)