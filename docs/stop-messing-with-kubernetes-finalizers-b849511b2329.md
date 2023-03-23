# 不要再和 Kubernetes 终结器捣乱了

> 原文：<https://betterprogramming.pub/stop-messing-with-kubernetes-finalizers-b849511b2329>

## 这就是为什么你不应该强制删除 Kubernetes 资源或者移除它们的终结器

![](img/d15ea7ac32b9ac6d9cd1d17c4f1a4f28.png)

背景照片由 [Sam Pak](https://unsplash.com/@melocokr?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

我们都经历过——看到 Kubernetes 资源的删除被卡住、挂起或花费很长时间令人沮丧。您可能已经使用移除终结器或运行`kubectl delete ... --force --grace-period=0`强制立即删除的可怕建议“解决”了这个问题。99%的时候，这是一个可怕的想法，在这篇文章中，我会告诉你为什么。

# 终结器

在我们讨论为什么强制删除是个坏主意之前，我们首先需要讨论一下终结器。

终结器是资源元数据中的值，表示所需的预删除操作。它们告诉资源控制器在删除对象之前需要执行什么操作。

最常见的是:

它们的目的是防止资源被删除，同时控制器或 Kubernetes 操作员干净利落地清理任何相关对象，比如底层存储设备。

当删除具有终结器的对象时，`deletionTimestamp`被添加到资源元数据中，使该对象成为只读的。只读规则的唯一例外是可以移除终结器。一旦所有终结器都消失了，对象就排队等待删除。

理解终结器只是资源元数据中的项/键是很重要的。终结器不指定要执行的代码。它们必须由资源控制器添加/删除。

此外，不要混淆终结器和所有者引用。

`.metadata.OwnerReferences`字段指定对象间的父/子关系，如【部署-】>复制集- > Pod。当您删除一个对象时，例如`Deployment`，可以删除整个子对象树。这个过程(删除)是自动的，与终结器不同，在终结器中，控制器需要采取一些操作并移除终结器字段。

# 什么会出错？

如前所述，您可能遇到的最常见的终结器是附加到永久卷(PV)或永久卷声明(PVC)的终结器。这个终结器保护存储在被 Pod 使用时不被删除。因此，如果 PV 或 PVC 不想删除，很可能意味着它仍然由 Pod 装载。

如果您决定强制删除 PV，请注意，云或任何其他基础架构中的后备存储可能不会被删除，因此您可能会留下一个悬而未决的资源，这仍然会耗费您的资金。

另一个例子是`namespace`，它可能陷入`Terminating`状态，因为资源仍然存在于名称空间中，而名称空间控制器无法移除这些资源。强制删除名称空间可能会在您的集群中留下悬而未决的资源，例如包括[云提供商的负载平衡器](https://github.com/kubernetes/kubernetes/issues/60807#issuecomment-658875036)，以后可能很难追踪到这些资源。

虽然不一定与终结器相关，但值得一提的是，除了等待终结器之外，资源还可能因为许多其他原因而被阻塞:

最简单的例子是`Pod`卡在`Terminating`状态，这通常表明运行`Pod`的节点有问题。用`kubectl delete pod --grace-period=0 --force ...`来“解决”这个问题将会从 API 服务器(`etcd`)中删除`Pod`，但是它可能仍然在节点上运行，这肯定是不可取的。

另一个例子是`StatefulSet`，其中`Pod`强制删除会产生问题，因为`Pods`有固定的标识(`pod-0`、`pod-1`)。分布式系统可能依赖于这些名称/身份。

如果`Pod`被强制删除，但仍在该节点上运行，当`StatefulSet`控制器替换原来的“已删除”`Pod`时，您可以得到两个具有相同身份的 pod。然后，这两个 `Pod`可能会尝试访问同一个存储，这可能会导致数据损坏。更多信息请参见[文档](https://kubernetes.io/docs/tasks/run-application/force-delete-stateful-set-pod)。

# 野生环境中的终结器

我们现在知道，我们不应该乱用带有终结器的资源，但是这些资源是什么呢？

在“普通”Kubernetes 中，你会遇到的三个最常见的例子是分别与持久卷和持久卷声明相关的`kubernetes.io/pv-protection`和`kubernetes.io/pvc-protection`(加上在 1.23 版中引入的[和](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolume-deletion-protection-finalizer))以及存在于名称空间中的`kubernetes`终结器。不过最后一个不在`.metadata.finalizers`场，而是在`.spec.finalizers`。这种特殊情况在[架构文档](https://github.com/kubernetes/design-proposals-archive/blob/main/architecture/namespaces.md#finalizers)中有描述。

除了这些“普通的”终结器之外，如果安装 Kubernetes 操作符，您可能会遇到更多的终结器，这些操作符经常在它们的定制资源上执行预删除逻辑。快速搜索一些流行项目的代码会发现以下内容:

*   [Istio](https://github.com/istio/istio/blob/master/operator/pkg/controller/istiocontrolplane/istiocontrolplane_controller.go#L65) — `istio-finalizer.install.istio.io`
*   [证书管理器](https://github.com/cert-manager/cert-manager/blob/ee8ec69fadff165afa96c2dd22264c16fdb7d065/internal/apis/acme/v1beta1/const.go#L20) — `finalizer.acme.cert-manager.io`
*   [斯特里姆齐(卡夫卡)](https://github.com/strimzi/strimzi-kafka-operator/commit/69e77ce8d5918c25048a253f91f4bca8e89028d9#diff-0f711d9ed233c37fbe749fd6c4aadce73849f48de3c414d86d9af89d51ea5ef7R317)——`service.kubernetes.io/load-balancer-cleanup`
*   [码头](https://github.com/quay/quay-operator/pull/405/files#diff-db06dd075ea792819f15dcbfb9c2376eea2e17832c2bd64ae6b381d3c947b57eR56)——`quay-operator/finalizer`
*   [Ceph/Rook](https://github.com/rook/rook/blob/master/Documentation/ceph-teardown.md#removing-the-cluster-crd-finalizer) — `ceph.rook.io/disaster-protection`
*   [逃离](https://github.com/argoproj-labs/argocd-operator/pull/247/files#diff-1078fd6d90631dae21aebe2e5cb7b8f2e559f568d61b8277117dd19344462d47R188)——`argoproj.io/finalizer`
*   [石蕊混沌](https://github.com/litmuschaos/chaos-operator/blob/master/pkg/controller/chaosengine/chaosengine_controller.go#L57)——`chaosengine.litmuschaos.io/finalizer`

如果要查找集群中存在的所有终结器，则必须针对每种资源类型运行以下命令:

您可以使用`kubectl api-resources`来获取集群中可用资源类型的列表。

不管哪个终结器停止删除您的资源，强制删除这些资源的负面影响通常是相同的:留下了一些东西，可能是存储、负载平衡器或简单的 pod。

此外，正确的解决方案通常是相同的，即找到停止删除的终结器，可能通过查看控制器/操作符的源代码来弄清楚它的目的，并解决阻止控制器移除终结器的任何问题。

如果您决定强制删除有问题的资源，那么解决方案应该是:

一个例外是`Namespace`，它有`finalize` API 方法，通常在所述`Namespace` 中的所有资源被清理时调用。如果`Namespace`拒绝删除，即使没有资源可以删除，那么您可以自己调用这个方法:

# 构建您自己的

现在我们知道了它们是什么以及它们是如何工作的，应该很清楚它们非常有用，所以让我们看看如何将它们应用到我们自己的资源和工作负载中。

Kubernetes 生态系统是基于围棋的，但为了简单起见，我在这里将使用 Python。如果您不熟悉 Python Kubernetes 客户端库，可以考虑先阅读我以前的文章:

[](/automate-all-the-boring-kubernetes-operations-with-python-7a31bbf7a387) [## 用 Python 自动化所有枯燥的 Kubernetes 操作

### 了解如何使用 Python 的 Kubernetes 客户端库来自动化所有枯燥的 Kubernetes 任务和操作

better 编程. pub](/automate-all-the-boring-kubernetes-operations-with-python-7a31bbf7a387) 

在我们开始使用终结器之前，我们首先需要在集群中创建一些资源——在本例中，是一个`Deployment`:

上面的代码创建了一个名为`my-deploy`的示例`Deployment`，此时没有任何终结器。要添加几个终结器，我们将使用以下补丁:

这里重要的部分是对`patch_namespaced_deployment`的调用，它将`.metadata.finalizers`设置为我们定义的终结器列表。每个都必须完全合格，这意味着它们必须包含`/`，因为它们需要遵守`DNS-1123`规范。理想情况下，为了让它们更容易理解，你应该使用像`kubernetes.io/pvc-protection`这样的格式，在格式前面加上你的服务的主机名，这个主机名与负责终结器的控制器相关。

上面代码片段中的其余代码只是确保`Deployment`的副本可用，然后我们可以继续管理终结器:

这里的一般顺序如下:

*   观察所需的资源(在本例中是一个部署)是否有任何变化/事件。
*   寻找与资源修改相关的事件，我们特别检查`deletionTimestamp`是否存在。如果有，我们从资源的元数据中获取一个终结器列表，并开始处理其中的第一个。
*   使用`finalize`功能执行所有必要的删除前任务
*   最后，用原来的终结器列表减去我们处理的列表，对资源应用一个补丁。

如果 Python 中的补丁看起来很复杂，那么只需知道它相当于下面的`kubectl`命令:

如果补丁被接受，我们将收到另一个修改事件，此时我们将处理另一个终结器。我们重复这个过程，直到所有终结器都消失。此时，资源会被自动删除。

请注意，您可能会不止一次收到这些事件，因此使预删除逻辑幂等非常重要。

如果您运行上面的代码片段，然后执行`kubectl delete deployment my-deploy`，那么您应该会看到如下日志:

上面使用 Python 的演示是可行的，但是不够健壮。在真实的场景中，您很可能希望使用 Operator 框架，或者在 Python 中使用，或者更常见的是在 Kubebuilder for Go 中使用。Kubebuilder 文档还包括一整页关于如何使用终结器的内容，包括示例代码。

如果不想实现整个 Kubernetes 操作符，也可以选择构建变异的 Webhook，这在[动态接纳控制](https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/)文档中有描述。流程应该是相同的——接收事件，处理业务逻辑，然后移除终结器。

# 结论

你应该从这篇文章中学到的一点是，在使用`--force --grace-period=0`或从资源中移除终结器之前，你可能要三思。可能有些情况下可以忽略 finalizer，但是为了您自己的利益，在使用 nuclear 解决方案之前进行调查，并了解可能的后果，因为这样做可能会隐藏集群中的系统问题。

# 你可能也喜欢

[](/building-github-apps-with-golang-43b27f3e9621) [## 用 Golang 构建 GitHub 应用

### 在几分钟内启动并运行您的第一个 GitHub 应用程序，并使用它来自动化您在 GitHub 上做的所有事情

better 编程. pub](/building-github-apps-with-golang-43b27f3e9621) 

```
**Want to Connect?**This article was originally posted at [martinheinz.dev](https://martinheinz.dev/blog/74?utm_source=medium&utm_medium=referral&utm_campaign=blog_post_74)
```