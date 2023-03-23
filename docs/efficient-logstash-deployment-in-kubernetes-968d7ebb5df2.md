# Kubernetes 中高效的 Logstash 部署

> 原文：<https://betterprogramming.pub/efficient-logstash-deployment-in-kubernetes-968d7ebb5df2>

## 帮助您有效管理管道和配置的简要指南

![](img/ddb6e45ac171006bac52c997df03ddd4.png)

[T K](https://unsplash.com/@realaxer?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

在本文中，我们将展示如何使用 Helm 轻松地将 Logstash 部署到 Kubernetes 集群中，以及如何管理管道和配置，以便它们在将来易于维护。

# 日志存储部署

对于基本的 Logstash 部署，我们所要做的就是使用 Elastic 的官方舵图:

1.  添加弹性回购:`helm repo add elastic [https://helm.elastic.co](https://helm.elastic.co)`
2.  安装日志存储图表:`helm install logstash elastic/logstash`
3.  使用`kubectl get po | grep logstash`验证 Logstash pod 正在运行

Logstash 应该已经启动并运行了，但它并没有做太多事情——默认情况下，它配置了一个名为`main`的管道，该管道监听端口 5044 上的 beats 数据，并将它们输出到 stdout。

# 配置

我们将使用 Kubernetes 配置映射来配置我们的 Logstash 部署。首先，我们将创建一个新的舵图，它使用 Logstash 舵图作为子图:

1.  创建新图表:`helm create <chart name>`
2.  清理预先创建的模板:`rm -rf <chart name>/templates/*`
3.  在您喜欢的文本编辑器中打开`<chart name>/Chart.yaml`并添加`dependencies`字段:

<chart name="">/Chart.yaml</chart>

`dependencies`部分定义了我们使用 Logstash 图表作为子图表。

现在，我们将通过向新创建的图表的`templates`目录中添加两个配置映射来添加我们的自定义配置，首先是替换默认配置文件的基本 logstash 配置:

<chart name="">/templates/logstash-config . YAML</chart>

第二个配置图将包含管道定义:

<chart name="">/templates/pipeline . YAML</chart>

如您所见，我们使用管道分割，正如我以前的一篇关于 [Logstash 最佳实践](https://medium.com/ableneo/best-practices-for-logstash-81e1eb6a6262)【1】的文章中所描述的。

现在我们必须告诉 Logstash 子图表安装这些配置图。为此，我们必须修改图表根目录中的`values.yaml`:

<chart name="">/values.yaml</chart>

我们只挂载来自`logstash-config`配置图的特定文件，保留其他默认配置文件，例如 log4j 配置。有关字段的完整列表，请参见 Logstash chart `[values.yaml](https://github.com/elastic/helm-charts/blob/main/logstash/values.yaml)` [2]。

完成这些更改后，图表就可以部署了:

1.  卸载以前的 logstash chart 安装:`helm uninstall logstash`
2.  让 Helm 下载 logstash 子图表:`helm dep build <chart name>/`
3.  安装新图表:`helm install logstash <chart name>/`

现在，我们可以检查是否部署了配置映射:

```
$ kubectl get configmaps               
NAME               DATA   AGE
logstash-config    2      4s
pipeline-config    4      4s
```

和 Logstash pod 正在运行:

```
$ kubectl get pods                     
NAME                  READY   STATUS    RESTARTS   AGE
logstash-logstash-0   1/1     Running   0          3m38s
```

我们的 Logstash 现在正在运行，并使用我们在(父)图表中定义的配置映射中的配置。

要修改管道，我们所要做的就是修改`pipelines.yaml`文件并运行`helm upgrade logstash <chart name>/`命令。这将把图表中的所有更改传播到 Kubernetes，并重新启动 Logstash pods。

# 从文件加载管道配置

在复杂管道的情况下，我们可以使用 Helm 文件操作命令来生成配置图。

首先，我们将管道配置文件提取到图表根目录下的单独文件夹中:

```
<chart name>/
  - pipeline/
    - audit.conf
    - main_01_input.conf
    - main_02_filter.conf
    - main_03_output.conf
```

然后我们将修改`templates/pipelines.yaml`文件，从该目录加载所有的`*.conf`文件:

<chart name="">/templates/pipeline . YAML</chart>

以上将加载`pipeline`文件夹中的所有文件，并将文件内容输出到配置映射中。我们还使用`tpl`函数处理文件内容，因此我们可以使用变量评估，例如，在`pipeline/main_01_input.conf`中，我们可以使用变量配置 beats 端口:

实际值将在`values.yaml`中定义如下:

这种方法允许我们将复杂的管道存储在多个文件中，将配置图的组成留给掌舵人。

# 结论

现有的 Helm 图表和 Helm 的内置功能相结合，可以极大地帮助我们将 Logstash 部署到 Kubernetes 中，而不需要任何手动部署步骤。Helm `.Files`对象可以进一步简化配置，尤其是对于复杂的 Logstash 管道。

# 资源

*   [航海图](https://github.com/elastic/helm-charts/tree/main/logstash)
*   [日志存储配置](https://www.elastic.co/guide/en/logstash/current/logstash-settings-file.html)
*   [Logstash 管道的结构](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html)