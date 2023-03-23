# 用密封的秘密加密 Kubernetes 的秘密

> 原文：<https://betterprogramming.pub/encrypting-kubernetes-secrets-with-sealed-secrets-fe363149a211>

## 如何在 git 中存储您的 Kubernetes 秘密

![](img/e1f4e6e24cf55452109832e24c4c6cbe.png)

照片由 Kristina Flour 在 Unsplash 上拍摄

GitOps 是一种通过 Git 管理所有配置的方式。它允许团队通过声明性代码来管理环境配置和基础设施。

虽然 Kubernetes 允许团队使用资源清单管理他们的容器工作负载，但是在 Git 存储库中存储 Kubernetes `Secrets`一直是一个挑战。

Kubernetes `Secrets`是帮助您存储敏感信息的资源，比如密码、密钥、证书、OAuth 令牌和 SSH 密钥。

它分离了秘密管理和利用。虽然管理员可以创建密码，但是开发人员可以简单地引用清单中的`Secret`资源，而不是在 pod 定义中硬编码密码。

这听起来不错，但是 Kubernetes secrets 的问题是它们将敏感信息存储为 base64 字符串。任何人都可以解码 base64 字符串，从`Secret`清单中获取原始令牌。

因此，`Secret`清单文件不能存储在源代码库中。在集群上创建秘密总是需要手动操作，这使得 100%的 GitOps 变得困难。

Bitnami Labs 试图通过创建一个名为 [Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets) 的开源工具来解决这个问题。

# 密封的秘密

《密封的秘密》由两部分组成:

*   集群端 Kubernetes 控制器/操作员
*   名为`kubeseal`的客户端实用程序

`kubeseal`实用程序允许您使用非对称加密算法来密封 Kubernetes `Secrets`。`SealedSecrets`是 Kubernetes 资源，包含只有控制器才能解密的加密`Secrets`。因此，`SealedSecret`即使存储在公共存储库中也是安全的。

`SealedSecret`资源只是创建 Kubernetes `Secret`资源的一种方法。当您在 Kubernetes 集群上应用它时，集群端操作符读取`SealedSecret`，生成 Kubernetes `Secret`，并将生成的`Secret`应用到集群上。Kubernetes `Pod`然后可以按照惯例使用`Secret`。

下面是一个`SealedSecret`的例子:

当控制器打开`SealedSecret`时，它生成一个 Kubernetes `Secret`，如下所示:

施加`SealedSecret`几秒钟后，控制器产生 Kubernetes `Secret`。

# 密封保密范围

从最终用户的角度来看，`SealedSecret`是一个只写设备。

除了运行的控制器没有人能解密`SealedSecret`，甚至连`Secret`的作者也不能。

一般来说，最佳实践是禁止用户直接读取机密。您可以创建 RBAC 规则来禁止低权限用户阅读`Secrets`。您还可以限制用户只能从他们的名称空间读取`Secrets`。

虽然`SealedSecrets`的设计方式是不可能直接读取它们，但用户可以绕过这个过程，获得他们不被允许查看的秘密。

`SealedSecret`资源提供了多种方式来防止这种误用。默认情况下，它们是名称空间感知的。一旦使用`kubeseal`为特定的名称空间生成了`SealedSecret`，就不能在另一个名称空间中使用`SealedSecret`。

例如，如果您为名称空间`web`创建一个名为`foo`的`Secret`并带有一个值`bar`，您就不能在`database`名称空间上应用`Secret`——即使它需要相同的`Secret`。

这是故意的，因为我们不允许访问`database`名称空间的用户通过在`database`名称空间上应用`web`名称空间的`SealedSecrets`来从`web`名称空间查看`Secrets`。表现得好像每个名称空间都有自己的解密密钥。

虽然 Sealed Secret 的控制器没有为每个名称空间使用独立的私钥，但它在加密过程中考虑了名称空间和名称，这达到了相同的结果。

