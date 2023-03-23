# 使用 Kubernetes Executor 和带 Istio 的 Kubernetes Pod 操作器运行气流

> 原文：<https://betterprogramming.pub/running-airflow-using-kubernetes-executor-and-kubernetes-pod-operator-with-istio-d5aa7af16ef5>

## 气流系列

## 克服障碍

![](img/2a75102370cb0b4f0d7e895c894c43c4.png)

爱德华·拉巴尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 什么是 Kubernetes 遗嘱执行人

Kubernetes executor 在 Kubernetes 集群上自己的 pod 中运行每个任务实例。这意味着对于 dag 中的每个任务，Kubernetes 集群中都会生成一个独立的 pod。pod 的图像是基于气流的图像。pod 将运行您的任务、PythonOperator、BashOperator 等。它基本上使用 pod 内的气流基本映像运行气流命令“气流任务运行…”。

[](https://airflow.apache.org/docs/apache-airflow/stable/executor/kubernetes.html) [## Kubernetes 执行器-气流文件

### Kubernetes executor 在 Kubernetes 集群上自己的 pod 中运行每个任务实例。KubernetesExecutor 作为一个…

airflow.apache.org](https://airflow.apache.org/docs/apache-airflow/stable/executor/kubernetes.html) 

# 什么是 Kubernetes Pod 操作员

Kubernetes Pod 操作符也在 Kubernetes 集群上创建一个新的 Pod，但是图像是由您使用操作符中的图像参数来定义的。它基本上调用 Kubernetes API 来启动一个 pod 并运行您的图像，然后记录您的结果。

 [## KubernetesPodOperator-Apache-气流-提供商-cncf-kubernetes 文档

### KubernetesPodOperator 使用 Kubernetes API 在 Kubernetes 集群中启动一个 pod。通过提供图像 URL…

airflow.apache.org](https://airflow.apache.org/docs/apache-airflow-providers-cncf-kubernetes/stable/operators.html) 

# 什么是 Istio

用最简单的语言来说，它是一个服务网络层。它控制流量如何通过每个服务。由于现代微服务架构通常包含数百或数千个服务，因此需要另一层来处理所有网络流量。它被称为服务网格。

Istio 将在您的 pod 中注入一个名为 istio-proxy 的 sidecar，并为您处理流量。但是这阻碍了 Kubernetes 执行者和 Kubernetes Pod 操作者的正常工作流程。这是因为它们都使用 Pod 阶段来确定 dag 任务的状态。当 Pod 阶段成功和失败时，气流将认为任务完成。由于 istio-proxy 边车，pod 将一直运行。只有任务容器本身完成了。这将导致成功任务中的 Pod 阶段变为未就绪，以及失败任务中的错误。在 Airflow UI 中，你会看到任务一直在运行，永无止境。

## 虚幻的希望

当在 google 中进行初始搜索时，有一个使用 Kubernetes 生命周期类型的优雅解决方案:Sidecar。就像下面的配置一样，您只需要将容器生命周期类型设置为 sidecar，然后 Kubernetes 会处理所有其他的事情。

```
apiVersion: v1
kind: Pod
metadata:
  name: bookings-v1-b54bc7c9c-v42f6
  labels:
    app: demoapp
spec:
  containers:
  - name: bookings
    image: banzaicloud/allspark:0.1.1
    ...
  - name: istio-proxy
    image: docker.io/istio/proxyv2:1.4.3
    lifecycle:
      type: Sidecar
    ...
```

[](https://banzaicloud.com/blog/k8s-sidecars/) [## Kubernetes 1.18 中边车集装箱生命周期的变化

### 更新:看起来下面描述的解决方案终究不会登陆 Kubernetes。开发人员回去了…

banzaicloud.com](https://banzaicloud.com/blog/k8s-sidecars/) 

然而，不知何故，上面描述的解决方案并没有像预期的那样在 Kubernetes 1.18 中真正实现。

另一种方法是在以下情况下包装容器命令:

```
- command:
  - /bin/sh
  - -c
  - |
    until curl -fsI http://localhost:15021/healthz/ready; do echo \"Waiting for Sidecar...\"; sleep 3; done;
    echo \"Sidecar available. Running the command...\";
    <YOUR_COMMAND>;
    x=$(echo $?); curl -fsI -X POST http://localhost:15020/quitquitquit && exit $x
```

开始时，它使用 Istio 端点检查边车是否已经启动。如果它启动了，那么运行您通常的命令。容器完成后，它使用 Istio quitquitquit endpoint 关闭 istio-proxy。此功能在 Istio 1.7 之后可用

[](https://stackoverflow.com/questions/54921054/terminate-istio-sidecar-istio-proxy-for-a-kubernetes-job-cronjob) [## 为 kubernetes 作业/ cronjob 终止 istio sidecar istio 代理

### 我们最近开始使用 istio Istio 在我们的 Kubernetes 景观内建立服务网络。我们现在有…

stackoverflow.com](https://stackoverflow.com/questions/54921054/terminate-istio-sidecar-istio-proxy-for-a-kubernetes-job-cronjob) 

乍一看，这可能是我们渴望的解决方案。但是总的来说它失败了，因为我们不能覆盖生成的 pod 的命令。正如我之前所说的，Kubernetes executor 基本上使用 pod 内部的气流基础映像来运行气流命令“气流任务运行…”。我们需要侵入 Kubernetes 执行器的源代码来添加包装器。我们绝对不想这样做。

该脚本在与气流相关的作业或 cronjobs 中仍然有用，例如迁移数据库作业。如果您使用舵图来部署气流，以下是您应该在值 YAML 中输入的内容。

```
migrateDatabaseJob:
  args:
    - "bash"
    - "-c"
    - |-
    until curl -fsI http://localhost:15021/healthz/ready; do echo \"Waiting for Sidecar...\"; sleep 3; done; echo \"Sidecar available. Running the command...\";
    airflow {{ semverCompare ">=2.0.0" .Values.airflowVersion | ternary "db upgrade" "upgradedb" }}; x=$(echo $?); curl -fsI -X POST http://localhost:15020/quitquitquit && exit $x
```

# 更持久的解决方案

## 天文学家

[](https://www.astronomer.io/) [## 现代数据编排-天文学家

### 使用 Astro 构建、运行和观察数据管道，Astro 是由 Apache 提供支持的基本数据编排平台…

www .天文学家. io](https://www.astronomer.io/) 

天文学家 image 已经处理了这个问题，他们做了一个全新的 Istio 类来处理这个问题。你可以在这里查看他们的代码:【https://github.com/astronomer/airflow/pull/62/files】T2

他们利用 Istio API 端点 quitquit，在任务完成时关闭 istio-proxy。

使用天文学家的解决方案有一个主要缺点。该解决方案只处理 Istio 代理。还有许多其他的边车可用，例如，保险库代理注射器。Vault 是一个机密存储库，vault agent injector 使用 pod 注释来控制使用 sidecar 将哪个机密注入到 Pod 中。在这种情况下，使用天文学家的图像仍然面临同样的问题。

## 杀死 Sidecar 的 CronJob

感谢[约格什·夏尔马](https://yogeshsharma.me/)为我们提供了一个关闭所有边车的有用脚本。我们可以部署一个 CronJob 来使用 Kubernetes API 检查 pod 状态，并在任务完成时终止 sidecars。我们需要对容器状态进行一些检查。如果容器完成了，container _ statuses 返回的字典将有一个名为`terminated`的字段。同样默认情况下，Airflow 将任务容器命名为`base`。

我们检查容器状态，如果它被终止，那么我们在其他容器上执行 exit 命令并杀死它。

[](https://github.com/kubernetes-client/python/issues/1138#issuecomment-717977206) [## 从另一个容器中停止 Pod 中的容器问题#1138 kubernetes-client/python

### 在 pod 中停止/终止另一个集装箱中的一个集装箱我有一个需要停止侧车的用例…

github.com](https://github.com/kubernetes-client/python/issues/1138#issuecomment-717977206) 

这处理了边车问题，并防止修改气流 Kubernetes 执行代码库。然而，Kubernetes Pod 操作员可以在任意名称空间上部署 Pod，让 cronjob 在每个名称空间上终止 sidecars 并不是一个好的做法。它可能会意外杀死其他有用的豆荚。(也许有些人在他们的吊舱中使用了相同的名字“基地”)

## 定制 Kubernetes Pod 操作员

最好的方法是实现一个定制的 Kubernetes Pod 操作符，它在基本容器(运行任务的容器)完成时关闭 Pod。它包括创建一个定制的吊舱发射器。

让我告诉你如何一步一步地构建它。如果您跟踪 Kubernetes Pod 操作符的源代码，您会发现关键部分在`execute`方法中。它首先创建一个 pod ( `get_or_create_pod`)，然后等待 pod 启动(`await_pod_start`)。然后使用 pod 管理器的`await_pod_completion`等待 Pod 完成。这是我们希望在自定义 pod 管理器中覆盖的方法。我们需要的是一个 pod 管理器，它可以在基本容器完成时考虑 Pod 已完成。下面是 pod 管理器的代码片段，您可以在第 55 到 56 行看到我们添加了检查来确定 Pod 是否完成。如果 pod 状态阶段正在运行(因为 istio 代理边车仍在运行),但基本容器已完成，则我们认为 pod 也已完成。

`container_is_completed`方法就像本地的`container_is_running`方法一样。如果容器完成了，那么 Kubernetes API 的响应负载中的`container_status.state.terminated`将会有完成信息。要检查是否成功，exit_code 字段将为 0，否则，它将是另一个退出代码。

接下来就简单了，我们只需在 Kubernetes pod 操作器中使用 Pod 管理器。正如您在下面看到的，我们的自定义操作符初始化了一个自定义 pod 管理器。然后我们覆盖原来的清理方法，只修改第 8 行。这一行检查任务是否失败。如果基础容器不成功，我们只考虑 pod 失败。

这样，我们终于可以在启用 Istio 的情况下使用 Kubernetes executor 和 Kubernetes Pod Operator 运行 Airflow 了！

![](img/8565de56d5a4693ccadad69679a69907.png)

[杰森·霍根](https://unsplash.com/@jasonhogan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 一些预防措施

[](https://github.com/istio/istio/issues/25157) [## 当名称长度超过 63 个字符时，Pod 创建失败问题#25157 istio/istio

### 由于 service.istio.io/canonical-name 标签，当名称长度超过 63 个字符时，错误描述窗格创建失败…

github.com](https://github.com/istio/istio/issues/25157) 

当您的任务名称足够长时，您可能会遇到 Pod 创建失败问题。气流会在你的任务名称后随机添加一个后缀，形成吊舱名称。虽然 Airflow 会自动处理长任务名称并安全地构建 pod 创建，但 Istio 会添加一个使用您的 pod 名称的附加标签`service.istio.io/canonical-name`。该标签名称将超过 63 个字符，因此 pod 无法创建。

感谢[凯文·普林](https://github.com/kppullin)为我们提供了变通办法。我们可以给工人分配一个特定的 app.kubernetes.io/name 标签或者在 pod 模板中解决这个问题。如果您使用官方的舵图来部署气流，我们可以在标签字段`app.kubernetes.io/name: some_name`中进行设置。这将修复 Kubernetes 执行器生成工人舱的问题。

对于 Kubernetes Pod 操作员，我们再次需要修改我们的客户 Kubernetes Pod 操作员。我们还需要将标签字段添加到 pod，因此我们在自定义操作符`__init__`方法中将标签添加到 kwargs，并传递给原始构造函数。

```
def __init__(self, *args, **kwargs):
  kwargs["labels"] = kwargs.get("labels", {}) | {"app.kubernetes.io/name": "airflow_pod"}
  super().__init__(*args, **kwargs)
```

# 结论

当我们使用 Kubernetes executor 和 Kubernetes Pod Operator 将气流部署到支持 Istio 的 Kubernetes 集群时，会遇到许多障碍和困难。Istio 代理边车妨碍了 Kubernetes 执行者和 Kubernetes Pod 操作员的正常工作流程。它使工蜂舱永远挂起并运行。

气流永远不知道任务完成了。通过实现一个从 Kubernetes Pod Operator 继承的自定义操作符，并实现一个 cronjob 来终止所有这些边车，我们可以成功地将气流部署到一个支持 Istio 的 Kubernetes 集群。我希望本文能以我们的经验帮助您更顺利地部署。