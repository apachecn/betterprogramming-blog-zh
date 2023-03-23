# 从零开始的 Kubernetes(第二部分)

> 原文：<https://betterprogramming.pub/kubernetes-from-scratch-part-2-e30b48f7ca6b>

## 不含 Minikube 或 MicroK8s 的 Kubernetes

![](img/32d0d0b0a856a5efe63c51e9dbabbddb.png)

Sven Mieke 在 [Unsplash](https://unsplash.com/s/photos/architecture-plans?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在本系列的文章“从零开始的 Kubernetes”中，我讨论了一个最小的 Kubernetes 系统。现在我想通过使它成为一个更完整的系统来增加这一成功。

如果你从云提供商那里获得 Kubernetes，像存储和入口这样的东西很可能会被提供。核心的 Kubernetes 系统不提供像 Ingress 这样的东西，因为这应该与运行它的云系统紧密集成。

要继续学习，您应该已经从头开始阅读了"[Kubernetes](https://medium.com/better-programming/kubernetes-from-scratch-4691283e3995)"并构建了所描述的系统。我们构建的系统是在裸机服务器上的虚拟机中运行的四个节点。只要你有一个类似的设置，你应该可以跟着做一些小的调整。集群节点被命名为`kube1`、`kube2`、`kube3`和`kube4`。这个`kube1`节点是主人，其余的是工人。主机名为`beast`，运行的是 Ubuntu 20.04，虚拟机运行的是 Ubuntu 18.04。

本文的后半部分还需要一个我们在我的文章“[构建您自己的家庭云存储](https://medium.com/better-programming/build-your-own-in-home-cloud-storage-1aa74b5c6397)”中构建的存储服务器该服务器在裸机上运行 Ubuntu 20.04，并安装了 GlusterFS。

本文旨在与云提供商无关。它不是生产系统的蓝图。如果你试图建立一个生产系统，坚持使用云提供商提供的工具。本文通过看到组成系统的各个部分，并带走一点魔力，来了解 Kubernetes 的内幕。您对 Kubernetes 的运营方式了解得越多，您在与云提供商合作时就能做出更好的决策。

在我们开始添加系统之前，让我们测试一下我们现在运行的系统的各个部分。我的前一篇文章只测试了 Kubernetes API 的工作情况，并且可以通过`kubectl`命令访问。我们应该看看我们是否能实际部署一些东西。我有一句简单的“你好，世界！”图像应该适合测试。因此，回到主主机，创建一个名为`test.yaml`的文件，并添加以下内容:

```
kind: Deployment
metadata:
  name: hellok8s-deployment
  labels:
    app: hellok8s
spec:
  selector:
    matchLabels:
      app: hellok8s
  template:
    metadata:
      labels:
        app: hellok8s
    spec:
      containers:
      - name: hellok8s
        image: docker.io/rlkamradt/hellok8s:latest
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: hellok8s-service
spec:
  type: ClusterIP
  selector:
    app: hellok8s
  ports:
  - port: 8080
    targetPort: 8080
```

现在用命令`kubectl create -f test.yaml`部署它。用`kubectl get all`应该能看到。

```
rkamradt@beast:~$ kubectl get all
NAME                                      READY   STATUS    RESTARTS   AGE
pod/hellok8s-deployment-85fdc9d4f-s5z4q   1/1     Running   0          13mNAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/hellok8s-service   ClusterIP   10.102.67.208   <none>        8080/TCP   13m
service/kubernetes         ClusterIP   10.96.0.1       <none>        443/TCP    4d22hNAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hellok8s-deployment   1/1     1            1           13mNAME                                            DESIRED   CURRENT   READY   AGE
replicaset.apps/hellok8s-deployment-85fdc9d4f   1         1         1       13m
```

您可能需要等待几秒钟，直到 pod 状态为`Running`。现在我们如何测试服务呢？通常你会通过负载平衡器或入口服务来公开它，但是我们还没有这些东西。幸运的是，你可以使用`kubectl`的`port-forward`子命令。

```
rkamradt@beast:~$ kubectl port-forward service/hellok8s-service 8080:8080
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080
Handling connection for 8080
```

此命令在您完成转发之前不会返回，因此您必须打开一个新的终端来测试服务。

```
rkamradt@beast:~$ curl [http://localhost:8080](http://localhost:8080)
Hello World
```

现在回到原来的终端窗口，Ctrl-C 结束端口转发。

我在上一篇文章中创建的 Kubernetes 集群只有两个节点。从那时到现在，我重复了 worker 节点的指令来创建另外两个节点，因此我当前运行的系统有一个控制平面节点和三个 worker 节点。

我们的 pod 运行在哪个节点上？我们可以使用`kubectl`的`describe`子命令来了解一下。运行`kubectl get pods`找到 pod 的确切名称及其描述:

```
rkamradt@beast:~$ kubectl describe pod hellok8s-deployment-85fdc9d4f-s5z4q
Name:         hellok8s-deployment-85fdc9d4f-s5z4q
Namespace:    default
Priority:     0
Node:         kube3/192.168.122.223
...
```

我的节点被命名为`kube1-4`，所以这个节点运行在第二个 worker 节点上。您可以使用 pod 规范中的`nodeSelector`属性，使它总是在一个节点上运行，尤其是如果一个节点具有像额外内存或 SSD 驱动器这样的独特功能。但是大多数时候，让系统决定如何在节点间分配工作是有好处的。

让我们看看是否能让所有的节点都工作起来。用这个命令`kubectl scale --replicas=3 deployment hellok8s-deployment`放大服务的副本。

现在运行`kubectl get all`,直到您在就绪栏中看到 3/3。在每个 pod 上运行`describe`应该显示 pod 均匀分布在节点上。如果您将服务进行端口转发，它应该对每个节点进行循环调度。不幸的是，我的`hellok8s`应用程序没有记录每个请求，所以我们不能只通过查看日志来判断。我敢肯定，一些 Kubernetes 大师知道我们如何通过一些其他方式来告诉，但我们会假设它的工作，并处理检查后。

我看到的第一个问题是，我们不想为我们提供的每项服务都运行端口转发。有几种方法可以解决这个问题，如果你使用的是云提供商，他们应该能够为你解决这个问题。但是我们是在裸机上运行，所以我们必须想出自己的解决方案。

永久公开服务的两种方法是负载平衡(不要与 Kubernetes 服务自动为您提供的负载平衡混淆)和入口。Ingress 通常仅支持 HTTP/HTTPS，但支持高级功能，如虚拟主机或基于路径的路由和 SSL 终端。负载平衡工作在 TCP 层，因此它可以公开数据库和消息队列之类的东西。

过去，我使用了一个负载平衡器，并在主机上设置了一个 nginx 反向代理。这让我两全其美，尽管这意味着手动配置 nginx，这并不十分困难。首先，让我们在集群上安装一个负载平衡器。我对 [MetalLB](https://metallb.universe.tf/) 有些熟悉，所以让我们看看是否可以在我们的系统上安装并运行它。

首先，如果您在云提供商而不是裸机上运行本练习，您需要跳过这一部分。MetalLB 与大多数云提供商不兼容，这是有充分理由的:云提供商提供他们自己的负载平衡器。请参考您的云提供商的文档，以创建可访问外部 IP 的负载平衡器。

安装 MetalLB 并不太复杂:应用几个 YAML 文件，并做一些配置。

但是首先，根据 MetalLB 文档，“如果你在 IPVS 模式下使用 kube-proxy，由于 Kubernetes v1.14.2，你必须启用严格的 ARP 模式。”我不知道那是什么意思，但我认为把你的 ARP 设置成严格模式不会有什么坏处。因此，通过`kubectl edit configmap -n kube-system kube-proxy`编辑 kube-proxy 的配置图，并将`ipvs.strictARP`设置为`true`。最终，这种改变会在整个系统中发挥作用。然后安装 MetalLB:

```
kubectl apply -f [https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/namespace.yaml](https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/namespace.yaml)
kubectl apply -f [https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/metallb.yaml](https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/metallb.yaml)
kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
```

然后你就可以看到所有用`kubectl get all -n metallb-system`创造出来的新东西。上面的第一行创建了一个名称空间`metallb-system`，所以要访问所有的片段，您必须使用带有`-n metallb-system`的名称空间。值得注意的一点是所有的扬声器豆荚创建，每个节点一个。它创建这些路由来向网络通告它创建的所有路由。这是现代网络魅力的一部分。

我们到底在用上面的命令做什么？我们使用 MetalLB 提供的定义文件将自己配置为系统的负载平衡器。现在，任何类型为`LoadBalancer`的服务都将自动获得一个 IP 地址，该地址被路由到我们安装原始系统时创建的 VM 桥(参见我的文章“[使用 VM 和 Kubernetes](https://medium.com/better-programming/playing-with-vms-and-kubernetes-26ef93019c22) ”)。你可以用`wget`上面的 YAML 文件看看是怎么回事。

在配置之前，MetalLB 一直处于休眠状态。为了配置它，我们需要一个在名称空间`metallb-system`中名为`config`的配置映射。创建一个名为`metallbconfig.yaml`的文件，并输入以下内容:

```
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 192.168.122.240-192.168.122.250
```

现在应用带有`kubectl apply -f metallbconfig.yaml`的配置。

我们使用`layer2`配置，并使用`192.168.122.0/24`的桥接网络给出一个地址池，最后一个数字范围为 240-250。我不确定 KVM 如何从那个网络分配，所以我玩得有点危险。但是在我的服务器冒烟之前，我只能期待最好的结果。如果你在生产中这样做，你可能会想调查一下。实际上。如果你在生产中这样做，我会因为你没有使用云提供商而解雇你。总之，我们有 11 个 IP 地址用于公开服务。

这是我们第二次使用`ConfigMaps`，我想提出它的用法。它允许您将应用程序的定义与配置分开。

有几个好处是显而易见的:首先，您可以在不同的名称空间中拥有不同的`ConfigMaps`,所以如果您在不同的名称空间中拥有不同的环境(dev/test/prod ),您可以为每一个都拥有一个配置。这也意味着如果你在不同的微服务中有相似的配置元素，你可以共享`ConfigMaps`并确保每个人都在相同的配置页面上。

要公开一个服务，可以将其类型设置为`LoadBalancer`。如果你的系统有一个负载平衡器提供者(我们现在有)，它会给服务一个永久的 IP 地址。运行`kubectl edit service/hellok8s-service`，将`spec.type`从`ClusterIP`编辑到`LoadBalancer`，然后查看`kubectl get services`命令的输出:

```
rkamradt@beast:~$ kubectl get services
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP       PORT(S)          AGE
hellok8s-service   LoadBalancer   10.99.129.137   192.168.122.240   8080:30210/TCP   10h
kubernetes         ClusterIP      10.96.0.1       <none>            443/TCP          5d15h
```

如你所见，`hellok8s-service`现在有了一个外部 IP，`192.168.122.240`(你的会有所不同)。用`curl`打起来:

```
rkamradt@beast:~$ curl [http://192.168.122.240:8080/](http://192.168.122.240:8080/)
Hello World
```

嘣！现在，您已经配置了一个负载平衡器，并且可以自动公开多达 11 个服务。我想开一个关于*到 11* 的玩笑，但这有失我的身份。

还有一个问题:只有我们的主主机可以看到它托管的桥。如果我们想让主网络看到我们的服务，我们需要建立一个路由。

我的家庭网络路由器相当笨重，试图让它做任何花哨的事情都是徒劳的。也许你的路由器运气会更好，但还有另一个选择。我在我的主机上设置了一个 nginx 反向代理，让它像一个边缘服务器一样工作。所以现在我可以将 HTTP 从主网络代理到主主机，主主机可以看到桥接网络。同样，请参阅我的文章“[使用虚拟机和 Kubernetes](https://medium.com/better-programming/playing-with-vms-and-kubernetes-26ef93019c22) ”了解详细信息。

我们的系统还缺少什么？到目前为止，我们只公开了打印相同内容的虚拟服务。相当无聊。我们可以通过在组合中添加一个数据库来使事情变得更令人兴奋。但是数据库需要存储东西——它们需要开箱即用，Kubernetes 没有可靠的持久存储。我们需要提供一个。当然，有许多供应商可供选择。

如果你在云提供商上运行，他们会内置持久存储。但是我在客厅角落的一个盒子上运行，所以我必须找到一个存储提供商并安装它，类似于我安装负载平衡器的方式。

在之前的一篇文章“构建你自己的家庭云存储”中，我用 GlusterFS 搭建了一个盒子。GlusterFS 是 Kubernetes 合作的存储提供商之一。为了能够在您的节点中挂载 GlusterFS，您必须对每个节点进行 ssh 并安装 GlusterFS 客户端:`sudo apt-get install glusterfs-client`。

然后我们需要设置一个`Endpoints`资源，并将其连接到一个无头服务。下面是我的设置中的`Endpoints`资源的样子:

```
apiVersion: v1
kind: Endpoints
metadata:
  name: glusterfs-cluster
subsets:
- addresses:
  - ip: 192.168.0.104
  ports:
  - port: 1
```

我的存储主机在`192.168.0.104`，端口可以是 1-64，000 之间的任何数字(它必须是合法值，但除了与下面定义的服务匹配之外，它不被使用)。我只有一个单节点集群，所以它不是弹性的或分布式的，但它能满足我的需要。接下来是无头服务，其定义如下:

```
apiVersion: v1
kind: Service
metadata:
  name: glusterfs-cluster
spec:
  ports:
  - port: 1
```

端口会将其匹配到`endpoints`。创建这两个文件为`endpoints.yaml`和`service.yaml`，然后应用它们:

```
kubectl apply -f endpoints.yaml
kubectl apply -f service.yaml
```

如果你在这里做一个`kubectl get all`，你会看到一个新服务。要查看端点，可以运行`kubectl get ep`和`kubectl decribe ep glusterfs-cluster`。

现在更新您之前创建的`test.yaml`，并将该卷放入 pod 规范:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hellok8s-deployment
  labels:
    app: hellok8s
spec:
  selector:
    matchLabels:
      app: hellok8s
  template:
    metadata:
      labels:
        app: hellok8s
    spec:
      containers:
      - name: hellok8s
        image: docker.io/rlkamradt/hellok8s:latest
        ports:
        - containerPort: 8080
        volumeMounts:
        - mountPath: "/mnt/glusterfs"
          name: glusterfsvol
      volumes:
      - name: glusterfsvol
        glusterfs:
          endpoints: glusterfs-cluster
          path: /gv0
          readOnly: true
---
apiVersion: v1
kind: Service
metadata:
  name: hellok8s-service
spec:
  type: ClusterIP
  selector:
    app: hellok8s
  ports:
  - port: 8080
    targetPort: 8080
```

`glusterfs:`部分指示 Kubernetes 使用 GlusterFS 并提供配置参数。`path: /gv0`是我们在前一篇文章中创建的卷。现在，删除并重新创建部署:

```
kubectl delete -f test.yaml
kubectl apply -f test.yaml
```

现在我们可以进入分离舱，看看`/mnt/glusterfs`是否在那里。但是等等，有个问题。pod 没有启动—它停留在`ContainerCreating`状态—所以有些不对劲。让我们做一点故障排除。运行`kubectl describe pod <podname>`将排除安装卷的问题。

```
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/pods/6aa61d4b-0673-4b77-a2b6-9e8c990986e9/volumes/kubernetes.io~glusterfs/glusterfsvol --scope -- mount -t glusterfs -o auto_unmount,log-file=/var/lib/kubelet/plugins/kubernetes.io/glusterfs/glusterfsvol/hellok8s-deployment-666fcddc56-lsllq-glusterfs.log,log-level=ERROR,ro 192.168.0.104:/gv0 /var/lib/kubelet/pods/6aa61d4b-0673-4b77-a2b6-9e8c990986e9/volumes/kubernetes.io~glusterfs/glusterfsvol
Output: Running scope as unit: run-r20968ad0da8d4b5380bebfaaba201e23.scope
Mount failed. Please check the log file for more details.
, the following error information was pulled from the glusterfs log to help diagnose this issue: 
[2020-04-21 22:54:12.196614] E [fuse-bridge.c:900:fuse_getattr_resume] 0-glusterfs-fuse: 3: GETATTR 1 (00000000-0000-0000-0000-000000000001) resolution failed
The message "E [MSGID: 101046] [dht-common.c:1501:dht_lookup_dir_cbk] 0-gv0-dht: dict is null" repeated 2 times between [2020-04-21 22:54:12.178581] and [2020-04-21 22:54:12.196594]
  Warning  FailedMount  20s  kubelet, kube3  MountVolume.SetUp failed for volume "glusterfsvol" : mount failed: mount failed: exit status 1
```

基本问题是`resolution failed`。但这意味着什么呢？无论我提供的错误消息多或少，在整个错误消息上进行广泛的 Google 搜索都没有发现任何东西。我尝试了一些事情，比如在每个节点上安装 Gluster 客户端包。但是什么都没用。

于是我仔细看了一下`mount`命令，找到了`log-level=ERROR`。我想如果我能把它换成`log-level=DEBUG`，我就能更好地知道哪里出了问题。

经过进一步挖掘，我发现您可以在卷的规范中指定`mountOptions`。但是 Kubernetes 的精英们决定不允许`mountOptions`在线音量定义。

所以我们需要更精确的定义。我们需要创建一个`PersistentVolume`和一个`PersistentVolumeClaim`，然后将`PersistenVolumeClaim`附加到 pod 规范。我认为应该更明确一些，尽管出于本文简洁的目的，我希望通过内嵌的卷定义来保持简单。

让我们创造一个`PersistentVolume`和`PersistentVolumeClaim`。首先，创建一个名为`pv.yaml`的文件，并添加以下内容:

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: gluster-pv
  labels:
    pv: gluster-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  mountOptions:
    - log-level=DEBUG
  glusterfs:
    path: /gv0
    endpoints: glusterfs-cluster
```

然后创建一个名为`pvc.yaml`的文件，并输入以下内容:

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: gluster-claim
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 5Gi
```

这些是对什么是可用的，什么是需要的更明确的定义。`PersistentVolume`表示 GlusterFS 提供了 5 GB，并提供了路径和参数。

它还允许我们定义`mountOptions`，这样我们就可以创建一个更详细的日志。`PersistentVolumeClaim`表示，出于某种目的，我们需要 5 GB 的存储空间。因为我们只有一个持久卷，所以它应该总是与那个相匹配。现在更新`test.yaml`文件中的卷。

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hellok8s-deployment
  labels:
    app: hellok8s
spec:
  selector:
    matchLabels:
      app: hellok8s
  template:
    metadata:
      labels:
        app: hellok8s
    spec:
      containers:
      - name: hellok8s
        image: docker.io/rlkamradt/hellok8s:latest
        ports:
        - containerPort: 8080
        volumeMounts:
        - mountPath: "/mnt/glusterfs"
          name: glusterfsvol
      volumes:
      - name: glusterfsvol
        persistentVolumeClaim:
          claimName: gluster-claim
---
apiVersion: v1
kind: Service
metadata:
  name: hellok8s-service
spec:
  type: ClusterIP
  selector:
    app: hellok8s
  ports:
  - port: 8080
    targetPort: 8080
```

现在应用所有内容:

```
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml
kubectl delete -f test.yaml
kubectl apply -f test.yaml
```

当然，它仍然不起作用，但是现在我们可以通过 SSH 进入节点并查看 GlusterFS 日志。要了解具体情况，请描述 pod `kubectl describe pod <podname>`。您应该会找到类似下面这样的一行:

```
Normal   Scheduled         32s                   default-scheduler  Successfully assigned default/hellok8s-deployment-7667684f95-tfx9k to kube3
  Warning  FailedMount       32s                   kubelet, kube3     MountVolume.SetUp failed for volume "gluster-pv" : mount failed: mount failed: exit status 1
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/pods/2a54c709-efea-4af6-8aa8-8a4634f980ae/volumes/kubernetes.io~glusterfs/gluster-pv --scope -- mount -t glusterfs -o auto_unmount,log-file=/var/lib/kubelet/plugins/kubernetes.io/glusterfs/gluster-pv/hellok8s-deployment-7667684f95-tfx9k-glusterfs.log,log-level=DEBUG 192.168.0.104:/gv0 /var/lib/kubelet/pods/2a54c709-efea-4af6-8aa8-8a4634f980ae/volumes/kubernetes.io~glusterfs/gluster-pv
```

我们可以看到它被部署到`kube3`节点，并给出了`log-file`路径。所以 SSH 连接到节点，而`cat`连接到日志。我们应该会看到更多的日志线，这将为我们提供一些关于哪里出错的线索。

最终，我找到了线`DNS resolution failed on host artful`。我发现这很奇怪，因为在 Kubernetes 的描述中我只使用了 IP 地址——它怎么知道这个名字`artful`？

答案是 GlusterFS 将关于使用主机名而不是 IP 地址的卷的元数据发送回客户端。为了测试这一点，我编辑了`kube3`上的`/etc/hosts`，并为`artful`添加了一行。几分钟后，Kubernetes 自动再次尝试挂载该卷，并且成功了！

现在我们可以用`kubectl exec -it <podname> -- /bin/sh`查看一下 pod 内部，这将显示一个在 pod 内部运行的 shell。然后，您可以`ls /mnt/glusterfs`查看我们在“[构建您自己的家庭云存储](https://medium.com/better-programming/build-your-own-in-home-cloud-storage-1aa74b5c6397)”中测试存储时创建的文件

这是对 Kubernetes 系统进行故障诊断时必须做的一个小例子。能够`ssh`进入您的节点，在您的 pod 内打开一个外壳，并在您的 pod 陷入非就绪状态时使用 describe 命令解决问题，这是您找出问题所在的工具。

当然，在所有节点上编辑`/etc/hosts`并不是正确的答案——可能有更好的方法。也许在 GlusterFS 的配置中，我们应该只使用 IP 地址。但是我将只在所有节点上编辑`/etc/hosts`，因为我只有三个工作节点(pods 通常不在主节点上运行)，并且我不打算再添加任何节点。一旦我这样做了，我们就可以看看我们是否能为我们的解决方案找到一个目的(想想数据库)。

我们会做一个 MongoDB 数据库——谁不爱 Mongo？这将允许我们了解一些我以前没有介绍过的功能。Mongo 将在由一个`StatefulSet`创建的 pod 中运行，这些 pod 类似于副本集，除了它们有非常具体的 pod 创建限制。

首先，a `StatefulSet`用顺序号来命名 pod，而不是你通常看到的最后一组数字的随机数。它还可以一次启动一个，这样当它们启动时，吊舱就不会互相踩踏。不过，对于我们的目的来说，这些都无关紧要，因为我将只启动一个 pod。您可以尝试扩大规模，看看会发生什么，但为了一开始就保持简单，我坚持使用单个 pod。

我要做的另一件事是添加一个用户名/密码密码，这样我们就不必在描述文件中显式地设置它。让我们从这个开始。创建 Kubernetes 秘密的方法有很多，但最简单的方法是在命令行中使用文字值:

```
kubectl create secret generic mongo-secret \
    --from-literal=username=mongo \
    --from-literal=password=ognom
```

这并不完全是超级秘密，但是在真实环境中，您可能会从文件或其他地方获得值。从 Kubernetes 1.14 开始，您可以使用 Kustomize 来生成随机密码，并将其放入 secrets 中。但这只是一个演示，展示了一旦秘密被创造出来，如何使用它们。一旦创建完毕，你可以使用`kubectl describe secret mongo-secret`来查看关于这个秘密的信息。显然，实际值是隐藏的。

现在我们可以创建我们的`StatefulSet`。创建一个名为`mongo.yaml`的文件，并输入以下内容:

```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mongodb
spec:
  serviceName: database
  replicas: 1
  selector:
    matchLabels:
      app: database
  template:
    metadata:
      labels:
        app: database
        selector: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo:4.0.8
        env:
          - name: MONGO_INITDB_ROOT_USERNAME
            valueFrom:
              secretKeyRef:
                name: mongo-secret
                key: username
          - name: MONGO_INITDB_ROOT_PASSWORD
            valueFrom:
              secretKeyRef:
                name: mongo-secret
                key: password
        volumeMounts:
        - name: mongodb-data
          mountPath: /data/db
      volumes:
      - name: mongodb-data
        persistentVolumeClaim:
          claimName: gluster-claim
```

现在运行`kubectl apply -f mongo.yaml`，等待它启动。最终，应该会有一个叫做`mongodb-0`的 pod，我们可以用`kubectl exec -it mongodb-0 — /bin/sh`在里面启动一个 shell。从那里，您可以访问 MongoDB shell: `mongo mongodb://localhost:27017`。嘭！你被录取了。

您必须启动一个服务来从其他 pod 访问数据库。也许在我的下一篇文章中，我会这样做，并编写一个访问数据库的 REST 服务。现在，如果您想向自己证明它确实有效，您可以回到您的存储主机(在我的例子中是`artful`)并查看我们设置为砖块的目录。现在里面应该有一堆数据库文件。

我希望你和我一样，在完成这一切的过程中获得了很多乐趣，包括一些小的故障排除。曾经有过令人沮丧的时候，但那只是增加了事情实际运作时的成就感。本文的所有脚本都可以在最终版本的[这里](https://github.com/rkamradt/KubernetesFromScratchScripts/tree/v1.0)找到。