另一个场景是，我们可能有一个用户在`web`名称空间上，他只能查看某些秘密，而不是全部。也允许这样。

当您为名称空间`web`中名为`foo`的`Secret`生成一个`SealedSecret`时，对名称空间`web`中名为`bar`的`Secret`具有读取权限的用户不能将`SealedSecret`清单中的`Secret`的名称更改为`bar`并应用它来查看`Secret`。

虽然这些方法有助于你防止人们滥用`Secrets`，但它们可能会让你在管理上头疼。在默认配置中，您将无法定义在多个名称空间中使用的泛型`Secrets`。

您可能没有一个大型团队，并且您的 Kubernetes 集群可能只由管理员访问和管理。因此，您可能不需要那种级别的基于角色的访问控制。

您可能还想定义可以跨名称空间移动的`SealedSecrets`。您不希望为同一个`Secret`管理多个`SealedSecrets`副本。

`SealedSecrets`使用示波器允许这些可能性。

您可以使用三个范围创建您的`SealedSecrets`:

*   `strict`(默认):在这种情况下，考虑到名称和命名空间，你需要密封你的`Secret`。一旦创建了`SealedSecret`，就不能更改它的名称和命名空间。如果你试图这样做，你会得到一个解密错误。
*   `namespace-wide`:这个作用域允许您在已经密封了`Secret`的名称空间内自由地重命名`SealedSecret`。
*   `cluster-wide`:这个作用域允许你自由地将`Secret`移动到任何名称空间，并给它起任何你想要的名字。

除了名称和命名空间之外，您还可以重命名密钥，而不会失去任何解密功能。

使用`kubeseal`时，可以选择带有`--scope`标志的范围:

```
$ kubeseal --scope cluster-wide --format yaml <secret.yaml >sealed-secret.yaml
```

在将配置传递给`kubeseal`之前，您还可以在`Secret`中使用注释来应用作用域:

*   `sealedsecrets.bitnami.com/namespace-wide: "true"`为`namespace-wide`
*   `sealedsecrets.bitnami.com/cluster-wide: "true"`为`cluster-wide`

如果您没有指定任何注释，那么`kubeseal`将采用`strict`范围。如果您设置了两种注释，`cluster-wide`优先。

# 安装“共享机密”

如前一节所述，`SharedSecrets`包含一个客户端`kubeseal`二进制文件和一个集群端 Kubernetes 操作符。

让我们从安装`kubeseal`二进制文件开始。

