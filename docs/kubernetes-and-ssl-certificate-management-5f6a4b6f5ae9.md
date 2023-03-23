# Kubernetes 和 SSL 证书管理

> 原文：<https://betterprogramming.pub/kubernetes-and-ssl-certificate-management-5f6a4b6f5ae9>

## 用 Helm 管理 K8s 中的 SSL 证书命令，让我们加密

![](img/2589a0d357be190168f86c1de01e04d2.png)

由 [Toa Heftiba](https://unsplash.com/@heftiba?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

安全性变得比以往任何时候都更加重要，需要与任何技术解决方案集成，即使是测试环境也是如此。最流行的技术之一是使用带有 SSL 证书的 HTTPS 协议，而不是使用普通的 HTTP 协议。这项技术为我们提供了两个主要好处:

*   客户端和服务器之间的通信是加密的。
*   服务或网站的身份可以由客户端使用可信认证机构来验证。

订购和使用有效的 SSL 证书既费钱又费时间(一些证书颁发机构需要三天时间来颁发所需的证书)。因此，需要自动创建和发布 SSL 证书。各大云平台大多能在数小时内生成 SSL 证书。但是，生成的 SSL 证书不是免费的。

幸运的是，有一个叫做[让我们加密](https://letsencrypt.org/about/)的工具可以让用户为他们的网站生成免费的 SSL 证书。

> “Let's Encrypt 是一个免费的、自动化的、开放的认证机构(CA)，为公众的利益而运行。这是由互联网安全研究小组(ISRG)提供的服务— [让我们加密网站](https://letsencrypt.org/about/)

使用 Let's Encrypt，可以自动创建和更新所需的 SSL 证书。自动化过程的唯一要求是能够自动创建 DNS 记录或向 web 服务器创建文件。但是，Let's Encrypt 对每个域每周颁发的证书数量有限制(每周 50 个证书)。

在 Kubernetes 世界中，使用 [cert-manager](https://cert-manager.io/) 可以轻松管理 SSL 证书的创建。

> “cert-manager 是一个本地的 [Kubernetes](https://kubernetes.io/) 证书管理控制器。它可以帮助从各种来源发布证书，如 [Let's Encrypt](https://letsencrypt.org/) 、 [HashiCorp Vault](https://www.vaultproject.io/) 、 [Venafi](https://www.venafi.com/) 、简单签名密钥对或自签名。
> 
> 它将确保证书是有效的和最新的，并尝试在到期前的配置时间续订证书。”— [证书管理器文档](https://cert-manager.io/docs/)

为了能够使用 cert-manager 来管理 Kubernetes 集群的 SSL 证书创建，需要遵循两个主要步骤:

*   如文档中的[所述，将 cert-manager 部署到 Kubernetes 集群(手动或通过官方 helm 图表)。](https://cert-manager.io/docs/installation/kubernetes/)
*   配置代表证书颁发机构的`Issuer`或`ClusterIssuer`资源，使其能够颁发 SSL 证书，如文档中的[所述。](https://cert-manager.io/docs/configuration/acme/)

# 问题是

我想用 Kubernetes 构建一个集成测试集群，以便能够部署我的软件堆栈，并在每个已部署的环境上执行集成测试，然后移除整个环境以释放资源用于其他部署。以下是我对 Kubernetes 集群和部署管道的主要要求:

*   使用 Kubernetes 集群托管集成测试沙箱。
*   每个沙箱或环境将被部署到一个专用的命名空间。
*   每个沙箱都有自己的子域和 SSL 证书(如`*.test1.shihadeh.de`和`*.test2.shihadeh.de`)。
*   成功运行集成测试后，需要删除 Kubernetes 名称空间和资源。

使用 cert-manager 和 Let's Encrypt 来创建满足上述要求的动态环境会达到证书创建限制。因此，整个 Kubernetes 集群不可用，直到限制期结束。达到限制的根本原因是生成的 SSL 证书存储在沙箱的名称空间中，一旦清理任务完成，证书将从集群中删除。因此，使用同一个子域重新部署会导致再次从 Let's Encrypt 请求 SSL 证书。

# 解决方案

由于 Kubernetes 集群用于集成测试，并且我平均每天有 10 个部署，所以 Let's Encrypt 限制不足以对每个变更请求运行集成测试。这就是为什么我开始寻找解决这个问题的方法。我最后有了以下选择。

## **减少每天的部署数量**

为了减少请求 SSL 证书的数量并避免达到加密限制，我们可以限制集成测试部署。然而，这意味着开发周期需要调整，因为在运行集成测试之前，需要允许新的变更与代码库合并。这就是为什么我忽略了这个选项。

## **切换到舞台集群**

将 cert-manager 配置为使用 Let's Encrypt stage 服务器而不是生产服务器将解决此限制问题。但是，生成的证书将是假的。因为这个原因，我也忽略了这个选项。

## **使用多域证书**

为 Kubernetes 集群中所有受支持的子域颁发一个 SSL 证书会将请求的证书数量减少到一个，因为所有环境都将使用相同的 SSL 证书。然而，仅仅这样做无助于解决达到加密极限的问题。

SSL 证书将被多次颁发，因为 cert-manager 存储将它作为 Kubernetes 机密对象存储在环境名称空间中，并且由于清理过程将删除名称空间，机密对象也将被删除。一旦触发下一个部署，证书管理器将请求一个新的证书，因为旧的证书不存在。这意味着我们仍然被限制在每周 50 次([让我们加密限制](https://letsencrypt.org/docs/rate-limits/))部署。

为了克服这个问题，我决定实施以下解决方案:

*   使用多域证书概念。
*   将生成的证书存储在 cert-manager 命名空间中，而不是环境命名空间中，以避免在清理过程中删除证书。
*   更新部署管道，以便在部署服务之前将证书从 cert-manager 命名空间复制到环境命名空间。
*   同步环境中的 SSL 证书，以防在证书管理器命名空间中部署后更新 SSL 证书。

# **实施**

正如我在本文前面提到的，为了使用 cert-manager，必须创建一个`Issuer`或`ClusterIssuer`来发布有效的 SSL 证书。我通过开发一个[掌舵图](https://github.com/wshihadeh/cert-manager-issuer)来创建集群发布者，这个掌舵图将负责创建所需的 Kubernetes 对象。helm chart 的作用是将 AWS API 密钥存储为 Kubernetes secret 对象，并创建一个配置了 AWS 凭证和 route53 配置的`ClusterIssuer`对象(例如 AWS 区域和 AWS DNS 区域)。

在仔细阅读了 cert-manager 文档之后，我发现可以用以下对象更新 helm chart 来实现上述解决方案。

*   `ClusterRole`:我们需要这个集群角色来授予其他对象管理所需资源所需的权限，比如证书和名称空间。以下是可用于创建该对象的 Kubernetes YAML 文件:

*   `ServiceAccount`:将被绑定创建`ClusterRole`的服务账户。

*   `ClusterRoleBinding`:需要这个对象来绑定集群角色和服务帐户。

*   `Certificate`:证书对象用于定义证书管理器订购的 SSL 证书的规范。下面是需要定义的配置项的简要描述

→ `secret name`:这是保存 SSL 证书数据的 Kubernetes secret 的名称。这个秘密名称将用于配置 Nginx 入口对象来加载 SSL 证书。

→ `issuerRef`:用于颁发 SSL 证书的证书管理器颁发者。

→ `commonName`:证书主域。

→ `DNS name`:需要包含在 SSL 证书中的所有域的列表。

我还向`certificate`对象添加了一个注释，以定义使用该证书的名称空间列表。稍后将使用该注释将 SSL 证书数据与所有名称空间同步。

*   `Job`:为了能够将存储在 cert-manager 名称空间中的 SSL 证书复制到特定于环境的名称空间，我向 helm 图表添加了一个`Job`对象，该对象将在每次更新或部署 helm 图表时执行。这个`Job`是一个简单的 bash 脚本，将在每个部署中执行，并执行以下操作:

→检查 Kubernetes 集群上是否存在环境名称空间(`TARGET_NAMESPACE`)。

→等待 SSL 证书生成并准备好使用。

→将 SSL 证书机密从证书管理器名称空间复制到环境名称空间。

*   `CronJob`:一些环境将运行足够长的时间，以使 SSL 证书达到其到期日期(不是针对集成测试环境，而是针对其他测试环境)。因此，一旦证书管理器更新更新了证书，就需要在这些环境中更新 SSL 证书机密。为了实现这个目标，我决定添加一个定期执行的`CronJob`对象，并将 SSL 证书机密从 cert-manager 名称空间同步到所有支持的名称空间。

# 结论

在 Let's Encrypt、cert-manager 和 Kubernetes 等工具的帮助下，自动创建 SSL 证书比以往任何时候都更容易。在本文中，我展示了如何为集成测试集群自动创建 SSL 证书。讨论的解决方案可以在下面的 GitHub [资源库](https://github.com/wshihadeh/cert-manager-issuer)中找到。