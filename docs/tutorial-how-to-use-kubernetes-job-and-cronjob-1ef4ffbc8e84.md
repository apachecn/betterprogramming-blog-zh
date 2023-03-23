# 如何使用 Kubernetes 的 Job 和 CronJob

> 原文：<https://betterprogramming.pub/tutorial-how-to-use-kubernetes-job-and-cronjob-1ef4ffbc8e84>

## 安排代码在您的容器化环境中运行

![](img/9d91e2b5353e31c4b77fbb38039bb892.png)

[格兰特·里奇](https://unsplash.com/@grantritchie?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/environment?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

欢迎来到博客系列的另一部分“一言以蔽之的 Kubernetes”。到目前为止，我们已经讨论了 Kubernetes 资源(对象)，比如`Deployment` s、`Service` s、`Volume` s 等。

在这篇博客中，我们将探索`Job`和`CronJob`。借助示例，您将了解到:

*   如何使用这些组件。
*   指定时间限制、并发性等约束。
*   处理故障等。

代码(YAML 的很多)在 GitHub 上有[。](https://github.com/abhirockzz/kubernetes-in-a-nutshell)

# 库伯内特工作

您可以使用 Kubernetes `Job`来运行批处理、ETL 作业、特别操作等。它从一个`Pod`开始，并让它运行到完成。这与其他`Pod`控制器如`Deployment`或`ReplicaSet`完全不同。

一如既往，我们将在实践中学习。所以，让我们开始吧！

## 你好工作！

下面是一个典型的`Job`清单:

```
apiVersion: batch/v1
kind: Job
metadata:
  name: job1
spec:
  template:
    spec:
      containers:
        - name: job
          image: busybox
          args:
            - /bin/sh
            - -c
            - date; echo sleeping....; sleep 90s; echo exiting...; date
      restartPolicy: Never
```

这个`Job`将简单地启动一个`[busybox](https://hub.docker.com/_/busybox)`容器，该容器简单地执行一些 shell 命令。让我们创建这个`Job`并调查发生了什么

为了简单起见，YAML 文件直接从 [GitHub repo](https://github.com/abhirockzz/kubernetes-in-a-nutshell) 中引用，但是您也可以将该文件下载到您的本地机器上，并以同样的方式使用它。

```
kubectl apply -f [https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/jobs/job1.yaml](https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/jobs/job1.yaml)
```

检查`Job`及其相关的`Pod`。

```
kubectl get job/job1NAME   COMPLETIONS   DURATION   AGE
job1   0/1           8s         8s
```

您应该在`Running`状态下看到一个`Pod`，例如:

```
kubectl get pod -l=job-name=job1job1-bptmd 1/1  Running
```

如果您检查`Pod`日志，您应该会看到类似如下的内容:

```
kubectl logs <pod_name>Thu Jan  9 10:10:35 UTC 2020
sleeping....
```

90 后再检查一下工作。

```
kubectl get job/job1NAME   COMPLETIONS   DURATION   AGE
job1   1/1           95s        102s
```

`Job`跑了 90 秒多一点，而`COMPLETIONS`反映出一个`Pod`成功完成。这也将反映在`Pod`日志中。

```
Thu Jan  9 10:10:05 UTC 2020
sleeping....
exiting...
Thu Jan  9 10:11:35 UTC 2020
```

另外，`Pod`状态应该变为`Completed`。

```
kubectl get pod -l=job-name=job1job1-bptmd 0/1  Completed
```

如果所有的`Job`只是创建一个`Pod`来运行一个容器，为什么我们不能使用一个普通的旧`Pod`？

这是因为如果容器发生故障，Kubernetes 可以重启一个`Job`，而隔离的`Pod`不会发生这种情况。除此之外，作业控制器还提供了许多其他功能，我们将继续探索这些功能。

要删除这个`Job`，只需运行`kubectl delete job/job1`。

## 强制执行时间限制

例如，您正在运行一个批处理作业，由于某种原因，它需要很长时间才能完成。这可能是不可取的。您可以通过设置规范中的`activeDeadlineSeconds`属性来限制`Job`继续运行的时间。

这里有一个例子:

```
apiVersion: batch/v1
kind: Job
metadata:
  name: job2
spec:
  activeDeadlineSeconds: 5
  template:
    spec:
      containers:
        - name: job
          image: busybox
          args:
            - /bin/sh
            - -c
            - date; echo sleeping....; sleep 10s; echo exiting...; date
      restartPolicy: Never
```

请注意，`activeDeadlineSeconds`已被设置为 5 秒，而容器进程已被指定运行 10 秒。

创建`Job`，等待几秒钟(~10 秒)并检查`Job`。

```
kubectl apply -f https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/jobs/job2.yamlkubect get job/job2 -o yaml
```

向下滚动检查`status`字段，您将看到`Job`由于`DeadlineExceeded`而处于`Failed`状态。

```
status:
  conditions:
  - lastProbeTime: "2020-01-09T10:57:13Z"
    lastTransitionTime: "2020-01-09T10:57:13Z"
    message: Job was active longer than specified deadline
    reason: DeadlineExceeded
    status: "True"
    type: Failed
```

要删除作业，只需运行`kubectl delete job/job2`。

## 处理故障

如果由于容器故障(进程退出)或`Pod`故障而出现问题，该怎么办？让我们通过模拟一次失败来尝试一下。

在此`Job`中，容器打印`date`、`sleep`s 5 秒，并以*状态 1* 退出以模拟故障。

```
apiVersion: batch/v1
kind: Job
metadata:
  name: job3
spec:
  backoffLimit: 2
  template:
    spec:
      containers:
        - name: job
          image: busybox
          args:
            - /bin/sh
            - -c
            - date; echo sleeping....; sleep 5s; exit 1;
      restartPolicy: OnFailure
```

请注意，`restartPolicy: OnFailure`与之前设置为`Never`的示例不同。我们一会儿将回到这一点。

创建`Job`并关注该工作的特定`Pod`。

```
kubectl apply -f https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/jobs/job3.yamlkubectl get pod -l=job-name=job3 -w
```

您应该会看到类似下面的内容:

```
NAME                                     READY   STATUS              RESTARTS   AGE
job3-qgv4b                               0/1     ContainerCreating   0          4s
job3-qgv4b                               1/1     Running             0          6s
job3-qgv4b                               0/1     Error               0          12s
job3-qgv4b                               1/1     Running             1          17s
job3-qgv4b                               0/1     Error               1          22s
job3-qgv4b                               0/1     CrashLoopBackOff    1          34s
job3-qgv4b                               1/1     Running             2          40s
job3-qgv4b                               1/1     Terminating         2          40s
job3-qgv4b                               0/1     Terminating         2          45s
job3-qgv4b                               0/1     Terminating         2          51s
```

注意`Pod`状态是如何转变的。

*   它从拉动和运行容器开始。
*   它转换到`Error`状态，因为它以状态 1 退出(休眠 5 秒后)。
*   它再次回到`Running`状态(注意`RESTARTS`计数现在是 1)。
*   不出所料，它再次进入`Error`状态，并再次重启- `RESTARTS`计数现在为 2。
*   最后是`terminated`。

Kubernetes(具体来说是作业控制器)为我们重启了容器，因为我们指定了`restartPolicy: OnFailure`。

但是可能会有一种情况，这种情况可能会无限期地持续下去，所以我们使用`backoffLimit: 2`对此加以限制，这将确保 Kubernetes 在将这个`Job`标记为`Failed`之前只重试两次。

注意，这是一个重启容器的例子。在`Pod`出现故障的情况下，作业控制器也可以创建一个新的`Pod`。

如果您检查`Job`状态...

```
kubectl get job/job3 -o yaml
```

…您会看到，由于`BackoffLimitExceeded`，它有`Failed`。

```
status:
  conditions:
  - lastProbeTime: "2020-01-09T11:16:24Z"
    lastTransitionTime: "2020-01-09T11:16:24Z"
    message: Job has reached the specified backoff limit
    reason: BackoffLimitExceeded
    status: "True"
    type: Failed
```

`Never`中的`restartPolicy`表示出错时不会重启容器或创建新的`Pod`。另外，`backoffLimit`的默认限值是`6`。

要删除此作业，只需运行`kubectl delete job/job3`。

## 越多越好

有些情况下，你可能希望`Job`带动不止一个`Pod`来完成任务。

例如，考虑一个场景，您正在运行一个批处理作业来处理来自一个数据库的记录——让多个`Pod`分担负载肯定会有所帮助。

一种方法是让每个`Pod`按顺序运行，记录外部源(例如另一个数据库表)中处理的行数，另一个`Pod`可以从那里开始。

这可以通过在`Job`规范中添加`completions`属性来实现。

```
apiVersion: batch/v1
kind: Job
metadata:
  name: job4
spec:
  completions: 2
  template:
    spec:
      containers:
        - name: job
          image: busybox
          args:
            - /bin/sh
            - -c
            - date; echo sleeping....; sleep 10s; echo exiting...; date
      restartPolicy: Never
```

创建`Job`并关注它的进展。

```
kubectl apply -f https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/jobs/job4.yamlkubectl get job/job4 -w
```

您应该会看到类似这样的内容:

```
NAME   COMPLETIONS   DURATION   AGE
job4   0/2           3s         3s
job4   1/2           20s        20s
job4   2/2           37s        37s
```

因为我们已经将`completions`设置为 2:

*   两个`Pod`一个接一个地被实例化(按顺序)。
*   只有在两个`Pod`都运行完成后，`Job`才被标记为`Completed`(成功)。否则，就会出现故障条件(如上所述)。

让我们也检查一下`Pod`日志。

```
kubectl get pods -l=job-name=job4
kubect logs <pod_name>
```

如果您看到两个`Pod`的日志，您将能够确认它们按顺序一个接一个地启动(并且每个运行了大约 10 秒)。

`Pod` 1 的日志。

```
Thu Jan  9 11:31:57 UTC 2020
sleeping....
exiting...
Thu Jan  9 11:32:07 UTC 2020
```

`Pod` 2 的日志。

```
Thu Jan  9 11:32:15 UTC 2020
sleeping....
exiting...
Thu Jan  9 11:32:25 UTC 2020
```

以并行的方式运行批处理怎么样，所有的`Pod`被一次实例化(而不是顺序地)？

为了处理这种情况，我们的处理逻辑需要相应地调整，因为在选择哪组工作项以及如何更新它们的完成状态方面，并行`Pod`之间需要协调。

我们不会深入探讨这个问题，但我希望你能从需求方面了解这个想法。

现在，这可以通过使用`parallelism`和`completions`来实现。这里有一个例子:

```
apiVersion: batch/v1
kind: Job
metadata:
  name: job5
spec:
  completions: 3
  parallelism: 3
  template:
    spec:
      containers:
        - name: job
          image: busybox
          args:
            - /bin/sh
            - -c
            - date; echo sleeping....; sleep 10s; echo exiting...; date
      restartPolicy: Never
```

通过使用`parallelism`属性，我们能够对一次可以运行的`Pod`的最大数量设置上限。在这种情况下，由于`parallelism`被设置为 3，这意味着:

*   三个`Pod`将同时被实例化。
*   只有三个都运行完成时，`Job`才会被标记为`Completed`(成功)。否则，故障条件适用(如上所述)。

## 一旦你完成了

您可以使用`ttlSecondsAfterFinished`指定`Job`完成后自动删除的秒数(可以是`Completed`或`Failed`)。这也删除了依赖实体，例如由`Job`产生的`Pod`。

# 克朗乔布

一个`CronJob`对象允许您安排`Job`的执行，而不是手动启动它们。

它使用 [Cron](https://en.wikipedia.org/wiki/Cron) 格式按计划运行作业。基本上，`CronJob`是一个更高层次的抽象，它内嵌了一个`Job`模板(如上所示)以及一个时间表(cron 格式)和其他属性。

让我们创建一个简单的每分钟重复一次的`CronJob`。

```
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: cronjob1
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: cronjob
              image: busybox
              args:
                - /bin/sh
                - -c
                - date; echo sleeping....; sleep 5s; echo exiting...;
          restartPolicy: Never
```

`jobTemplate`部分与`Job`部分相同。它只是嵌入在这个`CronJob`规格中。这与我们在`Job`示例中使用的容器相同。

创建`CronJob`并检查它:

```
kubectl apply -f https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/jobs/cronjob1.yamlkubectl get cronjob/cronjob1
```

输出:

```
NAME       SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob1   */1 * * * *   False     0        <none>          4s
```

跟踪这个`CronJob`产生的`Job`。

```
kubectl get job -wNAME                  COMPLETIONS   DURATION   AGE
cronjob1-1578572340   0/1           2s         2s
cronjob1-1578572340   1/1           11s        11s
cronjob1-1578572400   0/1                      0s
cronjob1-1578572400   0/1           0s         0s
cronjob1-1578572400   1/1           10s        10s
cronjob1-1578572460   0/1                      0s
cronjob1-1578572460   0/1           0s         0s
cronjob1-1578572460   1/1           11s        11s
```

每分钟都有一个新的`Job`被创建，它运行了大约 10 秒钟。您还可以检查由`Job`创建的个人`Pod`的日志(就像您在前面的例子中所做的那样)。

```
kubectl get pod -l=job-name=<job_name>
kubectl logs <pod_name>
```

除了`schedule`属性之外，还有其他(可选)`CronJob`属性。让我们来看看其中的一个。

## 并行政策

它有三个可能的值— `Forbid`、`Allow`和`Replace`。

如果您不想同时执行您的`Job`，请选择`Forbid`。当按照时间表触发一个`Job`并且一个`Job`实例已经在运行时，当前的迭代被跳过。

如果您选择`Replace`作为并发策略，当前运行的`Job`将被停止，一个新的`Job`将被生成。

指定`Allow`将让多个`Job`实例并发运行。

这里有一个例子:

```
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: cronjob2
spec:
  schedule: "*/1 * * * *"
  concurrencyPolicy: Allow
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: cronjob
              image: busybox
              args:
                - /bin/sh
                - -c
                - date; echo sleeping....; sleep 90s; echo exiting...;
          restartPolicy: Never
```

您可以创建这个`CronJob`，然后跟踪单个的`Job`来观察行为。

```
kubectl apply -f https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/jobs/cronjob2.yamlkubectl get job -w
```

由于时间表是每一分钟，容器运行 90 秒，您将看到多个`Job`同时运行。这种重叠是可能的，因为我们已经应用了`concurrencyPolicy: Allow`。

您可能会看到类似这样的内容:

```
cronjob2-1578573480   0/1                      0s
cronjob2-1578573480   0/1           0s         0s
cronjob2-1578573540   0/1                      0s
cronjob2-1578573540   0/1           0s         0s
cronjob2-1578573480   1/1           95s        95s
```

请注意，作业`cronjob2-1578573540`在`cronjob2-1578573480`完成之前被触发。

`CronJob`的其他属性有:

*   作业历史:`successfulJobsHistoryLimit`和`failedJobsHistoryLimit`可用于指定您希望为失败和完成的`Job`保留多少历史
*   由`startingDeadlineSeconds`指定的开始截止日期。
*   由`suspend`指定暂停。

# 结论

这就是“果壳中的库伯内特”系列的这一部分。敬请关注更多内容。

我真的希望你喜欢这篇文章，并从中学到了一些东西。很高兴收到您的反馈意见。