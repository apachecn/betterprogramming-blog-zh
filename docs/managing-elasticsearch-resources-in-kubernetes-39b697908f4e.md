# 管理 Kubernetes 中的弹性搜索资源

> 原文：<https://betterprogramming.pub/managing-elasticsearch-resources-in-kubernetes-39b697908f4e>

## 如何在 Kubernetes 中部署 Elasticsearch 和 Kibana

![](img/562b725fad22fa3727fd456bb72a6135.png)

詹姆斯·哈里森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Kubernetes(简而言之，ECK)上的弹性云带来了在 Kubernetes 上部署弹性搜索集群的可能性，只需几行代码。

不幸的是，相关的资源，比如索引、索引模板和生命周期策略都不是由 ECK 操作符处理的，所以到目前为止，我们必须依赖生命周期挂钩或 init 容器。为了解决这个问题，我们将使用一个额外的操作符，以适当的声明方式协调这些资源。

在本文中，我们将展示如何部署一个新的 Elasticsearch 集群，以及如何轻松地为其提供用户、索引和其他常用资源。

# 使用 Eck 部署弹性搜索集群

为了展示如何为 Elasticsearch 提供额外的资源，我们首先需要创建 Elasticsearch。考虑到我们已经启动并运行了一个 Kubernetes 集群，配置了 kubectl 以便它可以连接到集群，并且 [ECK 操作员已经安装了](https://www.elastic.co/guide/en/cloud-on-k8s/master/k8s-deploy-eck.html)这只是一个部署`Elasticsearch`规范的问题:

运行`kubectl apply -f elasticsearch.yaml`将部署一个单节点 Elasticsearch 集群，过一会儿，您的集群应该准备好接受连接了。

要验证集群的健康状况，您可以运行`kubectl get Elasticsearch quickstart`。输出中报告了群集运行状况:

```
$ kubectl get Elasticsearch quickstart
NAME         HEALTH   NODES   VERSION   PHASE   AGE
quickstart   green    1       8.1.0     Ready   5m
```

或者，我们可以通过部署 Kibana 规范来安装 Kibana，例如:

用`kubectl apply -f kibana.yaml`。

# 访问 Elasticsearch 和 Kibana

对本地 Kubernetes 集群中运行的 Elasticsearch 的最简单访问是通过端口转发会话。首先，我们应该得到 Elasticsearch 服务的名称。运行`kubectl get services`会给出一个服务列表:

```
$ kubectl get services
NAME                          TYPE        ...   PORT(S)    AGE
quickstart-es-default         ClusterIP   ...   9200/TCP   5m
quickstart-es-http            ClusterIP   ...   9200/TCP   5m
quickstart-es-internal-http   ClusterIP   ...   9200/TCP   5m
quickstart-es-transport       ClusterIP   ...   9300/TCP   5m
quickstart-kb-http            ClusterIP   ...   5601/TCP   5m
```

`quickstart-es-http`用于与 Elasticsearch 进行 HTTP 通信，我们将在`port-forward`命令中使用它:

```
$ kubectl port-forward svc/quickstart-es-http 9200:9200
Forwarding from 127.0.0.1:9200 -> 9200
Forwarding from [::1]:9200 -> 9200
```

在我们尝试查询`https://localhost:9200`之前，我们应该检索默认`elastic`用户的密码:

```
$ kubectl get secret quickstart-es-elastic-user -o go-template='{{.data.elastic | base64decode}}'yEz6QpRW0w005H3q2B738ugx
```

现在我们准备查询 Elasticsearch:

```
$ curl https://localhost:9200 -k -u elastic:yEz6QpRW0w005H3q2B738ugx
{
  "name" : "quickstart-es-default-0",
  "cluster_name" : "quickstart",
  "version" : {
    "number" : "8.1.0",
    "build_flavor" : "default",
    "build_type" : "docker",
    ...
  },
  "tagline" : "You Know, for Search"
}
```

要访问 kibana，我们必须进行端口转发到`quickstart-kb-http`服务，默认情况下监听`5601`端口:

```
$ kubectl port-forward svc/quickstart-kb-http 5601:5601
Forwarding from 127.0.0.1:5601 -> 5601
Forwarding from [::1]:5601 -> 5601
```

之后，我们可以通过浏览器中的`https://localhost:5601`访问基巴纳。

现在，我们已经在 Kubernetes 集群中启动并运行了 Elasticsearch 和 Kibana，并且我们已经准备好为它提供额外的资源。

# 安装 ECK 自定义资源运算符

安装[ECK-定制-资源](https://github.com/xco-sk/eck-custom-resources) (ECK-CR)操作器可以使用提供的舵图表来完成:

```
$ helm repo add eck-custom-resources https://xco-sk.github.io/eck-custom-resources/
$ helm install eck-cr eck-custom-resources/eck-custom-resources-operator
```

操作符的默认配置很适合上面的例子，所以不需要额外的配置。通过传递适当的值，您可以随时更改 Elasticsearch 和 Kibana 的 URL 和验证细节，请参见默认的`[values.yaml](https://github.com/xco-sk/eck-custom-resources/blob/main/charts/eck-custom-resources-operator/values.yaml)`和图表的[自述文件](https://github.com/xco-sk/eck-custom-resources/blob/main/charts/eck-custom-resources-operator/README.md)。

我们现在可以通过调用`kubectl get pods`来检查 ECK-CR pod 是否准备好了。

# 创建索引模板和索引

ECK-CR 的 helm chart 安装了各种自定义资源定义，表示 Elasticsearch 和 Kibana 对象。GitHub 资源库中提供了受支持资源的列表[及其各自的文档。作为工作流的一个例子，我们将展示`Index`模板和`Index`的创建。](https://github.com/xco-sk/eck-custom-resources/blob/main/docs/cr_list.md)

我们将从简单的`Index`模板开始，在`body`字段中定义索引模板 json:

```
$ kubectl apply -f indextemplate.yamlindextemplate.es.eck.github.com/indextemplate-sample created
```

操作员将在我们的 Elasticsearch 集群中创建(或更新)名为`indextemplate-sample`的索引模板，这可以通过调用 Elasticsearch REST API 来验证:

```
$ curl "https://localhost:9200/_cat/templates/indextemplate*?v" -k -u elastic:yEz6QpRW0w005H3q2B738ugxname                 index_patterns order version composed_of
indextemplate-sample [index-*]      1             []
```

类似地，我们可以部署索引。为了确保在索引模板之后部署索引，`Index`规范定义了可选的`dependencies`来列出所需的索引模板。然后，操作员在应用给定的`Index`之前，等待所有需要的模板出现在集群中:

```
$ kubectl apply -f index.yaml
index.es.eck.github.com/index-sample created
```

ECK-CR 操作员将在我们的弹性搜索集群中创建`Index`。为了验证，我们可以再次查询 Elasticsearch:

操作员还将协调状态报告到对象事件中，因此如果出现问题，我们可以很容易地获得关于根本原因的信息:

```
$ kubectl describe index index-sample-failure

Events:
  Type     Reason                From              Message
  ----     ------                ----              -------
  Warning  Missing dependencies  index_controller  Some of declared dependencies are not present yet: dependencies not fulfilled. Missing indices:[index-base-sample]. Missing index templates:[]. Errors:[]
```

要删除资源，我们将使用`kubectl delete`命令:

```
$ kubectl delete IndexTemplate indextemplate-sampleindextemplate.es.eck.github.com "indextemplate-sample" deleted
```

这将从 Kubernetes 和 Elasticsearch 中删除`Index`模板。同样的情况也适用于`Index`，但是，有一个索引空检查来防止数据丢失。如果`Index` 不为空，运营商不会将其从 Elasticsearch 中删除。

还支持其他 Elasticsearch 资源，请参见关于如何使用其中每个资源的文档:[索引生命周期策略](https://github.com/xco-sk/eck-custom-resources/blob/main/docs/cr_index_lifecycle_policy.md)、[摄取管道](https://github.com/xco-sk/eck-custom-resources/blob/main/docs/cr_ingest_pipeline.md)、[快照存储库](https://github.com/xco-sk/eck-custom-resources/blob/main/docs/cr_snapshot_repo.md)、[快照生命周期策略](https://github.com/xco-sk/eck-custom-resources/blob/main/docs/cr_snapshot_lifecycle_policy.md)、[用户](https://github.com/xco-sk/eck-custom-resources/blob/main/docs/cr_user.md)和[角色](https://github.com/xco-sk/eck-custom-resources/blob/main/docs/cr_role.md)。

# 基巴纳可视化和仪表板

与 Kibana 相关的资源以与 Elasticsearch 相同的方式处理，参见可视化示例:

然后，操作员使用 Kibana REST API 在 Kibana 中创建对象。在上述示例中，可以使用事件来监控协调状态:

```
$ kubect describe visualization visualization-sampleEvents:
  Type    Reason   From                      Message
  ----    ------   ----                      -------
  Normal  Created  visualization_controller  Created/Updated kibana.eck.github.com/v1alpha1/Visualization visualization-sample
```

我们还可以在`dependencies`字段中定义所需的资源——这对于在 visualization JSON 中匹配`references`字段特别有用。有关更多示例及其规范的详细描述，请参见 Kibana 资源的[文档。](https://github.com/xco-sk/eck-custom-resources/blob/main/docs/cr_list.md)

# 结论

ECK-CR 操作符允许我们以声明的方式提供 Elasticsearch 和 Kibana 集群，这为我们的基础设施即代码带来了清晰性和透明度。操作器本身易于安装并与 ECK 最大限度兼容，但它也支持独立的 Elasticsearch 和 Kibana 安装。

在当前状态下，运营商应该覆盖使用最多的 Elasticsearch 和 Kibana 资源，但如果您发现缺少某些重要类型的资源，请随时在 [GitHub repo](https://github.com/xco-sk/eck-custom-resources) 中提出问题，通常，我们会非常感谢任何类型的反馈。

# 资源

*   [Kubernetes 上的弹性云运营商文档](https://www.elastic.co/guide/en/cloud-on-k8s/master/index.html)
*   [Elasticsearch REST API 文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/rest-apis.html)
*   [ECK-CR 运营商 GitHub 知识库](https://github.com/xco-sk/eck-custom-resources)