从[这里](https://github.com/bitnami-labs/sealed-secrets/releases)选择最新的发布版本，下载最新的二进制文件，然后运行以下程序:

现在安装集群端控制器资源。

# 创建一个“密封的秘密”

要创建一个`SealedSecret`，我们首先需要创建一个`Secret`文件。

```
echo -n "This is a secret!" | kubectl create secret generic mysecret -n web --dry-run --from-file=secret=/dev/stdin -o yaml > secret.yaml
```

上面的命令产生了下面的`secret.yaml`文件:

`Secret`只是`This is a secret!`的 base64 编码表示，因此，不适合存储在您的源代码回购中。

时间到了`kubeseal`该`Secret`了。

现在`Secret`被加密了。您可以将它安全地存储在 Git 存储库中。

# 应用“密封秘笈”

现在让我们在`busybox` pod 中使用`Secret`,看看我们能否在集群中检索它。

让我们从创建一个`web`名称空间开始。

```
$ kubectl create ns web
namespace/web created
```

使用`SealedSecret`。

```
$ kubectl apply -f sealedsecret.yaml
sealedsecret.bitnami.com/mysecret created
```

这应该会产生一个名为`mysecret`的对等`Secret`。

```
$ kubectl get secret mysecret -n web
NAME       TYPE     DATA   AGE
mysecret   Opaque   1      119s
```

使用下面使用`Secret`的`busybox` pod YAML。

让我们打印出`Secret`值。我们得到`This is a secret!`作为输出。

```
$ kubectl exec -it busybox -n web -- cat /tmp/mysecret/secret
This is a secret!
```

# 修改名称空间

由于我们在上面的`Secret`中没有指定任何范围，这个`Secret`不应该在`web`名称空间之外的任何地方工作。让我们创建一个`database`名称空间，在那里应用`Secret`，然后自己看看。

从`database`名称空间获取`Secret`。

```
$ kubectl get secret -n database
NAME                  TYPE                                  DATA   AGE
default-token-cjj68   kubernetes.io/service-account-token   3      76s
```

我们看不到`Secret`——为什么？让我们在`database`名称空间中列出`SealedSecret`。

```
$ kubectl get sealedsecret -n database
NAME       AGE
mysecret   109s
```

`SealedSecret`存在，但无法产生`Secret`。让我们看看控制器日志。

我们发现有一个错误。这表明默认情况下无法更改`SealedSecret`的名称空间。

# 修改机密名称

现在让我们尝试修改`Secret`名称，并将其应用到`web`名称空间。

```
$ cp -a sealedsecret.yaml sealedsecret-anothersecret.yaml
$ sed -i 's/mysecret/anothersecret/g' sealedsecret-anothersecret.yaml
$ kubectl apply -f sealedsecret-anothersecret.yaml
sealedsecret.bitnami.com/anothersecret created
```

在`web`名称空间上列出`Secrets`。

```
$ kubectl get secret -n web
NAME                  TYPE                                  DATA   AGE
default-token-6ngbx   kubernetes.io/service-account-token   3      12m
mysecret              Opaque                                1      11m
```

而我们没有看到`anothersecret`。

让我们看看控制器日志，看看发生了什么。

我们也看到了这个错误。

我们预料到了这一点，因为我们已经在`strict`模式中创建了`Secret`。现在让我们试着在`cluster-wide`范围内创建`Secret`，看看我们会得到什么。

# 正在创建群集范围的“密封机密”

让我们创建一个`Secret`而不指定名称空间。

```
echo -n "This is a secret!" | kubectl create secret generic mycwsecret --dry-run --from-file=secret=/dev/stdin -o yaml > secret-cw.yaml
```

使用`cluster-wide`作用域密封`Secret`，并将其应用于`web`名称空间。

```
$ kubeseal --format yaml --scope cluster-wide <secret-cw.yaml >sealedsecret-cw.yaml
$ kubectl apply -n web -f sealedsecret-cw.yaml
sealedsecret.bitnami.com/mycwsecret created
```

让我们看看`Secret`是否被创建。

```
$ kubectl get secret mycwsecret -n web
NAME         TYPE     DATA   AGE
mycwsecret   Opaque   1      19s
```

现在让我们重命名`Secret`，并将其应用到`web`名称空间。

```
$ cp -a sealedsecret-cw.yaml sealedsecret-anothercwsecret.yaml
$ sed -i 's/mycwsecret/anothercwsecret/g' sealedsecret-anothercwsecret.yaml
$ kubectl apply -n web -f sealedsecret-anothercwsecret.yaml
sealedsecret.bitnami.com/anothercwsecret created
```

当我们得到`Secret`时，我们看到它是可用的。

```
$ kubectl get secret anothercwsecret -n web
NAME              TYPE     DATA   AGE
anothercwsecret   Opaque   1      14s
```

修改名称空间怎么样？让我们将密封的`Secret`应用到`database`名称空间上。

```
$ kubectl apply -n database -f sealedsecret-cw.yaml
sealedsecret.bitnami.com/mycwsecret created
```

当我们得到`Secret`时，我们看到它也存在。

```
$ kubectl get secret mycwsecret -n database
NAME         TYPE     DATA   AGE
mycwsecret   Opaque   1      25s
```

# 结论

感谢阅读！我希望你喜欢这篇文章。

在 Git 中存储和管理 Kubernetes 的秘密总是一个挑战，而共享秘密可以帮助您解决这个问题。