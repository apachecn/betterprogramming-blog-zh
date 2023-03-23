# 多通道使本地 K3s 群集变得简单

> 原文：<https://betterprogramming.pub/local-k3s-cluster-made-easy-with-multipass-108bf6ce577c>

![](img/04284264608ed007a06b6cde1e582a21.png)

几周前，我偶然发现了 [Multipass](https://multipass.run) ，这是一款可以让你在 Mac、Linux 或 Windows 工作站上几秒钟内启动 Ubuntu 虚拟机的工具。

根据您的操作系统，Multipass 本机使用 Hyper-V、HyperKit、KVM 或 VirtualBox 来实现最快的启动时间。

在这篇短文中，我们将在使用 Multipass 创建的虚拟机上设置 K3s Kubernetes 集群。

# 获得多次通过

只需前往 [Multipass](https://multipass.run/) ，为您的平台下载二进制文件。安装后，您可以获得可用命令的列表。在本文中，我们将只使用其中的几个。

```
$ multipass
Usage: multipass [options] <command>
Create, control and connect to Ubuntu instances.This is a command line utility for multipass, a
service that manages Ubuntu instances.Options:
  -h, --help     Display this help
  -v, --verbose  Increase logging verbosity, repeat up to three times for more
                 detailAvailable commands:
  delete    Delete instances
  exec      Run a command on an instance
  find      Display available images to create instances from
  get       Get a configuration option
  help      Display help about a command
  info      Display information about instances
  launch    Create and start an Ubuntu instance
  list      List all available instances
  mount     Mount a local directory in the instance
  purge     Purge all deleted instances permanently
  recover   Recover deleted instances
  restart   Restart instances
  set       Set a configuration option
  shell     Open a shell on a running instance
  start     Start instances
  stop      Stop running instances
  suspend   Suspend running instances
  transfer  Transfer files between the host and instances
  umount    Unmount a directory from an instance
  version   Show version details
```

# **虚拟机的创建**

首先，我们创建三个虚拟机，保留一个 CPU 和 1GB RAM 的默认配置。

```
$ multipass launch -n node1
$ multipass launch -n node2
$ multipass launch -n node3
```

接下来，我们验证这三个虚拟机是否处于运行状态

```
$ multipass list
Name         State             IPv4             Image
node3        Running           192.168.64.13    Ubuntu 18.04 LTS
node2        Running           192.168.64.12    Ubuntu 18.04 LTS
node1        Running           192.168.64.11    Ubuntu 18.04 LTS
```

**注意:**在本例中，我在 MacOS 上使用 Multipass，因此虚拟机在 HyperKit 上运行，因为我可以确认列出了 HyperKit 相关的进程。

```
luc@saturn:~$ ps aux | grep -i "[h]yperkit"
... com.docker.hyperkit ...
... com.canonical.multipass/bin/hyperkit...
... com.canonical.multipass/bin/hyperkit...
... com.canonical.multipass/bin/hyperkit...
```

在这些进程中，第一个是已经在我的机器上运行的 Docker 守护进程。接下来的三个是上面创建的虚拟机。

# 初始化节点 1 上的 K3s

首先，我们在`node1`上运行一个命令(使用 Multipass 的 *exec* 子命令)来安装在[文档](https://k3s.io)中定义的 K3s。

```
$ multipass exec node1 -- \
  bash -c "curl -sfL https://get.k3s.io | sh -"
```

**注意:**K3s 的初始化用了不到 30 秒…对于一个认证的 Kubernetes 发行版来说，真的令人印象深刻。

然后，从本地机器上，我们收集向集群添加额外节点所需的元素:

*   加入集群的令牌

```
$ TOKEN=$(multipass exec node1 sudo cat /var/lib/rancher/k3s/server/node-token)
```

*   运行在`node1`上的 API 服务器的 IP

```
$ IP=$(multipass info node1 | grep IPv4 | awk '{print $2}')
```

# 将节点 2 和节点 3 加入集群

从本地机器上，我们运行以下命令将`node2`和`node3`添加到集群中。

```
# Joining node2
$ multipass exec **node2** -- \
bash -c "curl -sfL https://get.k3s.io | K3S_URL=\"https://$IP:6443\" K3S_TOKEN=\"$TOKEN\" sh -"# Joining node3
$ multipass exec **node3** -- \
bash -c "curl -sfL https://get.k3s.io | K3S_URL=\"https://$IP:6443\" K3S_TOKEN=\"$TOKEN\" sh -"
```

正如我们所看到的，这些命令使用我们上面定义的令牌和 IP 地址。

# 获取集群配置

运行以下命令，我们可以列出集群的所有节点:

```
$ multipass exec node1 -- sudo kubectl get nodes
NAME    STATUS   ROLES    AGE     VERSION
node1   Ready    master   5m31s   v1.16.2-k3s.1
node3   Ready    <none>   21s     v1.16.2-k3s.1
node2   Ready    <none>   45s     v1.16.2-k3s.1
```

要从我们的本地机器访问集群的 API 服务器，我们只需要在设置过程中获取在`node1`创建的`kubeconfig`文件。

```
$ multipass exec node1 sudo cat /etc/rancher/k3s/k3s.yaml > k3s.yaml
```

然后我们需要更改`server`键，使它引用`node1`的远程 IP 地址，而不是本地主机。

```
sed -i '' "s/127.0.0.1/$IP/" k3s.yaml
```

最后，我们配置我们的本地 kubectl，以便它使用我们刚刚检索的 kubeconfig 文件(k3s.yaml)。一个简单的方法是设置`KUBECONFIG`环境变量，使其指向配置文件。

```
export KUBECONFIG=$PWD/k3s.yaml
```

我们都设置好了，现在我们可以从您的本地机器与集群通信。

```
$ kubectl get nodes
NAME STATUS ROLES AGE VERSION
node1 Ready master 10h v1.16.2-k3s.1
node2 Ready <none> 10h v1.16.2-k3s.1
node3 Ready <none> 10h v1.16.2-k3s.1
```

注意:本文中定义的所有步骤都可以根据以下要点运行:

[https://gist . github . com/lucj/5a 0e 2286 b 40130d 02388 a 264 e 6924 ed 4](https://gist.github.com/lucj/5a0e2286b40130d02388a264e6924ed4)

## 摘要

多遍是一个非常有用的工具。它与低级虚拟机管理程序的集成使其成为在本地部署多个虚拟机以及使用许多分布式解决方案(如伟大的 K3s Kubernetes 发行版)的良好选择。