# 理解库伯内特 YAML 语法

> 原文：<https://betterprogramming.pub/understanding-kubernetes-yaml-syntax-83359d33f9c2>

## JSON 能做的一切，还有更多

![](img/bba719dfcb1a97e0629ea22327a1e1fe.png)

照片由[白兰度在](https://unsplash.com/@brandomakesbranding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) [Unsplash](https://unsplash.com/search/photos/kubernetes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上制作品牌

正如 JSON 的[维基百科页面上所说，YAML(另一种标记语言)是 JSON 的超集，这意味着它拥有 JSON 的所有功能，但它也在某种程度上扩展了这种功能。](https://en.wikipedia.org/wiki/JSON#YAML)

YAML 基本上是 JSON 的一个包装器，做 JSON 能做的一切，甚至更多。

为了说明这一点，让我们从 Kubernetes 文档页面获取一个名为 [*理解 Kubernetes 对象*](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/) 的 YAML 文件，并使用[http://convertjson.com](http://convertjson.com/)将其转换为 JSON，这是一个可以将 YAML 转换为 JSON 的在线实用程序。

下面是原始的`deployment.yaml`文件:

```
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

这是转换成 JSON 的文件:

```
{
   "apiVersion": "apps/v1",
   "kind": "Deployment",
   "metadata": {
      "name": "nginx-deployment"
   },
   "spec": {
      "selector": {
         "matchLabels": {
            "app": "nginx"
         }
      },
      "replicas": 2,
      "template": {
         "metadata": {
            "labels": {
               "app": "nginx"
            }
         },
         "spec": {
            "containers": [
               {
                  "name": "nginx",
                  "image": "nginx:1.7.9",
                  "ports": [
                     {
                        "containerPort": 80
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

这里有几件明显的事情需要注意。

1.YAML 副本比 JSON 副本占用更少的空间。

2.YAML 比 JSON 需要更少的字符。

3.YAML 允许评论，而 JSON 不允许。

显而易见，YAML 比 JSON 占用更少的空间，使用更少的字符。即使考虑到 YAML 例子中的注释，YAML 代码也使用了 414 个字符，而 JSON 代码使用了 697 个字符。

在 YAML 使用评论的能力也很不错。注释由`#`字符表示，在这一行中:

```
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
```

`#`之后的一切都是注释。

尽管您可能想知道为什么有人会使用 JSON 而不是 YAML，但是 JSON 确实有一些优势。JavaScript 提供了内置的工具来解析和处理 JSON(毕竟 JSON 代表 JavaScript 对象符号)。

不仅仅是 JavaScript，很多其他语言都内置了 JSON 助手，包括很多 C 族语言。

# YAML 语法基础

让我们深入了解一些 YAML 语法的基础知识。

YAML 文件由键值对的映射(或字典)组成。YAML 地图只是一个包含键和值的对象。

下面是五个键值对的映射:

```
key1: value1
key2: value2
key3: value3
key4: value4
key5: value5
```

JSON 中的对应内容是:

```
{
   "key1": "value1",
   "key2": "value2",
   "key3": "value3",
   "key4": "value4",
   "key5": "value5"
}
```

一个键本身可以包含一个映射:

```
key1:
  subkey1: subvalue1
  subkey2: subvalue2
  subkey3: subvalue3
```

嵌套地图只是一个嵌套对象。JSON 中的对应内容是:

```
{
   "key1": {
      "subkey1": "subvalue1",
      "subkey2": "subvalue2",
      "subkey3": "subvalue3"
   }
}
```

YAML 也有列表:

```
list:
  - item1
  - item2
  - item3
  - item4
  - item5
```

YAML 列表只是一个特定键的值数组。JSON 中的对应内容是:

```
{
   "list": [
      "item1",
      "item2",
      "item3",
      "item4",
      "item5"
   ]
}
```

列表也可以包含地图:

```
list:
  - item1
  - 
    mapItem1: value
    mapItem2: value
  - item3
  - item4
  - item5
```

包含地图的列表项只是数组中的一个对象。JSON 中的对应内容是:

```
[
   "item1",
   {
      "mapItem1": "value",
      "mapItem2": "value"
   },
   "item3",
   "item4",
   "item5"
]
```

# 再看看我们的 YAML 档案

给定上面的例子，让我们再看一下我们的`deployment.yaml`文件，看看我们是否能读得更好一点。

拿这部分来说:

YAML:

```
spec:
  selector:
    matchLabels:
      app: nginx
```

JSON:

```
"spec": {
      "selector": {
         "matchLabels": {
            "app": "nginx"
         }
      }
```

缩进仍然存在，但是引号和花括号被抽象掉了。

让我们进一步看看 YAML 文件中使用列表的地方:

```
spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

和 JSON 等价物:

```
"spec": {
            "containers": [
               {
                  "name": "nginx",
                  "image": "nginx:1.7.9",
                  "ports": [
                     {
                        "containerPort": 80
                     }
                  ]
               }
            ]
         }
```

正如您在 JSON 等价物中看到的那样，`"containers"`处的值是一个数组，数组中的第一个(也是唯一一个)项是一个对象。

那个对象有三个键:`name`、`image`和`ports`。`ports`的值是一个包含一个项目的数组，该项目是一个对象。

我们现在越来越擅长阅读 YAML 了！

# Kubernetes YAML 文件中的必填字段

每个 Kubernetes YAML 文件中都有几个必填字段:

*   `apiVersion`
*   `kind`
*   `metadata`
*   `spec`

您将看到我们提供的 YAML 文件包含所有这四个顶级密钥。

Kubernetes 文档将`apiVersion`描述为:

> *“您正在使用哪个版本的 Kubernetes API 来创建此对象。”*

有不同的 Kubernetes API 版本。我们使用的是`apps/v1`，这个 API 可以在 [Kubernetes 文档](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.15/#deployment-v1-apps)中找到。

您将会看到 Kubernetes API 非常庞大，详细介绍它的各个方面，甚至关注`apps/v1`，都超出了本文的范围。

尽管如此，API 有不同的种类和版本，您必须熟悉哪个 API 适合您正在创建的 Kubernetes 对象。

下一个必填字段是`kind`。文件对此的定义是:

> *“你想创建什么样的对象。”*

您可以创建大量不同的 Kubernetes 对象。它们列在[这个栈溢出答案](https://stackoverflow.com/a/55161551/7386637)里。

必填字段列表中的下一项是`metadata`。这些文档将元数据定义为:

> *“帮助唯一标识对象的数据，包括一个* `*name*` *字符串、* `*UID*` *、可选的* `*namespace*` *”*

这相当容易理解。在我们的`deployment.yaml`文件中，我们给对象一个`nginx-deployment`的`name`元数据值。

然后，其他对象可以使用该值来引用该对象，并且它还为我们提供了关于该 Kubernetes 对象用途的更多上下文。

最后一个必填字段是`spec`，但肯定不是最不重要的。医生检查了这个:

> *"你还需要提供对象* `*spec*` *字段。对象* `*spec*` *的精确格式对于每个 Kubernetes 对象都是不同的，并且包含特定于该对象的嵌套字段。*[*Kubernetes API reference*](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.15/)*可以帮助您找到使用 Kubernetes 创建的所有对象的规范格式。”*

`spec`字段是您更详细地描述对象的地方，您需要使用 Kubernetes API 来完成。

对于我们特定的 YAML 文件，我们将引用[用于部署 v1 应用的 API](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.15/#deployment-v1-apps)，因为这是我们在`apiVersion`字段中指定的 API 版本。

# 结论

希望这能让你更好地理解 Kubernetes YAML 文件是如何工作的。

与 JSON 相比，YAML 看起来非常好，但是它如何抽象掉 JSON 的一些特征可能很难理解。YAML 也不像 JSON 那样常用。

我计划更深入地研究 Kubernetes API，以及如何为这些对象正确配置`spec`字段。敬请期待！