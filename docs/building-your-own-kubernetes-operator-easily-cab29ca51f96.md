# 轻松构建自己的 Kubernetes 运营商

> 原文：<https://betterprogramming.pub/building-your-own-kubernetes-operator-easily-cab29ca51f96>

## 这些讨厌的完成 Kubernetes 工作无处不在！让我们构建一个运算符来处理它们。

![](img/4582a4eb7bd1d6b1fec21d977fe32a8e.png)

照片由[王思然·哈德森](https://unsplash.com/@hudsoncrafted?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

有一点永远不变:你总是要做一些批处理。Kubernetes 通过使用`Job`对象有自己的批处理风格。例如，创建计算圆周率前 2000 位小数的作业很容易:

您将它推到您的集群中，观察作业的生命周期。应该在大约 10 秒钟后完成:

```
$ kubectl create -f job.yaml
$ kubectl get job piNAME   COMPLETIONS   DURATION   AGE
pi     1/1           10s         7s$ kubectl get pod | grep -E "NAME|pi-"NAME        READY   STATUS             RESTARTS   AGE
pi-pdrbw    0/1     Completed          0          3m40s
```

您注意到该作业被标记为已完成。它还创建了一个 pod 来运行给定的容器——这个容器也已经完成，并且由于错误或其他原因还没有重新启动。

如果您运行许多作业，或者如果您运行`[CronJob](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/)`，终止的作业数量将会迅速增加。一个好处是，作业历史允许您调查故障，或者让您的监控工具有时间报告这些错误。但是，另一方面，这些作业的绝对数量对 Kubernetes API 造成了压力，并使您的`kubectl get`命令输出变得混乱。

您可能想删除已终止的职务。为此，删除作业，然后[它会根据官方文档为您删除完成的 pod](https://kubernetes.io/docs/concepts/workloads/controllers/job/#:~:text=Delete%20the%20job%20with%20kubectl,it%20created%20are%20deleted%20too.):

```
$ kubectl delete job pi
$ kubectl get pod pi-pdrbw<nothing>
```

这很好，但如果它可以自动完成呢？

# 清除终止的 Kubernetes 作业

有几个选项可供选择:

*   **非最佳解决方案**:从外部工具自动运行您刚刚输入的命令行，该工具将定期运行 kubectl 命令。
*   **合理的解决方案**:简单地创建一个具有适当服务帐户权限的`CronJob`来执行一个脚本，该脚本将清理终止的作业。
*   **最佳解决方案**:在给定 TTL 后，使用 [TTL 控制器](https://kubernetes.io/docs/concepts/workloads/controllers/ttlafterfinished/)清理作业。

所以让我们安装 TTL 控制器吧！但是你很快就会意识到它仍然是它的 alpha 版本，隐藏在一个特性门的后面。你管理的 Kubernetes 最先进的云提供商集群[不允许你设置这些标志](https://github.com/aws/containers-roadmap/issues/255)，或者它允许但只在[阿尔法集群中设置，这些集群在 30 天后](http://managed Kubernetes cluster)会自动删除。

也许我们可以开发自己的控制器来处理这个问题，而不是将我们的整个集群转移到一个由 Kubespray 管理的庞然大物上。

# 开发作业监视器控制器

我们将按照操作符模式来开发控制器。

## 接线员？

操作符是一种特殊的 Kubernetes 控制器进程，它有自己的自定义资源定义——也就是说，是一个 Kubernetes 对象。这些 CRD 又允许您扩展 Kubernetes API 的功能。你可以在[官方文档](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/)中了解更多。

我们将使用[操作符 SDK](https://github.com/operator-framework/operator-sdk) ，而不是自己编写所有代码。它本质上是一个代码生成器，用来构建一个全功能的操作符。

首先，我们将[安装 SDK](https://sdk.operatorframework.io/docs/installation/install-operator-sdk/) 并运行一些命令来引导项目。

```
brew install operator-sdkoperator-sdk init job-purger --domain my.github.com --repo github.com/xxx/job-watcher-operatoroperator-sdk create api --group batch --kind JobWatcher --version v1 --resource true --controller true
```

## 创造你的 CRD

现在，您应该已经创建了一个完整的项目。我们先来看看`api/v1/jobwatch_types.go`。

这里，我们定义了 CRD 及其规格和状态信息:

*   用于已完成或失败作业的单独 TTL，在此之后作业将被删除。
*   一些命名空间和作业名模式(regex ),用于识别可能要删除的作业。
*   两次删除检查之间的延迟。

状态信息将是上次开始和结束的时间。注意，默认情况下，我们的操作符不是并发运行的。

像`//+kubebuilder`这样的特殊注释会为我们生成约束代码。

## 实施协调循环

*你可以在这里* *找到* [*完整的控制器代码。*](https://gist.github.com/esys/e9214229d175c265f37913e19def5dcc)

让我们转到实现代码。主要逻辑在对账循环中。它将接收一个`Request`作为参数，其中只包含资源的名称空间和名称。

这个方法将被调用用于你的操作者关心的每一个对象，当然，从我们的自定义`JobWatcher` CRD 开始。此外，生成的操作符自带一个记录器和一个 Kubernetes 客户端来操作对象。

实现的逻辑很简单:

1.  首先，我们将获取与`Request`匹配的`JobWatcher`对象。
2.  然后，我们将列出并保留与我们的名称空间模式之一相匹配的名称空间。
3.  接下来，我们将识别具有匹配名称的作业，如果作业终止且 TTL 过期，我们将删除它。
4.  最后，我们将更新 CRD 的状态信息。

## 讨论

当您的一个 CRD 改变或者返回的`ctrl.Result`不为空(或者返回一个错误)时，就会调用`Reconcile`。我们利用这种行为，以 CRD 规范中给定的频率重新调度对`Reconcile`函数的调用。

如果您指定了生成器注释:`//+kubebuilder:rbac:groups:resources:verbs`，那么集群角色和绑定会自动创建。

在`SetupWithManager`方法中，我们表示操作员正在通过`For`方法管理`JobWatcher` CRD。其他方法允许您收到其他对象的通知:

*   `Owns()`对于您正在创建的子对象。
*   `Watches()`对于集群中的任何对象。

例如，如果您添加了`Owns(&kbatch.Job{})`，那么您的协调循环将为每个创建、删除或修改的作业调用，并且您的 CRD 作为其[所有者](https://kubernetes.io/docs/concepts/workloads/controllers/garbage-collection/#owners-and-dependents)。`Reconcile`方法的输入`Request`参数将是作业的所有者实例。当然，这里我们不是在创造就业机会，所以这只是为了举例。

与我们更相关的是，如果您在构建器中为`Job`类添加一个对`Watches`方法的调用，那么集群中作业的每个变化都会通知您。您可以(也应该)通过设置适当的选项来过滤您感兴趣的事件。

最后，不要忘记在循环结束时更新 CRD 的状态。

# 构建和部署操作符

Makefile 提供了生成清单、构建 Go 应用程序、将 Docker 映像推送到您选择的注册表以及打包应用程序所需的一切。

[准备好测试 Kubernetes 集群](https://codeburst.io/creating-a-local-development-kubernetes-cluster-with-k3s-and-traefik-proxy-7a5033cb1c2d)并配置好您的 kubectl。安装目标将生成清单，并使用 Kustomize 将它们部署到您的集群:

```
make install
```

清单现在位于`config`文件夹下，包含所有 Kustomize 补丁。

然后，出于开发目的，您可以直接在集群之外运行您的操作符二进制文件。它将使用您的 kubectl 配置与您的集群通信。

```
$ make run 2020-11-09T20:56:08.279+0100    INFO    controller-runtime.metrics      metrics server is starting to listen    {"addr": ":8080"}
2020-11-09T20:56:08.281+0100    INFO    setup   starting manager
2020-11-09T20:56:08.281+0100    INFO    controller-runtime.manager      starting metrics server {"path": "/metrics"}
2020-11-09T20:56:08.281+0100    INFO    controller-runtime.controller   Starting EventSource    {"controller": "jobwatcher", "source": "kind source: /, Kind="}
2020-11-09T20:56:08.382+0100    INFO    controller-runtime.controller   Starting Controller     {"controller": "jobwatcher"}
2020-11-09T20:56:08.382+0100    INFO    controller-runtime.controller   Starting workers        {"controller": "jobwatcher", "worker count": 1}
```

现实部署的替代方案是将控制器打包成 Docker 映像(运行`make docker-build docker-push`)并用`make deploy`生成一个`Deployment`对象。

# 测试操作员

为了进行测试，我们将首先创建 CRD 的一个实例:

和一个测试任务:

我们应用两种资源:

```
$ kubectl apply -f jobwatcher-sample.yamljobwatcher.batch.my.github.com/jobwatcher-sample created$ kubectl apply -f job.yamljob.batch/pi created# after a few seconds, but before TTL expired$ kubectl get job pi   
NAME   COMPLETIONS   DURATION   AGE
pi     1/1           6s         15s
```

如果您查看一下控制器日志，应该会看到在 TTL 结束后，作业被删除了。

```
2020-11-12T08:23:55.056+0100    INFO    controllers.JobWatcher  deleted terminated job  {"JobWatcher": "default/jobwatcher-sample", "Job": "default/pi", "Finished": "2020-11-12T08:23:40.000+0100", "Elapsed": "15.043938698s"}
```

您可以确认作业已被删除:

```
kubectl get job pi<nothing>
```

# 不完全适合生产

作为一个例子，这个操作符已经构建得足够好了，但是它肯定不能用于生产。

## 调整日志级别

首先，您需要调整日志级别。由于 logger 实现是 Zap，您应该向命令行公开 logger 标志。修改`main.go`文件，添加`BindFlags`，如下所示:

然后，您可以使用更适合生产的设置运行:

```
make run ARGS="--zap-devel=false --zap-log-level=warn"
```

如果您想在集群中将控制器部署为容器时保留任何标志，请编辑`config/default/manager_auth_proxy_patch.yaml`。

## 添加网页挂钩

您可能想添加一个 [admission webhook](https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/) 来检查您的 CRD——例如，验证所使用的 job-name 模式是否是格式良好的正则表达式。

SDK 操作符通过创建服务器和注册钩子使得部署 webhook 变得容易。更多详情[此处](https://book.kubebuilder.io/cronjob-tutorial/webhook-implementation.html)。

## 查看代码逻辑

例如，如果您的集群中有这个 CRD 的多个实例，会发生什么情况？由于每个 CRD 的范围显然是全局的，您可能希望合并它们的配置。

如果有很多名称空间和作业或者 TTL 很低会怎么样？这个控制器会变慢，并进行大量的 kube API 调用。

出于所有这些原因，尽管它适用于中小型集群，但是您不应该在大型集群中运行它。更好的编码方式是:

*   让`JobWatcher` CRD 只处理它所在的名称空间中的作业。
*   如果在一个名称空间中创建了多个`JobWatcher`，那么在控制器全局范围内合并它们的配置。
*   此外，您应该配置您的操作员来观察`Job`上的更新事件，而不是每隔 TTL 秒触发一次协调循环。这样，您将避免对 API 的不必要调用。

# 结论

如果您很好奇，看一眼生成的代码量，您会意识到即使编写一个简单的操作符也不是一件容易的事情。通过为您处理底层问题，运营商 SDK 在抽象这种复杂性方面做得非常好。