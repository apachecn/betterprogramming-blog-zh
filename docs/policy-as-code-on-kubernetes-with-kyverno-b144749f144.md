# Kubernetes 与 Kyverno 的政策代码

> 原文：<https://betterprogramming.pub/policy-as-code-on-kubernetes-with-kyverno-b144749f144>

## 通过 CRD 为您的组织实施 Kubernetes 最佳实践

![](img/166b4742d34e2cbfa00eae1a1d6994bb.png)

[Denys Nevozhai](https://unsplash.com/@dnevozhai?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

Kubernetes 已经能够通过允许人们大规模运行分布式应用程序来彻底改变云原生生态系统。尽管 Kubernetes 是一个功能丰富且健壮的容器编排平台，但它也有自己的复杂性。在多个团队一起工作的情况下大规模管理 Kubernetes 并不容易，确保人们做正确的事情并且不越线也很难管理。

Kyverno 正是解决这一问题的合适工具。它是一个开源的 Kubernetes-native 策略引擎，帮助您使用简单的 Kubernetes 清单定义策略。它可以验证、变异和生成 Kubernetes 资源。因此，它可以允许组织定义和执行策略，以便开发人员和管理员保持一定的标准。

# Kyverno 是如何工作的？

Kyverno 的工作原理是使用一个动态准入控制器，它检查你通过 Kubectl 发送到 Kube API 服务器的每一个请求。如果请求与策略匹配，Kyverno 就应用它。否则，它用定义的消息拒绝请求。

因此，这使得 Kyverno 能够提供以下功能:

*   检查 CPU 和内存限制。
*   确保用户不会更改默认网络策略。
*   检查资源名称是否与特定模式匹配。
*   确保特定的资源总是包含特定的标签。
*   拒绝对特定资源的删除和更改。
*   如果图像标签为`latest`，自动将`imagePullPolicy`更改为`Always`。
*   为每个新命名空间生成默认网络策略。

Kyverno 使用自定义资源定义来定义策略，编写策略就像使用 kubectl 应用策略一样简单。

Kyverno 提供了三个主要功能:

*   确认
*   变化
*   产生

让我们来看看每个清单的例子。

## 确认

一个很好的用例是确保所有的 pod 都应该有一个资源请求和限制集。下面的例子很好地解释了这一点:

虽然大部分是不言自明的，但是`validationFailureAction`指定了是强制执行这个需求(通过使用`enforce`)还是仅仅审计它(通过使用`audit`)并报告违规。

## 变化

突变意味着改变资源，如果它们符合特定的场景。一个很好的例子是，如果图像标签是`latest`，将`imagePullPolicy`改为`Always`。

## 产生

顾名思义，生成针对特定事件的资源。例如，如果有人创建了一个新的名称空间，我们可能希望实施一个默认的网络策略。

# 亲自动手

现在让我们动手看看 Kyverno 的实际操作。我们将安装 Kyverno，然后应用一个验证策略来检查特定的标签。如果标签不存在，Kyverno 将拒绝该请求。否则，它将应用它。

# 先决条件

对于先决条件，我们需要一个功能性的 Kubernetes 集群。

# 安装 Kyverno

安装 Kyverno 很简单。您可以应用 GitHub 上的 Kyverno Kubernetes 清单，也可以安装最新的舵图。

## 使用清单

```
kubectl create -f https://raw.githubusercontent.com/kyverno/kyverno/master/definitions/release/install.yaml
```

## 使用舵图

```
helm repo add kyverno https://kyverno.github.io/kyverno/
kubectl create ns kyverno
helm install kyverno --namespace kyverno kyverno/kyverno
```

要检查我们是否成功安装了 Kyverno，请列出`kyverno`名称空间中的所有资源:

```
# kubectl get all -n kyverno
NAME                           READY   STATUS    RESTARTS   AGE
pod/kyverno-5f7769d697-x8lkj   0/1     Running   0          21sNAME                  TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
service/kyverno-svc   ClusterIP   10.96.167.8   <none>        443/TCP   21sNAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/kyverno   0/1     1            0           21sNAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/kyverno-5f7769d697   1         1         0       21s
```

# 应用策略

现在我们到了政策部分。让我们应用一个策略，确保所有的 pod 都包含一个名为`app`的标签。创建一个名为`require-app-label.yaml`的文件，内容如下:

如果您查看 YAML，我们会看到有一个匹配部分，其中包含我们应该匹配的资源种类。在这个场景中，我们看到一个 pod。validate 部分定义了如果验证失败我们应该输出的消息，以及解释我们需要匹配什么的模式。

由于这是一个 CRD，我们可以直接应用它并获得想要的结果:

```
kubectl apply -f require-app-label.yaml
```

# 测试

所以是时候做些测试了！让我们创建一个没有标签的 pod，看看我们会得到什么:

```
kubectl run nginx --image=nginx
```

![](img/b9e0a392c3cb03f7ed001ef0a10a9b82.png)

正如我们所见，验证失败的原因如下:

```
Error from server: admission webhook "nirmata.kyverno.resource.validating-webhook" denied the request:resource Deployment/default/nginx was blocked due to the following policiesrequire-app-label:
  autogen-check-for-app-label: 'Validation error: label `app` is required; Validation rule autogen-check-for-app-label failed at path /spec/template/metadata/labels/app/'
```

这和预期的一样，因为我们还没有提供标签。现在让我们试试标签`name=nginx`:

```
kubectl run nginx --image=nginx --labels="name=nginx" --generator=run-pod/v1
```

![](img/c0fe3b433cd398973d01f0cff1074769.png)

这个也失败了，因为应用程序标签仍然丢失。让我们创建一个标签为`app=nginx`的 NGINX pod:

```
kubectl run nginx --image=nginx --labels="app=nginx" --generator=run-pod/v1
```

![](img/5149db88275c9a39c5a7e76f739210d3.png)

如我们所见，pod 创建成功。现在，让我们使用 kubectl 来获取 pod 和标签:

```
kubectl get pod nginx --show-labels
```

![](img/e73d6d94e0446e5b1f6884ad875f3a75.png)

pod 正在运行并包含`app=nginx`标签。

# 结论

Kyverno 是一个优秀的政策代码工具，在组织层面上执行最佳实践时非常强大。因为它是 Kubernetes-native，所以编写和操作都很简单，不需要专业开发人员来维护。

感谢阅读！我希望你喜欢这篇文章。