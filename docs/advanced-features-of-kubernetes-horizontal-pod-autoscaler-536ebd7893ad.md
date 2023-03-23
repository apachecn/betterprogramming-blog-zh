# Kubernetes 卧式 Pod 自动秤的先进特性

> 原文：<https://betterprogramming.pub/advanced-features-of-kubernetes-horizontal-pod-autoscaler-536ebd7893ad>

## Kubernetes 的水平吊舱自动缩放器有一些你可能不知道的功能。以下是如何利用它们的优势。

![](img/29afe934d9f57dc9ec43af74ccf35615.png)

由[雷蒙·克拉文斯](https://unsplash.com/@raimondklavins?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

大多数使用 Kubernetes 的人都知道，您可以使用*Horizontal Pod auto scaler(HPA)*根据应用程序的 CPU 或内存使用情况来扩展应用程序。然而，您可以使用 HPA 的更多功能来定制应用的扩展行为，例如使用定制应用指标或外部指标进行扩展，以及 alpha/beta 功能，如*“扩展到零”*或容器指标扩展。

因此，在本文中，我们将探索所有这些选项，以便我们可以充分利用 HPA 的所有可用特性，并在未来的 Kubernetes 版本中率先使用这些特性。

# 设置

在开始扩展之前，我们首先需要一个测试环境。为此我们将使用*KinD(Kubernetes in Docker)*由以下 YAML 定义的集群:

该清单配置了具有 1 个控制平面节点和 3 个工作器的 KinD 集群，此外，它还启用了一些与自动缩放相关的功能门。这些特征门稍后将允许我们使用 HPA 的一些 alpha/beta 特征。要使用上述配置创建集群，您可以运行:

除了集群，我们还需要一个可以扩展的应用程序。为此，我们将使用在 Kubernetes 端到端测试中使用的[资源消费者工具](https://pkg.go.dev/k8s.io/kubernetes/test/images/resource-consumer#section-readme)及其映像。要部署它，您可以运行:

在这种情况下，这个应用程序非常方便，因为它允许我们模拟 Pod 的 CPU 和内存消耗。它还可以公开基于自定义/外部指标进行扩展所需的自定义指标。为了测试这一点，我们可以运行:

接下来，我们还需要部署收集指标的服务，我们将基于这些指标来扩展我们的测试应用程序。首先是 Kubernetes `[metrics-server](https://github.com/kubernetes-sigs/metrics-server)`,默认情况下它通常在集群中可用，但实际情况并非如此，因此要部署它，我们需要运行:

`metrics-server`允许我们监控 CPU 和内存使用等基本指标，但我们也希望根据自定义指标实现扩展，如应用程序在其`/metrics`端点上公开的指标，甚至是外部指标，如在群集外运行的队列的队列深度。为此，我们需要:

*   [Prometheus 操作员](https://github.com/prometheus-operator/prometheus-operator)收集自定义/外部指标。
*   [ServiceMonitor](https://github.com/MartinHeinz/metrics-on-kind/blob/master/service-monitor.yaml) 对象告诉 Prometheus 如何获取我们应用程序的指标。
*   [Prometheus 适配器](https://github.com/kubernetes-sigs/prometheus-adapter)将自定义/外部指标从 Prometheus 实例获取到 Kubernetes API 中。

您可以参考[端到端演练](https://github.com/kubernetes-sigs/prometheus-adapter/blob/master/docs/walkthrough.md)了解更多设置细节。

上面需要大量的设置，所以为了本文的目的和方便起见，我制作了一个脚本和一组清单，您可以使用它们来启动 KinD cluster 以及所有需要的组件。你需要做的就是从这个库运行`setup.sh`脚本[。](https://github.com/MartinHeinz/metrics-on-kind)

运行脚本后，我们可以使用以下命令验证一切准备就绪:

更多有用的命令可以在上述脚本的输出或[存储库自述文件](https://github.com/MartinHeinz/metrics-on-kind/blob/master/README.md)中找到。

# 基本自动缩放

既然我们的基础设施已经启动并运行，我们就可以开始扩展测试应用程序了。最简单的方法是使用像`kubectl autoscale deploy resource-consumer --min=1 --max=5 --cpu-percent=75`这样的命令创建 HPA，但是这样会创建带有`autoscaling/v1`的`apiVersion`的 HPA，缺少大部分特性。

因此，我们将使用 YAML 创建 HPA，将`autoscaling/v2`指定为`apiVersion`:

上述 HPA 将使用由`metrics-server`从应用 Pod 收集的基本指标。为了测试伸缩性，我们可以模拟大量的内存使用:

# 自定义指标

基于 CPU 和内存使用的扩展通常就足够了，但是我们需要高级扩展选项。首先是使用应用程序公开的自定义指标进行扩展:

此 HPA 配置为根据 Prometheus 从应用程序的`/metrics`端点收集的`custom_metric`值来扩展应用程序。如果所有 pod(`.target.type: AverageValue`)中指定指标的平均值超过 100，这将扩大应用程序的规模。

上面使用了 Pod metric 来进行缩放，但是也可以指定任何其他对象，该对象具有附加到自身的度量:

这个代码片段与上一个代码片段实现了相同的功能，但是这一次使用了*服务*而不是 Pod 作为度量的来源。它还表明，通过将`.target.type`设置为`Value`而不是`AverageValue`，您可以使用直接比较来测量缩放阈值。

为了找出哪些对象公开了可以在缩放中使用的度量，可以使用`kubectl get --raw`遍历 API。例如，要查找 Pod 或服务的`custom_metric`，您可以使用:

此外，为了帮助您排除故障，HPA 对象提供了一个 status 节，显示所应用的指标是否被识别:

最后，为了测试上述 HPA 的行为，我们可以提升应用程序公开的指标，并查看应用程序如何纵向扩展:

# 外部指标

为了展示 HPA 的全部潜力，我们还将尝试根据外部指标扩展应用程序。这将要求我们从运行在集群之外的外部系统(如 Kafka 或 PostgreSQL)中获取指标。我们没有可用的资源，所以我们配置了 Prometheus Adapter 来将某些指标视为外部指标。实现这一点的配置可以在[这里](https://github.com/MartinHeinz/metrics-on-kind/blob/master/custom-metrics-config-map.yaml)找到。不过，您需要知道的是，对于这个测试集群，任何带有`external`前缀的应用程序指标都将进入外部指标 API。为了测试这一点，我们增加了这样一个指标，并检查 API 是否被填充:

然后，要基于此指标扩展我们的部署，我们可以使用以下 HPA:

# HPAScaleToZero

现在，我们已经了解了 HPA 的所有众所周知的特性，让我们看看我们使用特性门实现的 alpha/beta 特性。第一个是 *HPAScaleToZero* 。

顾名思义，这将允许您将 HPA 中的`minReplicas`设置为零，如果没有流量，有效地关闭服务。这在*“突发”*工作流中很有用，例如当您的应用程序从外部队列接收数据时。在这个用例中，当有消息等待处理时，应用程序可以安全地扩展到零。

启用特征门后，我们只需运行:

这将先前示出的 HPA 的最小副本设置为零。

请注意，这只适用于`type` `External`或`Object`的指标。

# HPAContainerMetrics

我们可以利用的另一个特征门是 *HPAContainerMetrics* ，它允许我们使用`type: ContainerResource`的指标:

这使得基于单个容器而不是整个 Pod 的资源利用率进行扩展成为可能。如果您有包含应用程序容器和边车的多容器 Pod，并且您希望忽略边车并仅基于应用程序容器扩展部署，那么这将非常有用。

您还可以通过运行以下命令来查看 Pod/container 指标的细分:

# 对数标度下降

最后但同样重要的是*对数标度下降*特征标志。

如果没有此功能，运行时间最少的 Pod 将在缩减过程中首先被删除。虽然这并不总是理想的，因为它可能会造成副本分布的不平衡，因为新的 Pods 往往比旧的服务更少的流量。

启用此功能标志后，在选择要删除的 Pod 时，将改为使用半随机选择的 Pod。

有关完整的原理和算法细节，请参见 [KEP-2189](https://github.com/kubernetes/enhancements/tree/master/keps/sig-apps/2185-random-pod-select-on-replicaset-downscale) 。

# 结束语

在本文中，我试图涵盖您可以使用 Kubernetes HPA 来扩展您的应用程序的大多数事情。然而，在 Kubernetes 中运行的扩展应用程序有更多的工具和选项，例如[垂直 pod autoscaler](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler) 可以帮助保持 pod 资源请求和限制最新。

另一个选项是 *Digital Ocean* 的[预测 HPA](https://github.com/jthomperoo/predictive-horizontal-pod-autoscaler) ，它将尝试预测一个资源和应用程序应该有多少副本。

最后，自动扩展并不以 Pod 结束——设置 Pod 自动扩展后的下一步是设置[集群自动扩展](https://github.com/kubernetes/autoscaler),以避免耗尽整个集群中的可用资源。

[](/automate-all-the-boring-kubernetes-operations-with-python-7a31bbf7a387) [## 用 Python 自动化所有枯燥的 Kubernetes 操作

### 了解如何使用 Python 的 Kubernetes 客户端库来自动化所有枯燥的 Kubernetes 任务和操作

better 编程. pub](/automate-all-the-boring-kubernetes-operations-with-python-7a31bbf7a387) [](/stop-messing-with-kubernetes-finalizers-b849511b2329) [## 不要再和 Kubernetes 终结器捣乱了

### 这就是为什么你不应该强制删除 Kubernetes 资源或者移除它们的终结器

better 编程. pub](/stop-messing-with-kubernetes-finalizers-b849511b2329) 

```
**Want to Connect?**This article was originally posted at [martinheinz.dev](https://martinheinz.dev/blog/76)
```