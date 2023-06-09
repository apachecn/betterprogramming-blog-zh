# 使用 Helm 的 13 个最佳实践

> 原文：<https://betterprogramming.pub/13-best-practices-for-using-helm-f4fc68545a2c>

## #6.确保机密安全

![](img/2d92f8440798167891bc1a41da64ced0.png)

图片来源:作者

Helm 是将应用部署到 Kubernetes 集群不可或缺的工具。但只有遵循最佳实践，你才能真正获得 Helm 的好处。这里有 13 个最佳实践来帮助您使用 Helm 创建、操作和升级应用程序。

# 让你的航海图更上一层楼

Helm 是 Kubernetes 的包装经理。它减少了部署复杂应用程序的工作量，这要归功于它的模板方法和丰富的可重用和生产就绪包生态系统，也称为舵图。使用 Helm，您可以将打包的应用程序部署为版本化的、预配置的 Kubernetes 资源的集合。

让我们假设您正在使用 Kubernetes 部署一个数据库——包括多个部署、容器、秘密、卷和服务。Helm 允许您用一个命令和一组值安装同一个数据库。它的声明性和幂等命令使 Helm 成为连续交付(CD)的理想工具。

Helm 是一个云计算原生计算基金会(CNCF)项目，创建于 2015 年，于 2020 年 4 月毕业。随着[头盔 3](https://v3.helm.sh/docs/faq/#changes-since-helm-2) 的最新版本，它已经变得更加融入 Kubernetes 生态系统。

这篇文章提供了 13 个创建掌舵图的最佳实践，来管理在 Kubernetes 上运行的应用程序。

# 1.利用 Helm 生态系统

Helm 为您提供了丰富的社区专业知识，这可能是该工具最大的优势。它从世界各地的开发者那里收集图表，然后通过图表库共享。您可以将[官方稳定图表库](http://github.com/helm/charts)添加到您的本地设置，如下所示:

```
$ helm repo add stable [https://charts.helm.sh/stable](https://charts.helm.sh/stable)"stable" has been added to your repositoriesThen you can search for charts, for example, MySQL:$ helm search hub mysqlURL CHART VERSION  APP VERSION DESCRIPTION[https://hub.helm.sh/charts/bitnami/mysql](https://hub.helm.sh/charts/bitnami/mysql) 8.2.3 8.0.22 Chart to create a Highly available MySQL cluster[https://hub.helm.sh/charts/t3n/mysql](https://hub.helm.sh/charts/t3n/mysql) 0.1.0 8.0.22 Fast, reliable, scalable, and easy to use open-...
```

你会看到一长串结果，可见舵图生态圈有多大。

# 2.使用子图表来管理您的依赖关系

因为部署到 Kubernetes 的应用程序由细粒度的、相互依赖的部分组成，所以它们的导航图有各种资源模板和依赖关系。例如，让我们假设您的后端依赖于一个数据库和一个消息队列。数据库和消息队列已经是独立的应用程序(例如， [PostgreSQL](https://www.postgresql.org/) 和 [RabbitMQ](https://www.rabbitmq.com/) )。因此，建议为独立应用程序创建或使用单独的图表，并将它们添加到父图表中。从属应用程序在这里被命名为子图表。

创建和配置子图表有三个基本要素:

## 图表结构

文件夹结构应按以下顺序排列:

```
backend-chart
  - Chart.yaml
  - charts
    - message-queue
      - Chart.yaml
      - templates
      - values.yaml
    - database
      - Chart.yaml
      - templates
      - values.yaml
  - values.yaml
```

## Chart.yaml

此外，父图表中的`Chart.yaml`应列出所有依赖关系和条件:

## 价值观. yaml

最后，您可以使用以下`values.yaml`文件设置或覆盖父图表中子图表的值:

创建和使用子图表在父应用程序和依赖应用程序之间建立了一个抽象层。这些单独的图表使得在 Kubernetes 中使用它们单独的值和升级生命周期来部署、调试和更新应用程序变得容易。您可以在像 [bitnami/wordpress](https://github.com/bitnami/charts/tree/master/bitnami/wordpress) 这样的示例图表中浏览文件夹结构、依赖项和值文件。

# 3.使用标签轻松查找资源

标签对于 Kubernetes 的内部运营和运营者的日常工作至关重要。Kubernetes 中的几乎每个资源都提供了不同用途的标签，比如分组、资源分配、负载平衡或调度。

单个 Helm 命令将允许您安装多个资源。但是知道这些资源来自哪里是至关重要的。标签使你能够快速找到你的 Helm 版本创建的资源。

最常见的方法是在`helpers.tpl`中定义标签，就像这样:

然后，您需要对资源模板中的标签使用`include`函数:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-queue
  labels:
{{ include "labels" . | indent 4 }}
...
```

现在，您应该能够用标签选择器列出所有资源了。例如，您可以使用`kubectl get pods -l app.kubernetes.io/instance=[Name of the Helm Release]`命令列出 my-queue 部署的所有 pod。这一步对于定位和调试 Helm 管理的那些资源是必不可少的。

# 4.记录你的图表

文件对于确保舵图的可维护性至关重要。在资源模板和`README`中添加注释有助于团队开发和使用掌舵图。

您应该使用以下三个选项来记录您的图表:

*   注释:模板和值文件是 YAML 文件。您可以添加[注释](https://yaml.org/spec/1.2/spec.html#comment//)并提供关于 YAML 文件中字段的有用信息。
*   `README`:图表的自述文件是一个说明如何使用图表的减价文件。您可以使用以下命令检查自述文件的内容:`helm show readme [Name of the Chart]`。
*   `NOTES.txt`:这是一个位于`templates/NOTES.txt`的特殊文件，提供关于发布部署的有用信息。`NOTES.txt`文件的内容也可以用函数和值来模板化，类似于资源模板:

```
You have deployed the following release: {{ .Release.Name }}.
To get further information, you can run the commands:
$ helm status {{ .Release.Name }}
$ helm get all {{ .Release.Name }}
```

在舵安装或舵升级命令结束时，舵打印出`NOTES.txt`的内容，如下所示:

# 5.测试你的图表

掌舵图由将部署到集群的多个资源组成。检查集群中的所有资源是否都是用正确的值创建的，这一点很重要。例如，在部署数据库时，您应该检查数据库密码设置是否正确。

幸运的是，Helm 提供了一个测试功能，可以在集群中运行一些容器来验证应用程序。例如，用`"helm.sh/hook": test-success`标注的资源模板由 Helm 作为测试用例运行。

让我们假设你正在用 [MariaDB](https://mariadb.org/) 数据库部署 WordPress。Bitnami 维护的 Helm 图表有一个 [pod 来验证数据库连接](https://github.com/bitnami/charts/tree/master/bitnami/wordpress)，定义如下:

```
...
apiVersion: v1
kind: Pod
metadata:
  name: "{{ .Release.Name }}-credentials-test"
  annotations:
    "helm.sh/hook": test-success
...
      env:
        - name: MARIADB\_HOST
          value: {{ include "wordpress.databaseHost" . | quote }}
        - name: MARIADB\_PORT
          value: "3306"
        - name: WORDPRESS\_DATABASE\_NAME
          value: {{ default "" .Values.mariadb.auth.database | quote }}
        - name: WORDPRESS\_DATABASE\_USER
          value: {{ default "" .Values.mariadb.auth.username | quote }}
        - name: WORDPRESS\_DATABASE\_PASSWORD
          valueFrom:
            secretKeyRef:
              name: {{ include "wordpress.databaseSecretName" . }}
              key: mariadb-password
      command:
        - /bin/bash
        - -ec
        - |
          mysql --host=$MARIADB\_HOST --port=$MARIADB\_PORT --user=$WORDPRESS\_DATABASE\_USER --password=$WORDPRESS\_DATABASE\_PASSWORD
  restartPolicy: Never
{{- end }}
...
```

建议为您的图表编写测试，并在安装后运行它们。例如，您可以使用 helm test `<RELEASE_NAME>`命令来运行测试。这些测试是验证和发现安装了 Helm 的应用程序中的问题的宝贵资产。

# 6.确保机密安全

敏感数据，如密钥或密码，作为秘密存储在 Kubernetes 中。尽管在 Kubernetes 端保护秘密是可能的，但它们大多作为 Helm 模板和值的一部分存储为文本文件。

头盔秘密插件为你的重要信息提供秘密管理和保护。它将秘密加密委托给支持 AWS KMS、GCP 云 KMS、Azure Key Vault 和 PGP 的 Mozilla SOPS。

让我们假设您已经在一个名为`secrets.yaml`的文件中收集了敏感数据，如下所示:

```
postgresql:
  postgresqlUsername: postgres
  postgresqlPassword: WoZpCAlBsg
  postgresqlDatabase: wp
```

您可以使用插件加密文件:

```
$ helm secrets enc secrets.yaml
Encrypting secrets.yaml
Encrypted secrets.yaml
```

现在文件将被更新，所有值都将被加密:

```
postgresql:
    postgresqlUsername: ENC\[AES256\_GCM,data:D14/CcA3WjY=,iv...==,type:str\]
    postgresqlPassword: ENC\[AES256\_GCM,data:Wd7VEKSoqV...,type:str\]
    postgresqlDatabase: ENC\[AES256\_GCM,data:8ur9pqDxUA==,iv:R...,type:str\]
sops:
  ...
```

上面的`secrets.yaml`中的数据是不安全的，舵秘解决了将敏感数据作为舵图的一部分存储的问题。

# 7.使用模板函数使您的图表可重复使用

Helm 支持 60 多个可以在模板中使用的函数。这些函数在 [Go 模板语言](https://godoc.org/text/template)和 [Sprig 模板库](https://masterminds.github.io/sprig/)中定义。模板文件中的函数大大简化了舵操作。

让我们以下面的模板文件为例:

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-configmap
data:
  environment: {{ .Values.environment | default "dev" | quote }}
  region: {{ .Values.region | upper | quote }}
```

当没有提供环境值时，它将由模板函数默认。当您检查 region 字段时，您会看到模板中没有定义默认值。然而，该字段有另一个函数，称为`upper`，将提供的值转换成大写。

另一个重要且有用的功能是`required`。它使您能够设置模板呈现所需的值。例如，让我们假设您需要一个具有以下模板的配置图名称:

```
...
metadata:
  name: {{ required "Name is required" .Values.configName }}
...
```

如果条目为空，模板呈现将失败，并显示错误“需要名称”创建舵图时，模板函数非常有用。它们可以改进模板，减少代码重复，并用于在将应用程序部署到 Kubernetes 之前验证值。

# 8.当配置映射或密码更改时更新您的部署

将配置映射或机密装载到容器中是很常见的。尽管部署和容器映像会随着新版本的发布而改变，但是配置映射或机密不会经常改变。以下注释使得在配置图更改时部署新部署成为可能:

```
kind: Deployment
spec:
  template:
    metadata:
      annotations:
        checksum/config: {{ include (print $.Template.BasePath "/configmap.yaml") . | sha256sum }}
...
```

配置图中的任何更改都将计算新的`sha256sum`，并创建部署的新版本。这确保部署中的容器将使用新的配置图重新启动。

# 9.通过资源策略退出资源删除

在典型设置中，安装 Helm 图表后，Helm 将在集群中创建多个资源。然后，您可以通过更改值以及添加或删除资源来升级它。一旦不再需要该应用程序，可以将其删除，这将从集群中删除所有资源。但是，即使在运行 Helm uninstall 之后，一些资源也应该保留在集群中。让我们假设您已经使用 PersistentVolumeClaim 部署了一个数据库，并且希望存储卷，即使您正在删除数据库版本。对于这样的资源，您需要使用如下的`resource-policy`注释:

```
kind: Secret
metadata:
  annotations:
    "helm.sh/resource-policy": keep
...
```

舵命令如`uninstall`、`upgrade`或`rollback`将导致上述秘密的删除。但是通过使用所示的资源策略，Helm 将跳过秘密的删除，并允许它成为孤儿。因此，应该非常小心地使用注释，并且仅用于删除 Helm 版本后所需的资源。

# 10.使用这些有用的命令来调试舵图

Helm 模板文件提供了许多不同的功能和多种用于创建 Kubernetes 资源的价值来源。用户的一项基本职责是了解集群中部署了什么。因此，您需要学习如何调试模板和验证图表。有四个用于调试的基本命令:

*   `helm lint`:linter 工具会进行一系列测试，以确保您的图表格式正确。
*   `helm install — dry-run — debug`:这个函数呈现模板并显示结果资源清单。您还可以在部署之前检查所有资源，并确保设置了值，模板函数按预期工作。
*   `helm get manifest`:该命令检索安装到集群的资源清单。如果该版本没有按预期运行，这应该是您用来找出集群中正在运行什么的第一个命令。
*   `helm get values`:该命令用于检索安装到集群的发布值。如果您对计算值或默认值有任何疑问，这绝对应该放在您的工具箱中。

# 11.使用查找功能避免秘密再生

Helm 函数用于生成随机数据，如密码、密钥和证书。随机生成会创建新的任意值，并在每次部署和升级时更新群集中的资源。例如，它可以在每次版本升级时替换集群中的数据库密码。这将导致客户端在密码更改后无法连接到数据库。

为了解决这个问题，建议随机生成值，并覆盖集群中已经存在的值。例如:

```
{{- $rootPasswordValue := (randAlpha 16) | b64enc | quote }}
{{- $secret := (lookup "v1" "Secret" .Release.Namespace "db-keys") }}
{{- if $secret }}
{{- $rootPasswordValue = index $secret.data "root-password" }}
{{- end -}}
apiVersion: v1
kind: Secret
metadata:
  name: db-keys
  namespace: {{ .Release.Namespace }}
type: Opaque
data:
  root-password: {{ $rootPasswordValue}}
```

上面的模板首先创建一个 16 字符的`randAlpha`值，然后检查集群中的秘密及其对应的字段。如果找到，它会覆盖并重新使用`rootPasswordValue`作为根密码。

# 12.迁移到 Helm 3，获得更简单、更安全的 Kubernetes 应用程序

最新发布的头盔，头盔 3，提供了许多新功能，使其成为一个更轻，更精简的工具。Helm v3 因其增强的安全性和简单性而受到推荐。这包括:

*   **Tiller 的移除** : Tiller 是 Helm 服务器端组件，但由于在早期版本中对集群进行更改需要详尽的权限，它已经从 v3 中移除。这也造成了安全风险，因为任何获得 Tiller 访问权限的人都将对您的集群拥有过多的权限。
*   **改进的海图升级策略** : Helm v2 依靠双向战略合并补丁。它将新版本与配置图存储中的版本进行比较，并应用更改。相反，Helm v3 会比较旧清单、集群中的状态和新版本。所以你的手动修改不会在升级头盔时丢失。这简化了升级过程并增强了应用程序的可靠性。

有一个 helm-2to3 插件，您可以使用以下命令安装它:

```
$ helm3 plugin install [https://github.com/helm/helm-2to3](https://github.com/helm/helm-2to3)
```

这是一个小而有用的插件，带有清理、转换和移动命令，可以帮助您迁移和清理 v2 配置，并为 v3 创建版本。

# 13.保持您的持续交付渠道幂等

Kubernetes 资源是声明性的，因为它们的规范和状态存储在集群中。类似地，Helm 需要创建声明性模板和发布。因此，在使用 Helm 时，您需要将您的持续交付和发布管理设计为*幂等的*。幂等运算是一种可以应用多次而不会改变第一次运行后的结果的运算。

有两条基本规则需要遵循:

*   始终使用`helm upgrade --install`命令。如果尚未安装图表，它会安装图表。如果它们已经安装，它会升级它们。
*   在舵升级过程中，如果操作失败，使用`--atomic`标志回滚更改。这确保了舵释放不会卡在失效状态。

# 摘要

Helm 是将应用程序部署到 Kubernetes 集群不可或缺的工具。但只有遵循最佳实践，你才能真正获得 Helm 的好处。

本文涵盖的最佳实践将帮助您的团队创建、操作和升级生产级分布式应用程序。从开发的角度来看，你的舵图将更容易维护和保护。从操作方面来说，您将享受自动更新的部署，避免删除资源，并学习如何测试和调试。

赫尔姆官方[主题指南](https://helm.sh/docs/topics/)是检查赫尔姆[命令](https://helm.sh/docs/helm/helm/)和理解他们设计理念的另一个好资源。有了这些资源，以及本博客中概述的最佳实践和示例，您一定会准备好创建和管理在 Kubernetes 上运行的生产级 Helm 应用程序。

【https://codersociety.com/blog/articles/helm-best-practices】原载于[](https://codersociety.com/blog/articles/helm-best-practices)