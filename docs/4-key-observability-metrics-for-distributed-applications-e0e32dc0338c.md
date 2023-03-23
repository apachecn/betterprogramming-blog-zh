# 分布式应用的 4 个关键可观察性指标

> 原文：<https://betterprogramming.pub/4-key-observability-metrics-for-distributed-applications-e0e32dc0338c>

## 延迟、流量、错误率和饱和度

![](img/b90071d1e8c896a96ae5109abb97802d.png)

照片由[达林·阿里亚斯](https://unsplash.com/@darlingarias?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如今，一种常见的架构设计模式是将应用程序整体分解成更小的微服务。然后，每个微服务负责应用程序的一个特定方面或功能。例如，一个微服务可能负责服务外部 API 请求，而另一个可能为您的前端处理数据获取。

以这种方式设计一个健壮的、故障安全的基础设施可能具有挑战性。同时监控所有这些微服务的运行会更加困难。

最好不要简单地依赖应用程序日志来了解系统的成功和错误。设置适当的监控将为您提供更完整的画面，但是很难知道从哪里开始。在本帖中，我们将涵盖您的指标应该关注的服务领域，以确保您不会错过关键的见解。

# 开始之前

我们将对您的应用程序设置做一些假设。不要担心，您不需要使用任何特定的框架来开始跟踪指标。但是，对所涉及的组件有一个大致的了解确实有所帮助。换句话说，你如何设置你的可观察性工具比你跟踪什么更重要。

由于足够大的一组微服务需要某种程度的协调，我们将假设您正在使用 [Kubernetes](https://kubernetes.io/) 进行编排。我们还假设您有一个像 [Prometheus](https://prometheus.io/docs/introduction/overview/) 或 [InfluxDB](https://www.influxdata.com/) 这样的时间序列数据库来存储您的度量数据。你可能还需要一个入口控制器，如[Kong 提供的](https://konghq.com/solutions/kubernetes-ingress/?utm_source=guest&utm_medium=devspotlight&utm_campaign=community)来控制流量，以及一个服务网格，如[库马](https://kuma.io/)，以更好地促进服务之间的连接。

在实现任何监控之前，了解您的服务实际上是如何相互交互的是至关重要的。创建一个文档来标识哪些服务和功能相互依赖，以及可用性问题如何影响它们，这可以帮助您为构成适当阈值的内容设置基线数字。

# 度量的类型

您应该能够从两个角度查看数据点:影响数据和因果数据。影响数据代表识别谁受到影响的信息。例如，如果出现服务中断和响应变慢，影响数据可以帮助确定受影响的活跃用户的百分比。

影响数据决定了*谁*受到影响，而因果数据确定了*受到影响的是什么*以及原因。监控网络活动的 Kong Ingress 可以让我们深入了解影响数据。同时，库马可以收集和报告因果数据。

让我们来看几个数据源，并探索可以收集的关于它们的影响数据和因果数据之间的差异。

# 潜伏

等待时间是指用户执行一个动作和它的最终结果之间所花费的时间。例如，如果用户将一件商品添加到他们的购物车中，延迟是指从添加商品到用户看到指示成功添加的响应之间的时间。如果负责执行此操作的服务被降级，延迟将会增加——如果没有立即响应，用户可能会怀疑站点是否在工作。

为了正确跟踪影响数据上下文中的延迟，有必要跟踪单个事件的整个生命周期。坚持我们的购买示例，我们可能期望事件的完整流程如下所示:

*   客户点击“添加到购物车”按钮。
*   浏览器发出服务器端请求，启动事件。
*   服务器接受请求。
*   数据库查询确保产品仍有库存。
*   解析数据库响应，向用户发送响应，事件完成。

为了成功地遵循这一顺序，您应该标准化一个命名模式，既能识别正在发生的事情，又能识别它何时发生，比如`customer_purchase.initiate`、`customer_purchase.queried`、`customer_purchase.finalized`等等。根据您的编程语言，您可能能够为度量服务提供一个功能块或 lambda:

```
statsd.timing('customer_purchase.initiate') do
  # ...
end
```

通过提供特定的关键字，您应该能够确定在出现延迟问题时，事件的哪个部分速度较慢。

在因果数据上下文中跟踪延迟要求您跟踪服务之间事件的速度，而不仅仅是执行的操作。实际上，这意味着对服务对服务的请求进行计时:

```
statsd.histogram('customer_purchase.initiate') do
  statsd.histogram('customer_purchase.external_database_query') do
    # ...
  end
end
```

这不应该局限于捕获整个端点请求/响应周期。这种延迟跟踪过于宽泛，应该更加细化。

假设您有一个微服务，其端点发出内部数据库请求。在这种情况下，您可能希望记录收到请求的时间、查询花费的时间、服务响应请求的时间以及发起客户端收到请求的时间。通过这种方式，您可以精确定位服务之间的通信方式。

# 交通

您希望您的应用程序有用且受欢迎——但是如果您没有做好准备，用户的大量涌入可能是件好事！

网站流量的变化很难预测。您可能能够在日常基础上为用户负载提供服务，但是事件(预期的和意外的)可能会产生无法预料的后果。你的电子商务网站正在进行周末促销吗？你的网站会因为一些意想不到的赞扬而火起来吗？流量变化也会受到地理位置的影响。也许日本用户正经历着法国用户没有经历的流量负荷。您可能认为您的系统正在按预期工作，但所需要的只是大量用户的涌入来测试这种想法。如果一个事件需要 200 毫秒才能完成，但是您的系统一次只能处理一个事件，这可能看起来没有问题，直到事件队列突然被工作堵塞。

与延迟类似，在事件的整个生命周期中跟踪正在处理的事件数量对于了解任何瓶颈都很有用。例如，跟踪队列中的作业数量、每秒完成的 HTTP 请求数量以及活动用户数量，这些都是监控流量的良好起点。

对于因果数据，监控流量包括捕获服务如何相互传输信息，类似于我们对延迟所做的。您的监控设置应该跟踪对特定服务的请求数量、它们的响应代码、它们的有效负载大小等等——尽可能多地跟踪请求和响应周期。当您需要调查性能下降时，了解哪个服务遇到了问题将有助于您更快地跟踪可能的原因。

# 错误率

跟踪错误率相当简单。您的服务器作为 HTTP 响应发出的任何 5xx(甚至 4xx)都应该被标记和计数。即使是您已经考虑过的情况，比如被捕获的异常，也应该被监控，因为它们仍然代表一种非理想状态。这些问题可以作为更深层次问题的警告，这些更深层次的问题来自于没有解决实际问题的防御性编码。

库马可以捕获服务抛出的错误代码和消息，但这只是可操作数据的一部分。例如，您还可以捕获导致错误的参数(如果查询格式不正确)、发出的数据库查询(如果超时)、代理用户的权限(如果他们进行了未经授权的尝试)等等。简而言之，捕捉服务产生错误时的状态可以帮助您在开发和测试环境中重现问题。

# 浸透

您应该跟踪每个微服务的内存使用、CPU 利用率、磁盘读/写和可用存储。如果您的资源使用在特定的时间或操作中经常达到峰值，或者以稳定的速率增长，这表明您过度利用了您的服务器。虽然您的服务器可能会像预期的那样运行，但是流量的涌入或其他不可预见的事件可能会很快使它崩溃。

Kong Ingress 只监控网络活动，所以它不是跟踪饱和度的理想选择。然而，在 Kubernetes 上有许多工具可以用来跟踪这个问题。

# 实施监控和可观察性

到目前为止，我们已经讨论了在您的云应用程序中需要跟踪的重要指标。接下来，让我们深入一些具体的步骤来实现这种监控和可观察性。

## 安装普罗米修斯

Prometheus 是监控的首选标准——一个易于安装并与您的 Kubernetes 系统集成的开源系统。如果你使用[舵](https://helm.sh/)，安装会特别简单。

首先，我们创建一个监视名称空间:

```
$ kubectl create namespace monitoring
```

接下来，我们用头盔安装普罗米修斯。我们确保将普罗米修斯图表也添加到头盔中:

```
$ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts$ helm repo add stable https://kubernetes-charts.storage.googleapis.com/$ helm repo update$ helm install -f https://bit.ly/2RgzDtg -n monitoring prometheus prometheus-community/prometheus
```

https://bit.ly/2RgzDtg[引用的值文件](https://bit.ly/2RgzDtg)将普罗米修斯的数据抓取间隔设置为 10 秒。

## 启用孔中的普罗米修斯插件

假设您正在为 Kubernetes 使用 Kong Ingress 控制器(KIC ),您的下一步将是创建一个自定义资源—一个 KongPlugin 资源—它集成到 KIC 中。创建一个名为 prometheus-plugin.yml 的文件:

```
apiVersion: configuration.konghq.com/v1kind: KongClusterPluginmetadata: name: prometheus annotations: kubernetes.io/ingress.class: kong labels: global: "true"plugin: prometheus
```

## 安装 Grafana

Grafana 是一个可观测性平台，为可视化 Prometheus 收集的数据提供了优秀的仪表盘。我们使用 Helm 安装 Grafana，如下所示:

```
$ helm install grafana stable/grafana -n monitoring --values http://bit.ly/2FuFVfV
```

您可以在上面的命令中查看 bit.ly URL，以查看我们在安装时提供的 Grafana 的具体配置值。

## 启用端口转发

既然 Prometheus 和 Grafana 已经在我们的 Kubernetes 集群中启动并运行，我们需要访问他们的仪表板。对于本文，我们将设置基本的端口转发来公开这些服务。这是一种简单但不太安全的访问方式，不适合生产部署。

```
$ POD_NAME=$(kubectl get pods --namespace monitoring -l "app=prometheus,component=server" -o 
jsonpath="{.items[0].metadata.name}")
kubectl --namespace monitoring  port-forward $POD_NAME 9090 &$ POD_NAME=$(kubectl get pods --namespace monitoring -l "app.kubernetes.io/instance=grafana" -o 
jsonpath="{.items[0].metadata.name}")
kubectl --namespace monitoring port-forward $POD_NAME 3000 &
```

以上两个命令显示端口 9090 上的 Prometheus 服务器和端口 3000 上的 Grafana 仪表板。

这些简单的步骤应该足以让你开始运行。有了 Kong Ingress 控制器及其集成的 Prometheus 插件，使用 Prometheus 捕获指标并使用 Grafana 可视化它们的设置变得快速而简单。

# 结论

每当您需要调查不断恶化的性能时，您的影响数据指标可以帮助您确定问题的严重性，它应该会告诉您有多少人受到了影响。同样地，你的因果数据确定了什么没有起作用以及为什么。前者将你引向那缕烟，后者将你引向火焰。

除了以上所有内容，您还应该考虑指标变化的速度。例如，假设您的流量正在增加。观察这些数字移动的速度可以帮助你确定什么时候(或者是否)会成为一个问题。这对于管理您的服务的定期部署和变更的后续工作是至关重要的。它还确定了理想的性能指标应该是什么。

谷歌写了一整本关于网站可靠性的书，这是任何开发者的必读之作。如果您已经在您的集群中运行 Kong，插件[比如这个](https://github.com/Kong/kong-plugin-prometheus)直接与 Prometheus 集成，这意味着您需要做更少的配置来监控和存储您的服务的指标。