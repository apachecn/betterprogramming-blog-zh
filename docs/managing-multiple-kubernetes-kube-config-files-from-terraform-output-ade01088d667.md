# 管理多个 Kubernetes ~/。来自 Terraform 输出的 kube/config 文件

> 原文：<https://betterprogramming.pub/managing-multiple-kubernetes-kube-config-files-from-terraform-output-ade01088d667>

## 自动化群集的配置

![](img/c9838a69a26eb7ab09b0bc2af28f2edd.png)

[shutterstock](https://shutterstock.com) ，在标准许可下使用

在使用 Kubernetes 时，人们经常会发现自己在开发部署在多个集群下的应用程序。

有时，这些集群将分布在不同的云提供商之间，可能很难在许多环境之间切换。

尽管事实上有像`[kubectx](https://github.com/ahmetb/kubectx)`这样伟大的开源工具，可以无缝地在一个人的上下文和名称空间中跳转，但实际的过程作为一个整体可能会变得棘手，特别是当使用 Terraform 来重复提供、创建和销毁资源时。

# 问题

在得到`terraform apply`的输出后，`kubeconfig`敏感文件往往以 base64 编码字符串的形式存在。它可以从云提供商的控制台下载，但这会产生多余的开销，毕竟这违背了 Terraform 的原则。

然而，让我们假设下载了，并且存在于用户机器的某个地方。

处理多个配置的常见方法是设置一个指向要使用的配置路径的环境值`KUBECONFIG`，但是我不喜欢这种方法。

在每个新的 shell 会话中设置它是令人厌烦的，而在 shell 配置文件中设置它使得一次只能访问一个上下文。

为了真正实现集群配置和部署的自动化，我想到了这个简单的解决方案。

# 解决方案(使用 Linode provider 的示例)

# 说明

在输出中包含`kubeconfig`值后:

```
output "kubeconfig" {
   value = linode_lke_cluster.[cluster-name].kubeconfig
   sensitive = true
}
```

和应用，`kubeconfig.yaml`然后可以被解析并通过管道传输到一个文件:

```
terraform output kubeconfig | jq -r "@base64d" > ~/.kube/$PROVIDER.yaml
```

以后用作别名中的变量:

```
alias $PROVIDER"ctl"="KUBECONFIG=~/.kube/$PROVIDER.yaml"
```

它也可以安全地附加到`.zshrc`或`.bashrc`上，以防 Terraform 的配置发生变化，只需覆盖即可。

# 限制

为了使用带有`[skaffold](https://github.com/GoogleContainerTools/skaffold)`或`[helm](https://helm.sh/)`的上下文，`env`变量也需要作为相应命令的一部分。