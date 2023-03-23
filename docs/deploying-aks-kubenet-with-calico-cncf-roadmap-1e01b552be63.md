# 使用印花布部署 AKS Kubenet

> 原文：<https://betterprogramming.pub/deploying-aks-kubenet-with-calico-cncf-roadmap-1e01b552be63>

## CNCF 路线图

![](img/87e4b04d1b37e6989a20b6bbda4edfde.png)

马克·柯尼希在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

部署 AKS 集群有多种方式，我们的重点是 Azure-CLI，但这并不意味着我会在不知道其他方法如何工作的情况下离开您。

Azure portal 是一个官方的 GUI，你可以用它来创建、管理和部署你的云资源。Azure 门户的好处是它总是最新的，并且它可以在任何安装了现代网络浏览器的设备上运行。要在这里创建资源，首先，单击 create 按钮。然后在弹出窗口中，通过在搜索栏中搜索或浏览缩略图并单击其创建链接来缩小 Kubernetes 服务选项的范围。好吧，我想如果我继续评论一切，会有点乏味。

[](https://docs.microsoft.com/en-us/azure/aks/learn/quick-kubernetes-deploy-portal) [## 快速入门:使用 Azure portal - Azure Kubernetes 服务部署 AKS 集群

### Azure Kubernetes Service (AKS)是一个托管的 Kubernetes 服务，可以让您快速部署和管理集群。在…

docs.microsoft.com](https://docs.microsoft.com/en-us/azure/aks/learn/quick-kubernetes-deploy-portal) 

所以让我来回顾一下需要你注意的重要事情。首先，为了一起浏览创建步骤，我将与您共享我当前的选项卡。现在让我们继续地区。区域是创建资源的强制选项；在生产环境中，区域应该靠近您的客户，以便他们可以获得最佳体验。如果您使用的是免费帐户，您可能无法在某些地区创建您的资源，如果这些地区当时负载很重的话。在那种情况下，请把地点换到一个不太拥挤的地区。如果您的 Kubernetes 工作负载需要特定版本的 Kubernetes，您可以在这里进行调整。

默认节点计数值为 3，如果您使用免费帐户并尝试升级群集，这可能会导致问题。如果你不需要那么多节点，确保你总是选择一个。我在这张幻灯片中没有包括节点大小，因为这取决于您的情况，但我总是选择可以支持我的实验室的最便宜的选项。在 Azure 中，您的节点是节点池的成员。如果您单击您当前的节点池，它将弹出一个窗口，允许您修改它们。在操作系统类型部分，您会注意到窗口是灰色的；这是因为第一个节点必须是运行 Kubernetes 系统工作负载的 Linux 机器。如果您进一步向下滚动到可选设置，您应该能够看到每个节点的 Maxpods 下拉列表。这意味着单个节点上允许运行多少个 pod。您不能选择小于 10 的数字，因为这是 Azure 的硬编码值，根据您选择的 CNI，您的最大选项是 250 pods(如果您使用 Azure-CNIor 110，如果您使用 kubenet)。

[](https://docs.microsoft.com/en-us/azure/aks/configure-kubenet) [## 在 Azure Kubernetes 服务(AKS)-Azure Kubernetes 服务中配置 kubenet 网络

### 默认情况下，AKS 集群使用 kubenet，并且会为您创建一个 Azure 虚拟网络和子网。有了 kubenet，节点…

docs.microsoft.com](https://docs.microsoft.com/en-us/azure/aks/configure-kubenet) 

该值由 AKS 服务强制执行，如果您需要在部署后增加该值，则必须使用新设置创建另一个节点池，并将所有工作负载迁移到新节点池。如果你想将一个 windows 节点作为辅助节点添加到你的集群中，你需要确保在网络选项卡下的插件被设置为 Azure-CNI，否则你会看到这个错误。让我们跳过“访问”选项卡，转到“网络”。在这里你可以选择你的网络 CNI，你的选择是蓝色 CNI 或 Kubenet。

[](https://docs.microsoft.com/en-us/azure/////aks/configure-azure-cni) [## 在 Azure Kubernetes 服务(AKS) - Azure Kubernetes 服务中配置 Azure CNI 网络

### 默认情况下，AKS 集群使用 kubenet，并且会为您创建一个虚拟网络和子网。有了 kubenet，节点得到了一个…

docs.microsoft.com](https://docs.microsoft.com/en-us/azure/////aks/configure-azure-cni) 

接下来，选择您的策略引擎，您的选项在 Calico、Azure 和 None 之间。请记住，当您选择由 AKS 部署 Calico 时，它将从 Microsoft image repository 安装 Calico 的一个版本。None 选项将导致 Kubernetes 集群没有策略实施器。在此模式下，您可以安装最新版本的 Calico 并手动配置。好吧，我必须解释为什么我跳过了这些标签；这是因为修改这些选项需要配置所有 Azure-CNI 最新的基于供应商的功能，这超出了本课程的范围。现在，让我们回到我们的部署，单击“Review + Create”选项卡。在这里，您可以按“create”按钮来部署您的集群，或者单击“Download a template for automation”链接，让我转到接下来的幻灯片。

[](https://www.tigera.io/project-calico/) [## 印花布项目

### Calico 项目是免费的开源项目，旨在简化、扩展和保护容器和 Kubernetes 网络…

www.tigera.io](https://www.tigera.io/project-calico/) 

Azure 资源管理器(ARM)是一种使用代码部署 Azure 资源的声明式方法。ARM 模板是一个常规的 JSON 文件，它包含了必须部署哪些资源的细节。导出 ARM 模板后，您可以将它与 Azure-CLI 或 Azure portal 一起使用，在一个动作中创建任意多的资源。Azure-CLI 是与 Azure 云平台交互的官方命令行实用程序。你可以使用 Azure-CLI 从你的命令行方便地创建 Azure 平台提供的每一个资源，但是我现在超越了我自己。

我想提一下，我选择了加拿大中部作为我的区域。请随意将此更改为离您更近的任何其他地区。

请记住，Azure 免费帐户可能不允许您在某些地区创建资源，如果该地区有巨大的需求。

使用以下命令获取可用区域的完整列表。

```
az account list-locations -o table
```

AKS 通常支持所有地区的主流版本的 Kubernetes。AKS 还为 Kubernetes 的三个 GA 次要版本提供支持。您可以通过发出以下命令来获得当前支持的 Kubernetes 列表。

```
az aks get-versions --location canadacentral -o table
```

在撰写本文时，AKS Kubernetes 的默认版本是 v1.21.9

要在 Azure 中运行 Kubernetes 集群，您必须创建共享相同生命周期的多个资源，并将它们分配给一个资源组。

资源组是一种在 Azure 中对相关资源进行分组的方式，以便于管理和访问。您可以在一个位置创建多个具有唯一名称的资源组。

创建资源组；

```
az group create --name ccol2azure-week2-lab --location canadacentral
```

使用 Azure-CLI 部署 AKS 集群是一个简单的命令执行过程。

网络插件是指将建立网络的 CNI 插件。

网络策略是指将实施网络安全策略的 CNI 插件。

请记住，部署后不能更改这些选项。在这种情况下，您唯一的选择是销毁并从头重新部署您当前的集群，因为 Azure 控制平面会定期检查并重新配置这些值。

使用以下命令部署 AKS 集群。

```
az aks create --resource-group ccol2azure-lab --name CalicoAKSCluster --node-count 1 --node-vm-size Standard_B2s --network-plugin kubenet --network-policy calico --pod-cidr 192.168.0.0/16 --generate-ssh-keys
```

Azure-CLI 可用于导出 AKS 部署的集群配置文件。这个文件可以与 kubectl 一起使用，以便与集群 API 服务器进行通信，从而管理和维护集群。

使用以下命令导出集群配置文件。

```
az aks get-credentials --resource-group ccol2azure-lab --name CalicoAKSCluster --admin
```

在导出配置文件之后，您应该使用 kubectl 命令之一来验证您可以访问您的集群控制平面。

例如，您可以使用节点命令。

```
kubectl get nodes
```

您应该会看到类似下面的结果。

```
NAME                                STATUS   ROLES   AGE   VERSION
aks-nodepool1-15457940-vmss000000   Ready    agent   86s   v1.21.9
```

在带有 Kubenet CNI 插件的 AKS 集群中，节点从底层 Azure 网络获取 IP 地址。

使用以下命令获取 VNET 内的可用 IP 范围。

```
az network vnet list -o table | egrep ccol2azure-lab
```

使用以下命令检查此 AKS 群集中参与节点的 IP 地址。

```
kubectl  get node -o wide
```

使用`kubenet` CNI 部署的 AKS 集群使用`host-local`插件为您的工作负载分配 IP 地址。

主机本地 IPAM 插件是一个常用的 IP 地址管理插件，它为每个节点分配一个固定大小的 IP 地址范围(CIDR ),然后为该范围内的每个 Pod 分配 IP 地址。

```
kubectl get configmap -n calico-system cni-config -o yaml
```

寻找下面的输出，验证`host-local`负责 IP 分配。

```
"ipam": { "type": "host-local", "subnet": "usePodCidr"},
```

默认的地址范围大小是/24 (256 个 IP 地址)，尽管其中两个 IP 地址是为特殊目的保留的，没有分配给 pod。

```
kubectl cluster-info dump | egrep -i 'PodCIDR"'
```

Kubenet 使用用户定义的路由在不同节点上托管的集群资源之间建立连接。

首先，让我们验证我们的集群没有使用任何封装。

```
kubectl get installations default -o jsonpath="{.spec.calicoNetwork}" | jq
```

如果仔细观察输出，您会发现后端(bgp)和 ipPool 封装都被禁用。

接下来，查看 VNET 路由表中的路由列表。

```
az network route-table list -o table
```

使用前面输出中的名称和资源组，并运行以下命令。

```
az network route-table show -g <NAME-OF-YOUR-ROUTE-TABLE-RESOURCE-GROUP> --name <NAME-OF-YOUR-AKS-ROUTETABLE> --query "{addressPrefix:routes[].addressPrefix,nexhop:routes[].nextHopIpAddress}"
```

您应该会看到类似于以下内容的结果:

```
{
  "addressPrefix": [
    "192.168.0.0/24"
  ],
  "nexhop": [
    "10.240.0.4"
  ]
}
```

正如输出所示，我们的 Azure 网络知道子网范围`192.168.0.0/16`可以通过`10.240.0.4`到达。

路由是第 3 层概念，需要额外的一跳才能将数据包传送到目的地。这可能会给 Pod 通信增加一点延迟。

当您创建一个使用 Kubenet 的集群时，只有第一个池路由会自动生成；额外的 ipPools 将需要用户定义的路由条目，这增加了管理员的复杂性。

使用 Kubenet 时，不支持 Windows 池和虚拟节点。

YAOBank 是一个演示应用程序，有三个不同的层，分别是客户层、摘要层和数据库层。客户窗格连接到摘要窗格，摘要窗格连接到数据库窗格。它在 YAML 有一个部署清单，可以帮助测试和学习 Kubernetes 网络。

使用以下命令部署 YaoBank 清单。

```
kubectl apply -f [https://raw.githubusercontent.com/tigera/ccol2azure/main/week2/yaobank.yaml](https://raw.githubusercontent.com/tigera/ccol2azure/main/week2/yaobank.yaml)
```

我们可以通过查看部署状态并使用`yao`关键字过滤输出来验证我们的部署。

```
kubectl get deployments -A | egrep yao
```

现在我们已经成功部署了我们的应用程序。让我们创建一个负载平衡器服务，并使用浏览器连接到客户 Pod。

为此，我们可以使用下面的清单。

```
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: yaobank-customer
  namespace: yaobank-customer
spec:
  selector:
    app: customer
  ports:
    - port: 80
      targetPort: 80
  type: LoadBalancer
EOF
```

使用以下命令验证服务部署。

```
kubectl get svc -n yaobank-customer yaobank-customer
```

注意:负载平衡器的服务可能需要 1-2 分钟来获取外部 IP 地址

在浏览器中使用负载平衡器的 EXTERNAL-IP IP 地址，您应该能够看到 YaoBank web UI。

您可以使用以下命令来检查连接日志。

```
kubectl logs -n yaobank-customer deployments/customer
```

注意:日志中报告的 IP 地址属于 kube-proxy Pod。

首先，让我们验证客户 pod 是否可以直接访问数据库。

使用以下命令从一个客户 pod 访问数据库。

```
kubectl exec -it -n yaobank-customer deployments/customer -- curl --connect-timeout 5 [http://database.yaobank-database:2379/v2/keys?recursive=true](http://database.yaobank-database:2379/v2/keys?recursive=true) | python -m json.tool
```

由于没有适当的策略，数据库会对任何试图连接到它的人做出响应。

顾名思义，默认拒绝策略通过拒绝环境中所有类型的通信来建立隔离。为了创建一个能够实现这一目标的 Kubernetes 网络策略，我们需要以一种能够双向影响流量的方式使用选择器。

使用以下策略在 yaobank-database 命名空间中建立隔离。

```
kubectl apply -f - <<EOF
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
 name: default-deny
 namespace: yaobank-database
spec:
 podSelector: {}
 policyTypes:
 - Ingress
 - Egress
EOF
```

让我们再次进行连接检查。

```
kubectl exec -it -n yaobank-customer deployments/customer -- curl --connect-timeout 5 [http://database.yaobank-database:2379/v2/keys?recursive=true](http://database.yaobank-database:2379/v2/keys?recursive=true)
```

完美！我们已经阻止了对我们的数据库 Pod 的直接访问，并且作为副作用，通过隔离数据库，我们已经使我们的服务对我们的客户不可用。

注意:如果你刷新你的浏览器，你将不能再访问耀银行网站。

接下来，我们将探讨如何修复这种情况，并允许其他服务与数据库对话。

这一次，让我们使用允许流量的策略，而不是拒绝。我们的目标是描述什么类型的流量被允许进出数据库名称空间。

```
kubectl apply -f - <<EOF
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: p-summary2database
  namespace: yaobank-database
spec:
  podSelector:
    matchLabels:
      app: database
  ingress:
    - from:
      - namespaceSelector:
          matchLabels:
            ns: summary
      - podSelector:
          matchLabels:
            app: summary
  egress:
    - to:
      - namespaceSelector:
          matchLabels:
            ns: summary
      - podSelector:
          matchLabels:
            app: summary
EOF
```

使用浏览器连接负载平衡器服务，并验证我们的服务是否正常工作。

Kubernetes 的政策非常适合定位命名空间流量。但是，集群有许多部分是这些策略无法控制的。

例如，如果希望建立真正的隔离，必须对集群中的每个命名空间应用默认的拒绝。

同样值得注意的是，在没有策略的情况下，Calico 的默认行为是允许所有流量。但是，当策略存在时，此行为会更改为阻止除策略明确允许的流量之外的所有流量。

在开始下一个实验之前，请删除 ***默认-拒绝*** 策略。

```
kubectl delete networkpolicy -n yaobank-database default-deny
```

你好！我们在上一节已经做了很多，我想回顾一下刚刚发生的一些事情。我将尽力解释我们刚刚实施的这些 kubernetes 网络政策资源背后的思考过程。我们实现了一个 Kubernetes 网络策略资源，这一点很明显，因为我们的策略标题已经指出了这一点

然后，我们需要确保这个策略只影响数据库名称空间，这就是我们在元数据部分使用名称空间的原因。这里有一个简短的说明，Kubernetes 网络策略资源需要一个名称空间，当您没有表示名称空间时，它会自动分配到您的默认名称空间，之后我们添加了一个具有宽松条件的 pod 选择器，这意味着所有 pod 流量都发生在数据库名称空间内，然后通过声明流量方向

发生在数据库命名空间内，然后通过声明流量方向，我们总结了我们的策略，并在数据库命名空间中创建了隔离，成功阻止了所有人访问我们在该命名空间中的服务。之后，我们制定了一个许可政策，允许我们的客户进入。这一次，我们的 podSelector 标准更加严格，通过附加匹配标签，我们基本上声明了该策略应该影响任何具有“应用程序数据库”的 pod

我们基本上声明了这个策略应该影响任何带有“应用数据库”标签的 pod。对于入站流量或入口流量，我们表示，来自带有“ns summary”标签的命名空间并由带有“app summary”标签的 pod 发起的每个流量都应该只受到影响。在针对出站流量或出口的相同策略中，我们表达了数据库命名空间只允许与具有“app summary”标签且位于具有“ns summary”标签的命名空间中的 pod 对话。这里有一个小提示，因为 Kubernetes NetworkPolicy 资源绑定到名称空间，所以当您想要在多个名称空间中建立隔离时，您需要为每个名称空间单独编写一个拒绝策略。

在我们开始之前，请确保您的集群只有这两个 Kubernetes 网络策略。

使用以下命令列出策略。

```
kubectl get networkpolicy -A
```

如果您刚刚创建了一个新的集群，请使用以下命令创建 p-summary 2 数据库网络策略。

```
kubectl apply -f [https://raw.githubusercontent.com/tigera/ccol2azure/main/week2/01_allow_summary.yaml](https://raw.githubusercontent.com/tigera/ccol2azure/main/week2/01_allow_summary.yaml)
```

API 服务器提供了一个 REST API 接口来与 projectcalico.org/v3 API 组进行交互。API 服务器允许 kubectl 管理 Calico API 组资源，而无需安装 calicoctl。

 [## 安装 calicoctl

### 本指南帮助您安装 calicoctl 命令行工具来管理 Calico 资源和执行管理…

projectcalico.docs.tigera.io](https://projectcalico.docs.tigera.io/maintenance/clis/calicoctl/install) 

使用以下命令验证 calico-apiserver 尚未安装在集群中。

```
kubectl get deployments -n calico-apiserver
```

使用以下命令安装 calico-apiserver。

```
kubectl apply -f - <<EOF
apiVersion: operator.tigera.io/v1
kind: APIServer
metadata:
 name: default
spec: {}
EOF
```

使用以下命令获取关于 API 服务器部署状态的信息。

```
kubectl get tigerastatus
```

Calico GlobalNetworkPolicy 资源是一个安全策略规则，可以影响整个群集。这种类型的资源会影响命名空间(集群内部的流量)和非命名空间(外部和 NIC)流量。

注意:在此策略中，来自非命名空间和 kube-system、calico-system、calico-apiserver 命名空间的流量被有意排除，以简化内容的流动。

使用以下命令建立隔离。

```
kubectl apply -f - <<EOF
apiVersion: projectcalico.org/v3
kind: GlobalNetworkPolicy
metadata:
  name: default-app-policy
spec:
  namespaceSelector: has(projectcalico.org/name) && projectcalico.org/name not in {"kube-system", "calico-system", "calico-apiserver"}
  types:
  - Ingress
  - Egress
  egress:
    - action: Allow
      protocol: UDP
      destination:
        selector: k8s-app == "kube-dns"
        ports:
          - 53
EOF
```

让我们再次进行连接性测试，但是这次我们尝试从一个不同的名称空间访问一个外部 URL，这个外部 URL 没有被明确拒绝。

```
kubectl exec -it -n yaobank-customer deployments/customer --  curl --connect-timeout 10 -LIs [https://projectcalico.docs.tigera.io/](https://projectcalico.docs.tigera.io/) | egrep HTTP
```

全局策略适用于整个集群，这意味着已经建立了隔离，而不必为每个名称空间添加策略。即使对于您将来要创建的名称空间也是如此。

除了 GlobalNetworkPolicy 资源，Calico 还提供了一个 NetworkPolicy 资源，可以单独应用于名称空间。

使用以下命令添加所需的规则。

```
kubectl apply -f - <<EOF
apiVersion: projectcalico.org/v3
kind: NetworkPolicy
metadata:
  name: p-external2customer
  namespace: yaobank-customer
spec:
  selector: app == "customer"
  ingress:
  - action: Allow
    protocol: TCP
    destination:
      selector: app == "customer"
  egress:
  - action: Allow
    protocol: TCP
    destination: 
      serviceAccounts:
        names:
          - summary
      namespaceSelector: ns == "summary"
---
apiVersion: projectcalico.org/v3
kind: NetworkPolicy
metadata:
  name: p-customer2summary
  namespace: yaobank-summary
spec:
  selector: app == "summary"
  ingress:
  - action: Allow
    protocol: TCP
    destination:
      selector: app == "summary"
      ports:
      - 80
  egress:
  - action: Allow
    protocol: TCP
    destination:
      selector: app == "database"
      namespaceSelector: projectcalico.org/name == "yaobank-database"
EOF
```

此时，您的集群应该是安全的，您将能够重新访问 WebUI。

使用以下命令查看 Calico GlobalNetworkPolicy 规则列表。

```
kubectl get globalnetworkpolicy
```

使用以下命令查看 Calico NetworkPolicy 规则列表。

```
kubectl get caliconetworkpolicy -A
```

使用以下命令查看 Kubernetes 策略规则列表。

```
kubectl get networkpolicy -n yaobank-database
```

[](https://github.com/tigera/ccol2azure/tree/main/week2) [## ccol azure/main tigera/ccol azure 第 2 周

### azure 认证课程的资源。通过在…上创建帐户，为 tigera/ccol azure 开发做出贡献

github.com](https://github.com/tigera/ccol2azure/tree/main/week2) 

我们只需要添加一个全局网络策略来锁定集群，并添加几个网络策略来允许客户和摘要命名空间之间的通信。

现在，我想我需要解释为什么我们需要实现这些异常。真正的隔离策略应该是这样的。实现这样的策略需要微调异常，以允许基本的 Kubernetes 服务工作。这是因为全局网络策略影响命名空间和非命名空间环境，如果没有适当的例外，实施这样的策略将导致服务中断。例如，Calico 由于许多原因需要与 kube-system 名称空间进行通信，如果没有适当的例外，实现完全隔离可能会中断这一关键的通信流。另一个例子是 Kubernetes DNS 服务。DNS Pods 位于 kube-system 命名空间中，total isolation 阻止由集群资源发起的 DNS 查询。为了解决这些问题，我们使用简单的逻辑为可以在任何环境中工作的基本 Kubernetes 服务创建了一个通用豁免。

我们的逻辑使用一个唯一的 Calico 定义来告诉 Calico 策略引擎，我们对来自不属于该例外列表的名称空间环境的网络流量感兴趣。现在您可能想知道，如果 DNS pods 位于 Kube-system 名称空间中，为什么我们需要策略的出口部分？虽然 kube-system 名称空间现在可以发送和接收流量，但是工作负载名称空间仍然是隔离的，这意味着它们的入站和出站通信被阻塞。为了解决这个问题，我们实施了一个集群范围的出口或出站策略，该策略以端口 53 上的 UDP 流量为目标，并让所有命名空间资源向具有 kube-dns 标签的 pod 发送流量。

Azure-CLI 是管理 Azure 云平台资源的一站式应用程序。除了创建 aks 集群之外,“az aks delete 子命令”还提供了一种快速、干净的方法来删除 AKS 集群中的所有参与资源。

使用以下命令删除 AKS 集群。

```
az aks delete --name CalicoAKSCluster --resource-group ccol2azure-lab -yaz group delete --resource-group ccol2azure-lab -y
```

使用以下命令从 kubectl 配置文件中删除 AKS 集群条目。

```
kubectl config delete-cluster CalicoAKSCluster
kubectl config delete-context CalicoAKSCluster-admin
kubectl config delete-user clusterAdmin_ccol2azure-lab_CalicoAKSCluster
```

[](https://www.tigera.io/lp/calico-certification/) [## 印花布认证

### 提高您的 Kubernetes 网络、安全性和可观察性知识的资源。

www.tigera.io](https://www.tigera.io/lp/calico-certification/) 

感谢阅读