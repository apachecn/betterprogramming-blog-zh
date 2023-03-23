# 多集群 Kubernetes 环境下的 Istio 服务网格

> 原文：<https://betterprogramming.pub/istio-service-mesh-on-multi-cluster-kubernetes-environment-518094cdcdc4>

## 在单个服务网格中管理多个 Kubernetes 集群上运行的微服务

![](img/66a2162784daa6f47e1f3b125c04697a.png)

汤姆·陈在 [Unsplash](https://unsplash.com/s/photos/future?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

假设您为一个典型的企业工作，有多个团队一起工作，交付组成单个应用程序的独立软件。您的团队遵循微服务架构，并拥有由多个 Kubernetes 集群组成的广泛基础设施。

由于微服务分布在多个集群中，您需要设计一个解决方案来集中管理所有微服务。幸运的是，您正在使用 Istio，提供这个解决方案只是另一个配置更改。

像 Istio 这样的服务网格技术可以帮助您安全地发现和连接分布在多个集群和环境中的微服务。本文是“[如何在您的 K8s 集群](https://medium.com/better-programming/how-to-authorize-non-kubernetes-clients-with-istio-on-your-k8s-cluster-8a90fe95b137)上使用 Istio 授权非 Kubernetes 客户”的后续文章今天我们来讨论如何使用 Istio 管理多个 Kubernetes 集群中托管的微服务。

# 体系结构

Istio 通过以下组件提供跨集群服务发现:

*   Istio 核心 DNS:每个 Istio 控制平面都有一个核心 DNS。Istio 使用它来发现在全局范围上定义的服务。例如，如果集群 1 上托管的微服务需要连接到集群 2 上托管的另一个微服务，您需要在 Istio 核心 DNS 上为集群 2 上运行的微服务创建一个全局条目。
*   根 CA:由于 Istio 需要运行在不同集群上的服务之间的 mTLS 连接，您需要使用一个共享的根 CA 来为两个集群生成中间 CA 证书。这在不同集群上运行的微服务之间建立了信任，因为中间证书共享同一个根 CA。
*   Istio 入口网关:集群间通信通过入口网关进行，服务之间没有直接连接。因此，您需要确保入口网关是可发现的，并且所有集群都可以连接到它。

![](img/187b8c0fb47ed77d93b6289e1d0a2e4d.png)

集群间通信

# 服务发现

Istio 使用以下步骤来促进服务发现:

1.  所有集群上都有相同的控制平面，以提高高可用性。
2.  Kube DNS 被连接到 Istio 核心 DNS 以提供全球服务发现。
3.  用户通过 Istio 核心 DNS 中的 ServiceEntries 以 name.namespace.global 格式定义到远程服务的路由
4.  源边车使用全局核心 DNS 条目将流量路由到目标 Istio 入口网关。
5.  目标 Istio 入口网关将流量路由到正确的微服务 pod。

# 先决条件

本文假设您对 Kubernetes 和 Istio 的工作原理有基本的了解。关于 Istio 的介绍，请查看“[如何使用 Istio](https://medium.com/better-programming/how-to-manage-microservices-on-kubernetes-with-istio-c25e97a60a59) 管理 Kubernetes 上的微服务。”要跟随动手演示，请确保:

*   您至少有两个运行版本 1.14、1.15 或 1.16 的 Kubernetes 集群。
*   您拥有在集群中安装和配置 Istio 的权限。
*   您在两个 Kubernetes 集群上都拥有集群管理权限。
*   入口网关可通过网络负载平衡器或类似配置到达其他集群。平面网络不是必需的。

# 安装 Istio

在两个集群上，使用以下命令安装 Istio 1.6.1:

```
curl -L [https://istio.io/downloadIstio](https://istio.io/downloadIstio) | ISTIO_VERSION=1.6.1 sh -
cd istio-1.6.1
export PATH=$PWD/bin:$PATH
```

因为我们需要使用共享根证书生成的中间证书来引导我们的 Istio 服务网格，所以使用中间证书创建一个秘密。

对于这个示例，让我们使用提供的示例证书。但是，我不建议您在生产中使用这些工具，因为它们很容易获得并且广为人知。最好使用您组织的根 CA 来生成中间 CA 证书。

在两个集群上运行以下命令以使用示例证书。如果您正在使用您的证书，请替换适用的文件路径。

```
kubectl create namespace istio-system
kubectl create secret generic cacerts -n istio-system \
    --from-file=samples/certs/ca-cert.pem \
    --from-file=samples/certs/ca-key.pem \
    --from-file=samples/certs/root-cert.pem \
    --from-file=samples/certs/cert-chain.pemsecret/cacerts created
```

因为我们需要为多集群设置安装 Istio，所以在两个集群上使用提供的 Istio 多集群网关清单文件。

```
$ istioctl manifest apply -f install/kubernetes/operator/examples/multicluster/values-istio-multicluster-gateways.yaml
- Applying manifest for component Base...
✔ Finished applying manifest for component Base.
- Applying manifest for component Pilot...
✔ Finished applying manifest for component Pilot.
  Waiting for resources to become ready...
- Applying manifest for component AddonComponents...
- Applying manifest for component IngressGateways...
- Applying manifest for component EgressGateways...
✔ Finished applying manifest for component EgressGateways.
✔ Finished applying manifest for component IngressGateways.
✔ Finished applying manifest for component AddonComponents.✔ Installation complete
```

# 配置 Kube DNS

下一步是将 Kube DNS 的 DNS 解析联合到 Istio 核心 DNS。让我们通过为`kube-dns`定义一个`ConfigMap`来配置一个存根域。在两个集群上应用以下清单:

# 设置上下文

因为我们需要为不同的活动连接两个集群，所以获取上下文并将它们存储在环境变量中是有意义的。这样，我们可以在我们选择的集群中运行`kubectl`命令，只需在`kubectl`命令中包含上下文即可。

获取上下文:

```
$ kubectl config get-contexts
CURRENT   NAME        CLUSTER     AUTHINFO      NAMESPACE
          cluster-1   cluster-1   cluster-1
*         cluster-2   cluster-2   cluster-2
```

设置环境变量以使用上下文:

```
$ export CTX_CLUSTER1=$(kubectl config view -o jsonpath='{.contexts[0].name}')
$ export CTX_CLUSTER2=$(kubectl config view -o jsonpath='{.contexts[1].name}')
$ echo CTX_CLUSTER1 = ${CTX_CLUSTER1}, CTX_CLUSTER2 = ${CTX_CLUSTER2}
CTX_CLUSTER1 = cluster-1, CTX_CLUSTER2 = cluster-2
```

# 部署示例微服务

让我们从在 cluster-1 的`foo`命名空间上部署睡眠微服务开始:

```
$ kubectl create --context=$CTX_CLUSTER1 namespace foo
namespace/foo created
$ kubectl label --context=$CTX_CLUSTER1 namespace foo istio-injection=enabled
namespace/foo labeled
$ kubectl apply --context=$CTX_CLUSTER1 -n foo -f samples/sleep/sleep.yaml
serviceaccount/sleep created
service/sleep created
deployment.apps/sleep created
$ export SLEEP_POD=$(kubectl get --context=$CTX_CLUSTER1 -n foo pod -l app=sleep -o jsonpath={.items..metadata.name})
```

现在让我们在 bar 名称空间上的 cluster-2 上部署`httpbin`微服务:

```
$ kubectl create --context=$CTX_CLUSTER2 namespace bar
namespace/bar created
$ kubectl label --context=$CTX_CLUSTER2 namespace bar istio-injection=enabled
namespace/bar labeled
$ kubectl apply --context=$CTX_CLUSTER2 -n bar -f samples/httpbin/httpbin.yaml
serviceaccount/httpbin created
service/httpbin created
deployment.apps/httpbin created
```

# 创建服务条目

现在，我们需要在 Istio 核心 DNS 上创建一个服务条目，以便我们可以从 cluster-1 中发现 cluster-2 上的服务。由于所有通信都通过入口网关进行，因此导出 cluster-2 入口网关地址。

```
export CLUSTER2_GW_ADDR=$(kubectl get --context=$CTX_CLUSTER2 svc --selector=app=istio-ingressgateway \
    -n istio-system -o jsonpath='{.items[0].status.loadBalancer.ingress[0].ip}')
```

为了让集群 1 上的服务发现集群 2 上的`httpbin`,我们需要为集群 1 上的`httpbin.bar.global`创建一个`ServiceEntry`。这确保了如果 cluster-1 中的服务到达端点`httpbin.bar.global`，cluster-1 上的 Istio 核心 DNS 可以到达 cluster-2 入口网关。下面这个`yaml`:

*   在“主机”部分定义服务域名。
*   位置是`MESH_INTERNAL`，因为我们需要将其他服务视为同一个服务网格的一部分。
*   它公开端口 8000 上的服务。
*   我们需要为此服务提供一个唯一的 IP 地址。IP 地址不必是可路由的，您可以使用 240.0.0.0/16 范围内的任何地址。
*   我们在端点地址部分定义了 cluster-2 入口网关地址，以便请求可以路由到它。端口 15443 是入口网关的 SNI 感知特使代理端口，用于在目标集群服务的 pod 之间路由流量。

应用`yaml`文件:

# 测试服务

现在让我们从 sleep 微服务生成一些流量，看看它是否能到达运行在 cluster-2 上的`httpbin`微服务。

```
$ kubectl exec --context=$CTX_CLUSTER1 $SLEEP_POD -n foo -c sleep -- curl -I httpbin.bar.global:8000/headers
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0HTTP/1.1 200 OK
  0   519    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
server: envoy
date: Sat, 16 May 2020 23:03:22 GMT
content-type: application/json
content-length: 519
access-control-allow-origin: *
access-control-allow-credentials: true
x-envoy-upstream-service-time: 37
```

我们得到了成功的回应！恭喜，我们已经使用 Istio 成功配置了多个 Kubernetes 集群之间的服务发现。

# 结论

感谢阅读！我希望你喜欢这篇文章。

这是在多个集群上运行的高可用性 Istio 服务网格配置的演示。您还可以拥有共享的控制平面配置，但不建议在生产中使用，如果您因停机而失去一个集群，您也将失去对正在运行的集群的控制。