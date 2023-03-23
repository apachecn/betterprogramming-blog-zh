# 你应该知道的 5 个基本地形命令

> 原文：<https://betterprogramming.pub/5-basic-terraform-commands-you-should-know-d613f539fb9b>

## 比 Mars 更快地开拓您的云提供商

![](img/bcbadb0589cdf946cf0bd19d21d772ef.png)

照片由 [Jainath Ponnala](https://unsplash.com/@jainath?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/servers?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

当我第一次被介绍到 Terraform 时，一个全新的世界展现在我面前。云资源现在可以像代码一样管理。我被 IaC 病毒咬了一口，正在喝 Kool-Aid。最后，对实例或网络的小的增量更改可以被记录并在提交中部署。

构建庞大的服务架构可以在`terraform apply`的瞬间完成。

几年后，Terraform 仍然在提供一种简单有效的方法来提供代码资源。我几乎每天都在使用它，但是已经遇到了一些对日常地形改造有帮助的场景和命令。

如果您正在部署大量复杂的服务，或者状态开始分离，那么使用 Terraform 可能会令人望而生畏。在本文中，我们将研究一些常见 Terraform 问题的简便解决方案，以及如何在日常使用中充分利用 Terraform。

# 1.验证您的配置

这可能看起来简单得令人麻木，但是我已经遇到过一百万次无效的配置了。在开始运行更昂贵的命令如`plan`或`apply`之前，执行简单的配置验证可以节省您一点时间。

您所要做的就是从您的 Terraform 根目录执行以下命令:

```
terraform validate
```

您还可以将其构建到现有的 CI/CD 管道中，以便在执行配置之前对其进行验证。`validate`命令也将接受`-json`标志来产生机器可读的输出。这对 Jenkins 或任何其他自动化平台的使用都很方便。

# 2.针对特定资源

如果你的 Terraform 存储库已经开始变得很大，那么在不同的阶段进行`plan`或`apply`会很困难。有时，您可能希望只应用特定的资源，或者您可能仍在处理某个特定的模块，并且还不想对其进行部署评估。

在这种情况下，执行有针对性的部署可能是有意义的。这将限制 Terraform 在评估新计划时查看的资源。假设您有两个不同的实例:

```
aws_instance.instance_01
aws_instance.instance_02
```

默认情况下，Terraform 将评估所有资源。如果您只想针对特定资源或一组资源运行部署，那么您可以通过资源的路径来定位资源。

执行以下命令，针对第二个实例运行目标`plan`或`apply`:

```
terraform plan -target=aws_instance.instance_02
```

这将仅评估对`aws_instance.instance_02`的更改，并忽略所有其他更改。如果您想向 target 添加额外的资源，您可以简单地向`plan`传递更多的`-target`标志。

使用`-target`标志被认为是“例外使用”，这意味着不建议在日常应用中使用。我建议只有当你在紧要关头或者设法让 Terraform 进入一个特别糟糕的状态时才使用这个。

你可以在这里阅读更多关于资源定位[的内容。](https://learn.hashicorp.com/tutorials/terraform/resource-targeting)

# 3.导入现有资源

基础设施不完善。总有不可告人的秘密。有时这些骨骼是在 Terraform 存在或实现之前的许多年前建造的。如果您有一些在云提供商的控制台上“手工”创建的遗留资源，不用担心，因为您可以轻松地导入它们。

让我们假设您有一个旧的 AWS EC2 实例正在运行，但它不是由 Terraform 部署的。为了管理这个实例，您必须将其导入状态:

```
terraform import aws_instance.name i-1234567890
```

将实例导入 state 后，您还需要在 TF 文件中为它添加一个定义。如果不这样做，Terraform 可能会认为您希望删除实例。通常，最快的方法是在不添加任何新定义的情况下运行一个`terraform plan`，并检查 Terraform 认为应该删除的定义。然后，您可以使用它作为添加内容的模板。

一旦定义被添加并且状态匹配，您现在可以对它进行更改，从其他资源引用它等等。

点击阅读更多关于导入现有资源[的信息。](https://www.terraform.io/cli/import)

# 4.移动资源

想出一个描述性的名字并不容易。有时候直到后来你才知道某样东西真正的用途。如果您有一些命名不当的资源，您可能已经尝试在配置中简单地重命名它们。不幸的是，这不起作用。Terraform 会认为你想要一个完全不同的资源，并试图为你编造一个。

为了“重命名”资源(特别是当它被其他资源引用时)，您必须执行“状态移动”:

```
terraform state mv <old_path> <new_path>
```

这个命令的作用是获取一个资源路径，并在状态文件中将其重命名为另一个路径。请记住，路径包括资源类型。因此，为了将名为" *test* "的 AWS 实例移动到" *test2* "中，您应该执行如下内容:

```
terraform state mv aws_instance.test aws_instance.test2
```

虽然不建议对 state 进行大量的手动编辑，但我已经多次使用过，很少遇到重命名资源的问题。只是在对许多其他人依赖的关键资源进行更改时要小心。

点击查看更多关于`state`命令[的信息。](https://www.terraform.io/cli/commands/state)

# 5.重新部署损坏的资源

每个人在他们的 Terraform 冒险中都经历过这种情况。资源部署出错。您的互联网连接中断。您的云提供商不接受该配置。不赞成使用的选项不再有效。有时资源会进入一种糟糕的状态，变成“部署了一半”。它们可能缺少某些配置，或者甚至无法正常工作。

如果您想重新部署这些资源而不删除它们，Terraform 提供了一个内置的处理机制。你可以`taint`一个特定的资源。这意味着，您实际上是在这个资源上设置了一个要销毁和重新创建的标志:

```
terraform taint aws_instance.instance_02
```

运行这个命令将`taint`资源，直到您对它发出`untaint`命令。这将导致下一次部署销毁并重建此实例。

您不必注释掉资源，也不必担心复制和粘贴。只需`taint`按路径分配资源，重新部署并照常运营。

在这里阅读更多关于污染资源的信息[。](https://www.terraform.io/cli/commands/taint)

感谢阅读！如果你喜欢这篇文章，请花点时间看看我下面的其他帖子:

*   [*用 Ansible*](/build-a-useful-linux-login-banner-on-aws-with-ansible-4c000aba1258) 在 AWS 上搭建一个有用的 Linux 登录 Banner
*   [*5 Linux 网络性能与分析工具*](/5-network-performance-analysis-tools-for-linux-f420c35fb8bd)