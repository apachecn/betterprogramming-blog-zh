# Kubernetes 提示:使用服务帐户

> 原文：<https://betterprogramming.pub/k8s-tips-using-a-serviceaccount-801c433d0023>

## 如何从 Pod 与 API 服务器通信

![](img/016d5260e2b288c34917ffbe1d08bc9d.png)

在 Pod 中运行的容器使用 ServiceAccount 与 [Kubernetes](https://kubernetes.io/) 集群的 API 服务器进行通信。在本帖中，我们将看到这是如何完成的，以及 Kubernetes 资源在这个过程中的作用。

# API 服务器— HTTP REST API

如果您使用 Kubernetes 集群，那么您可能会使用`kubectl`二进制文件或 web 接口来调用 API 服务器。在幕后，所有的调用都是通过 API 公开的 HTTP 端点进行的。

这个 API 有很好的文档记录；所有端点都在[官方文档](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14)中有描述。

一个简单的例子:这里是在默认名称空间中运行的 pod 的[列表。当然，请求需要被认证和授权来执行这个动作。](https://API_SERVER/api/v1/namespaces/default/pods/)

# 从 Pod 访问 API 服务器

许多在集群中运行的应用程序(阅读:在 Pods 中运行)需要与 API 服务器通信。其中包括在控制平面内运行的进程(调度程序、控制器管理器、代理等)。)，以及所有需要对集群执行某种形式管理的应用程序。

例如，一些应用程序可能需要知道:

*   群集节点的状态。
*   可用的命名空间。
*   在集群或特定名称空间中运行的 pod。
*   以及其他类似的事情。

为了与 API 服务器通信，Pod 使用包含身份验证令牌的 ServiceAccount。角色(例如:列出给定名称空间内所有 pod 的权限)或 ClusterRole(例如:读取整个集群内所有机密的权限)可以绑定到这个 ServiceAccount。因此 ServiceAccount 有权执行这些操作。

*   **从集群外部:**可以使用 kube 配置文件中指定的端点来访问 API 服务器(默认为`$HOME/.kube/config`)。举个例子，如果你使用数字海洋管理的 Kubernetes，那么端点就是类似于[https://b 703 a4 FD-0d 56-4802-a354-ba 2c 2 a 767 a 77 . k8s . ondigitalocean . com](https://b703a4fd-0d66-4802-a354-ba2c2a734a77.k8s.ondigitalocean.com)
*   **从集群内部**(阅读:从 Pod):可以使用名为`kubernetes`的`ClusterIP`类型的专用服务来访问 API 服务器。默认情况下，这个服务在那里，如果它被错误地删除了，它会自动重新创建。

```
$ kubectl get svc
NAME       TYPE      CLUSTER-IP  EXTERNAL-IP PORT(S) AGE
kubernetes ClusterIP 10.96.0.1   <none>      443/TCP 23h
```

有了正确的权限(稍后将详细介绍)，在*默认*名称空间中列出 Pod 可以通过这个简单的 [GET 请求](https://kubernetes/api/v1/namespaces/default/pods/)从一个 Pod 中完成。

# 使用命名空间默认 ServiceAccount

每个名称空间都有一个默认的 ServiceAccount，名为`default`。我们可以使用以下命令来验证这一点:

```
$ kubectl get sa --all-namespaces | grep default
default     default 1 6m19s
kube-public default 1 6m19s
kube-system default 1 6m19s
```

让我们检查一下*默认*名称空间中名为`default`的 ServiceAccount(这与另一个名称空间的默认 ServiceAccount 非常相似)。

```
$ kubectl get sa default -o yaml
apiVersion: v1
kind: ServiceAccount
metadata:
 name: default
 namespace: default
 ...
secrets:
- name: default-token-dffkj
```

我们在这里可以看到，向这个 ServiceAccount 提供了一个密码。让我们仔细看看这个:

```
$ kubectl get secret default-token-dffkj -o yaml
apiVersion: v1
data:
 ca.crt: LS0tLS1CRU...0tLS0tCg==
 namespace: ZGVmYXVsdA==
 token: ZXlKaGJHY2...RGMUlIX2c=
kind: Secret
metadata:
 name: default-token-dffkj
 namespace: default
 ...
type: kubernetes.io/service-account-token
```

这个秘密的数据键下有几个键/值对。为了可读性，我缩短了`ca.crt`和`token`的值)，基本上是:

*   `ca.crt`是集群证书的 Base64 编码。
*   `namespace`是当前名称空间的 Base64 编码(默认)。
*   `token`是用于验证 API 服务器的 JWT 的 Base64 编码。

注意:JSON Web Token (JWT)是一个开放标准( [RFC 7519](https://tools.ietf.org/html/rfc7519) )，它定义了一种紧凑的自包含方式，以 JSON 对象的形式在各方之间安全地传输信息。该信息可以被验证和信任，因为它是数字签名的。

让我们来关注一下`token` *。*一旦解码(在 Linux 上使用`base64 -d`，或者在 MacOS 上使用`base64 -D`，我们可以很容易地从命令行，或者像 [jwt.io](https://jwt.io) 这样的在线服务中获得这个 JWT 的有效载荷。

此有效负载具有以下格式:

```
{
 “iss”: “kubernetes/serviceaccount”,
 “kubernetes.io/serviceaccount/namespace”: “default”,
 “kubernetes.io/serviceaccount/secret.name”: “default-token-dffkj”,
 “kubernetes.io/serviceaccount/service-account.name”: “default”,
 “kubernetes.io/serviceaccount/service-account.uid”: “ac5aa972–80ae-11e9–854d-0800278b691f”,
 “sub”: “system:serviceaccount:default:default”
}
```

我们可以看到它所链接的 ServiceAccount、它所在的名称空间以及其他一些内部信息。

我们将在下面看到如何根据下面的规范在一个简单的 Pod 中使用这个令牌:

假设该规范在`pod-default.yaml`文件中，您可以使用以下(标准)命令创建 Pod:

```
$ kubectl apply -f *pod-default.yaml*
```

因为没有指定`serviceAccountName`键，所以使用 Pod 的名称空间的默认 ServiceAccount。我们可以通过检查这个创建过的 Pod 的规范来确认这一点(Kubernetes 在创建过程中为我们添加了很多东西)。

这里需要注意的重要事项:

*   `serviceAccountName`键设置为默认 ServiceAccount 的名称。
*   ServiceAccount 的信息通过使用 volume 安装在 Pod 的容器中，在`/var/run/secrets/kubernetes.io/serviceaccount`中(稍后会详细介绍)。

## API 服务器的匿名调用

让我们在这个容器中运行一个 shell 并安装`curl`实用程序:

```
$ kubectl exec -ti pod-default -- sh
/ # apk add --update curl
```

从这个 shell 中，我们可以尝试从 API 服务器获取信息，而无需进行身份验证。

```
/ # curl [https://kubernetes/api/v1](https://kubernetes/api/v1) --insecure
```

注意:如上所述，从集群中运行的 Pod，可以使用 Kubernetes ClusterIP 服务访问 API 服务器。

然后我们得到一条错误消息，因为未经身份验证的用户不允许执行该请求。

```
/ # curl [https://kubernetes/api](https://kubernetes/api) --insecure
{
 “kind”: “Status”,
 “apiVersion”: “v1”,
 “metadata”: {},
 “status”: “Failure”,
 “message”: “forbidden: User \”system:anonymous\” cannot get path \”/api\””,
 “reason”: “Forbidden”,
 “details”: {},
 “code”: 403
}
```

让我们更进一步，尝试使用默认 ServiceAccount 的令牌发出相同的查询。

## 使用 ServiceAccount 令牌调用

从`alpine`容器中，可以从`/var/run/secrets/kubernetes.io/serviceaccount/token`检索默认 ServiceAccount 的令牌(记住上面的 volume/volumeMounts 指令)。使用此令牌，我们可以将其用作针对 API 服务器进行身份验证的承载令牌:

```
/ # TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)
/ # curl -H “Authorization: Bearer $TOKEN” [https://kubernetes/api/v1/](https://kubernetes/api/v1/) --insecure
```

这一次请求进行得很顺利——查询这个端点时不再有错误。将返回资源列表。

```
{
  "kind": "APIResourceList",
  "groupVersion": "v1",
  "resources": [
   {
     "name": "bindings",
     "singularName": "",
     "namespaced": true,
     "kind": "Binding",
     "verbs": [
       "create"
     ]
   },
   ...
  ]
}
```

现在让我们尝试一些更大胆的东西，使用这个标记列出默认名称空间中的所有 pod:

```
/ # curl -H “Authorization: Bearer $TOKEN” [https://kubernetes/api/v1/namespaces/default/pods/](https://kubernetes/api/v1/namespaces/default/pods/) --insecure
{
 “kind”: “Status”,
 “apiVersion”: “v1”,
 “metadata”: {},
 “status”: “Failure”,
 “message”: “pods is forbidden: User \”system:serviceaccount:default:default\” cannot list resource \”pods\” in API group \”\” in the namespace \”default\””,
 “reason”: “Forbidden”,
 “details”: {
   “kind”: “pods”
 },
 “code”: 403
}
```

默认的 ServiceAccount 没有足够的权限执行此查询。在接下来的部分中，我们将创建自己的 ServiceAccount，并为它提供执行此操作所需的额外权限。

# 使用自定义服务帐户

## 服务帐户的创建

让我们在默认名称空间中创建一个新的 ServiceAccount，并将其命名为`demo-sa`。这个 ServiceAccount 在下面的规范中定义，并使用标准的`kubectl apply -f`命令创建。

```
apiVersion: v1
kind: ServiceAccount
metadata:
 name: demo-sa
```

## 角色的创建

除非绑定了特定的权限，否则 ServiceAccount 没有多大用处。在 Kubernetes 中，权限被称为角色或集群角色。它们分别与 ServiceAccount、RoleBinding 和 ClusterRoleBinding 相关联。

角色(同样适用于集群角色)包含一系列规则。每个规则都定义了一些可以针对`apiGroups`(例如:`core`、`apps/v1`)内的`resources`(例如:`Pod`、`Service`、`Secret`)列表执行的动作(例如:`list`、`get`、`watch`)。角色定义特定命名空间的权限，而 ClusterRole 的范围是整个集群。

以下规范定义了一个`Role`，允许在默认`namespace`中列出所有 pod。

```
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: Role
metadata:
 name: list-pods
 namespace: default
rules:
 — apiGroups:
   — ''
 resources:
   — pods
 verbs:
   — list
```

## 将角色与 ServiceAccount 绑定

最后一步，我们绑定上面创建的角色和 ServiceAccount。为此，我们用以下规范定义了一个`RoleBinding`:

```
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: RoleBinding
metadata:
 name: list-pods_demo-sa
 namespace: default
roleRef:
 kind: Role
 name: list-pods
 apiGroup: rbac.authorization.k8s.io
subjects:
 — kind: ServiceAccount
   name: demo-sa
   namespace: default
```

一旦创建了`RoleBinding`,`demo-sa`service account 可以在`default` `namespace`中列出 pod(这是在角色规范中的`rules`键下定义的动作)。让我们检查一下这个。

## 在 Pod 中使用 ServiceAccount

我们根据以下规范创建一个简单的 Pod:

```
apiVersion: v1
kind: Pod
metadata:
 name: pod-demo-sa
spec:
 serviceAccountName: demo-sa
 containers:
 — name: alpine
   image: alpine:3.9
   command:
   — "sleep"
   — "10000"
```

指定了`serviceAccountName`键，该键包含该 Pod 使用的 ServiceAccount 的名称`demo-sa`。正如我们在上面看到的，如果在 Pod 规范中没有指定`serviceAccountName`，则使用名称空间的默认 ServiceAccount。

正如我们对`pod-default` Pod 所做的那样，我们现在在 Pod `pod-demo-sa`的`alpine`容器中运行一个 shell，获取属于`demo-sa` ServiceAccount 的令牌，并使用它来查询默认名称空间中的 Pod 列表。

```
# Get the ServiceAccount token from within the Pod's container
/ # TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)# Call an API Server's endpoint (using the ClusterIP kubernetes service) to get all the Pods running in the default namespace
/ # curl -H “Authorization: Bearer $TOKEN” [https://kubernetes/api/v1/namespaces/default/pods/](https://kubernetes/api/v1/namespaces/default/pods/) --insecure
```

这次不会再出现错误，因为 ServiceAccount 有权执行此操作。我们得到一个在默认名称空间中运行的 pod 列表。

```
{
  "kind": "PodList",
  "apiVersion": "v1",
  "metadata": {
    "selfLink": "/api/v1/namespaces/default/pods/",
    "resourceVersion": "96296"
  },
  "items": [
    ...
  ]
}
```

# 主要要点

默认情况下，每个 Pod 都可以与运行它的集群的 API 服务器进行通信。如果未指定 ServiceAccount，它将使用其命名空间的默认 ServiceAccount。由于默认的 ServiceAccount 只有有限的权限，因此通常最佳做法是为每个应用程序创建一个 service account，赋予它所需的权限(仅此而已)。

为了对 API 服务器进行身份验证，Pod 使用附加 ServiceAccount 的令牌。这个令牌在 Pod 的每个容器的文件系统中都是可用的。

在这个例子中，我们使用了`curl`来查询 API 服务器的 HTTP 端点。真正的应用程序显然会使用专用的库——其中几个库有不同的语言版本。