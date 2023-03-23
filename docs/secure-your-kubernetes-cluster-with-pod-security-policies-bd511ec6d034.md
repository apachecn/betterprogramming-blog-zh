# 使用 Pod 安全策略保护您的 Kubernetes 集群

> 原文：<https://betterprogramming.pub/secure-your-kubernetes-cluster-with-pod-security-policies-bd511ec6d034>

## Pod 安全最佳实践和动手练习

![](img/73d70b9e04b4ab8c1dd81ffa1458ae2f.png)

[汤姆·帕克斯](https://unsplash.com/@tomparkes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/future-security?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

Kubernetes 安全性一直是系统架构师非常感兴趣的问题。您仍然必须在管理控制和开发人员的灵活性和速度之间做出妥协，以确保您不会在集群中留下任何漏洞。

要在集群中启用安全性，您需要考虑多个方面。通常，网络罪犯会通过入侵 Kubernetes 应用程序来寻找控制主机工作节点的方法。然后他们可以利用这个机会关闭您的群集或利用它进行非法活动。

防止这种控制的一种方法是在集群中实施适当的 pod 安全策略，而不影响开发速度和增加管理开销。

Kubernetes pod 安全策略是集群级别的资源，您可以创建这些资源来控制在 Kubernetes 上运行的容器的安全方面。使用 pod 安全策略，您可以阻止人们运行特权容器，不允许主机联网和端口，不允许主机路径卷，不允许容器作为根用户运行，等等。

以下是使用 pod 安全策略确保群集安全的一些推荐最佳做法:

*   **禁用特权容器** —这个不用说了。在大多数情况下，你不需要特权容器。除非您有特定的用例，否则通过将`privileged`标志设置为`false`在所有安装中禁用它们。即使在这种情况下，最佳实践也是为该 Pod 使用专用的服务帐户并部署特定的名称空间。
*   **尽可能启用只读文件系统** —可能有些容器不需要写入磁盘。例如，只需要写入数据库的 web 应用程序。在这些场景中，没有必要允许对容器文件系统的读写。因此，您应该通过将`readOnlyRootFilesystem`设置为`true`来实施只读文件系统策略。
*   **防止特权升级** —我是不是又用了特权这个词？嗯，您可能会认为以非根用户身份运行容器，但这并不妨碍用户的权限提升。如果您不需要它，可以通过将`allowPrivilegeEscalation`标志设置为`false`来禁用它。
*   **强制非根用户** —这对于您的用例来说可能实用，也可能不实用，但是如果可能的话，不要允许应用程序以根用户身份运行(尤其是在您拥有控制权的情况下)。您可以通过将 Pod 安全策略上的`MustRunAsNonRoot`标志设置为`true`来实现这一点。
*   **防止** `**hostPath**` **卷** —严重不允许`hostPaths`。它们不仅不利于您的卷管理，并将您的容器绑定到特定节点，还会为网络犯罪分子提供访问您的主机文件系统的途径。即使您真的必须使用它们，最佳实践是只允许特定目录使用`hostPaths`,这样人们就只被绑定到特定的目录。大概是这样的:

```
**allowedHostPaths**:
  *# This allows "/foo", "/foo/", "/foo/bar" etc., but*
  *# disallows "/fool", "/etc/foo" etc.*
  *# "/foo/../" is never valid.*
  - **pathPrefix**: "/foo"
    **readOnly**: **true** *# only allow read-only mounts*
```

还有一些你可能想要实施的控制——你应该尽可能地限制它们，除非你出于某种原因需要它们。

# 实施 Pod 安全策略

您可以使用 pod 安全策略，而无需对您的群集进行任何更改。然而，这并不妨碍任何用户创建不使用 Pod 安全策略的 Pod。要在集群中执行严格的检查，您需要通过准入控制器在集群中启用 Pod 安全策略。

这确保了在 Kubernetes 应用配置之前，任何资源请求都应该绑定到特定的 Pod 安全策略。需要注意的是:在启用集群级强制之前，您需要授权用户和服务帐户使用 pod 安全策略。如果你不这样做，Kubernetes 将干脆拒绝创造任何新的豆荚。

在不加重用户负担的情况下，在整个集群中实施 pod 安全策略实施的最佳策略是仅授予 Pod 的服务帐户对 Pod 安全策略的访问权限。这样的话，你可以禁止人们做有潜在危险的事情，而不必付出额外的努力。

让我们通过动手练习来看看在您的 Kubernetes 集群上实施适当的 pod 安全策略的方法。

# 先决条件

在本练习中，您需要使用集群管理特权运行 Kubernetes 集群。

# 启用 Pod 安全策略

有多种方法可以在集群中启用 Pod 安全策略准入控制器。

如果您使用自托管安装，在您的`kube-apiserver`的`enable-admission-plugins`标志中添加`PodSecurityPolicy`，如下所示:

```
--enable-admission-plugins=NodeRestriction,**PodSecurityPolicy**
```

如果您使用的是 GKE 或 AKS 等托管集群，您可以查看云提供商的文档，了解如何启用它。例如，在 GKE，您可以运行以下命令来创建启用了 pod 安全策略的群集:

```
gcloud beta container clusters create cluster-name --enable-pod-security-policy
```

这是为了在现有集群上启用 pod 安全策略:

```
gcloud beta container clusters update cluster-name --enable-pod-security-policy
```

# 创建部署

在本次演示中，我们使用了一个 GKE 集群，并启用了一个集群范围的 pod 安全策略。

让我们创建一个`nginx`部署:

```
$ kubectl create deployment nginx --image=nginx
deployment.apps/nginx created
```

现在让我们看看部署的进展情况:

```
$ kubectl get deployment nginx
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   0/1     0            0           2m19s
```

发生了什么事？部署没有启动任何吊舱。让我们找出原因:

因此，它报告说此部署没有根据任何 pod 安全策略进行验证。为什么？因为我们没有创造任何东西！

# 创建 Pod 安全策略

让我们创建一个不允许任何特权 pod 的 pod 安全策略。

# 授予对 Pod 安全策略的访问权限

Kubernetes 对所有资源使用服务帐户和 RBAC。即使您没有为 pods 使用特定的服务帐户，您仍在使用默认的服务帐户。

实施群集级 pod 安全策略的一个好方法是授予默认服务帐户使用 pod 安全策略的权限。

让我们首先创建一个群集角色来使用 pod 安全策略:

```
kubectl create clusterrole psp:disallow-privileged-pod **\
**    --verb=use **\
**    --resource=podsecuritypolicy **\
**    --resource-name=disallow-privileged-pod
clusterrole.rbac.authorization.k8s.io/psp:disallow-privileged-pod created
```

现在，为群集的所有服务帐户创建群集角色绑定:

```
kubectl create clusterrolebinding psp:disallow-privileged-pod **\
**    --clusterrole=psp:disallow-privileged-pod **\
**    --group=system:serviceaccounts
clusterrolebinding.rbac.authorization.k8s.io/psp:disallow-privileged-pod created
```

# 测试设置

现在让我们看看之前创建的`nginx`部署。

删除副本集:

```
$ kubectl delete rs nginx-86c57db685
replicaset.apps "nginx-86c57db685" deleted
```

部署将启动新的副本集:

```
$ kubectl get rs
NAME               DESIRED   CURRENT   READY   AGE
nginx-86c57db685   1         1         0       2s
```

我们可以看到，当前的 Pod 是`1`。这意味着该政策得到正确应用，Kubernetes 可以创建非特权 pod。

```
$ kubectl get pod
NAME                     READY   STATUS    RESTARTS   AGE
nginx-86c57db685-rbqss   1/1     Running   0          2m22s
```

现在让我们尝试使用特权 pod 创建一个`nginx`部署:

获取部署以查看发生了什么:

```
$ kubectl get deployment nginx-priv
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
nginx-priv   0/1     0            0           18s
```

如我们所见，没有准备好的豆荚。让我们看看副本集:

正如我们所看到的，它抱怨说它无法验证任何 pod 安全策略，因为特权容器是不允许的。

# 结论

在本次动手练习中，我们了解了如何创建默认的 pod 安全策略并将其应用到您的集群。在这个例子中，我们不允许特权容器，因为它们是最常见的用例之一。

Pod 安全策略对 pod 的许多其他方面有更广泛的控制，您应该根据您的集群和应用程序的需要适当地使用它。记住执行最小特权原则，你会没事的。

感谢阅读——我希望你喜欢这篇文章！