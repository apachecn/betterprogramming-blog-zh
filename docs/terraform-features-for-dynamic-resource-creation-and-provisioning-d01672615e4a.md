# 用于动态资源创建和供应的地形特征

> 原文：<https://betterprogramming.pub/terraform-features-for-dynamic-resource-creation-and-provisioning-d01672615e4a>

## 当需要更动态的基础设施即代码方法时，您可以做什么

![](img/f483a359e0498679048a8ebc34a00122.png)

[基兰·伍德](https://unsplash.com/@kieran_wood?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 动态资源供应

过去，基础架构的设置和维护通常是手动进行的。一段时间后，管理员只能猜测发生了什么变化。这使得系统不太可能具有相同的配置，因此，问题可能出现在一组机器上，而不会出现在另一组机器上。

在那个时候，复制一个系统的配置并不是一个硬性要求，因为服务器的数量通常不会经常改变。

然而，在按需创建和销毁服务器和容器的弹性云环境中，需要一天多次从头创建运行时环境。重要的是，这些环境的行为都是一样的，不需要人工干预。

我也在弹性公共云环境上运行我的副业项目。为了管理我的云服务器的设置和配置，我使用了 [Terraform](https://www.terraform.io) (TF)。我还使用 TF 为这些服务器提供应用程序代码。[似乎不是最佳实践](https://www.terraform.io/docs/language/resources/provisioners/syntax.html#provisioners-are-a-last-resort)，但是我不想在项目中引入另一个工具，比如 Ansible。

> “Terraform 是 HashiCorp 作为代码工具的基础设施。它允许您在可读的声明性配置文件中定义资源和基础架构，并管理基础架构的生命周期。”
> — [用 Terraform | Terraform — HashiCorp 学习基础设施代码简介](https://learn.hashicorp.com/tutorials/terraform/infrastructure-as-code)

下面是设置和配置过程的样子:

1.  在 [Hetzner cloud](https://www.hetzner.com/cloud) 上创建一个新的云服务器
2.  将应用程序复制到云服务器并安装依赖项
3.  复制 systemd 配置
4.  启用 systemd 配置
5.  重启云服务器
6.  更新 DNS 记录

我打算将我的辅助项目的主应用程序分成多个模块，这些模块需要在应用程序启动之前分别部署和链接。因此，我在寻找动态创建和提供 TF 资源的可能性。

# 地形元论元

如上所述，TF 提供了人类可读的声明性配置文件来管理基础设施的生命周期。因此，. tf 文件可以是云基础架构的文本表示，包括服务器、网络、存储等。

将动态部分引入静态配置会使阅读和理解发生的事情变得更加困难。然而，如果我们更喜欢灵活性而不是简单性，这个缺点可能是可以接受的。

那么 TF 提供了什么呢？三个元论元`for_each`、`count`和`depends_on`。

使用`count`，我们可以创建相同资源的任意数量的实例。例如，多个 [AWS EC2](https://aws.amazon.com/ec2/) 实例或 [AWS S3](https://aws.amazon.com/s3/) 桶。

> “`count`元参数接受一个整数，并创建资源或模块的多个实例。每个实例都有一个与之相关联的独特的基础结构对象，并且在应用配置时会单独创建、更新或销毁每个实例。”
> —[https://www . terraform . io/docs/language/meta-arguments/count . html](https://www.terraform.io/docs/language/meta-arguments/count.html)

与`count`类似，`for_each`参数可以基于一个地图或集合的元素创建多个资源。

> “`*for_each*`元参数接受一个映射或一组字符串，并为该映射或集合中的每个项目创建一个实例。每个实例都有一个与之相关联的独特的基础结构对象，并且在应用配置时会单独创建、更新或销毁每个实例。”
> —[https://www . terraform . io/docs/language/meta-arguments/for _ each . html](https://www.terraform.io/docs/language/meta-arguments/for_each.html)

`depends_on`参数允许我们定义 TF 模块必须被实例化的顺序。这样，我们可以告诉 TF 在配置开始之前完成服务器的创建。

> "`depends_on`元参数(如果存在的话)必须是对同一调用模块中的其他资源或子模块的引用列表。在`depends_on`参数值中不允许任意表达式，因为它的值必须在 Terraform 知道资源关系之前知道，因此在它可以安全地计算表达式之前就知道。
> —[https://www . terraform . io/docs/language/meta-arguments/depends _ on . html](https://www.terraform.io/docs/language/meta-arguments/depends_on.html)

# 行动中的反论点

`count`指令使我们能够用相同的配置创建任意数量的资源，如果我们想创建多个实例而不重复，这是很有帮助的。

# 实际操作中的 for_each 参数

与`count`类似，`for_each`参数创建一个资源的多个实例。不同之处在于`for_each`确实需要一个列表或一个映射值，它将在给定的映射或集合中设置每个元素的一个实例。

我们可以在服务器资源级别使用`for_each`参数来创建多个具有相同配置和不同名称的服务器。

但是，我们如何将一个或多个修改——基于一个映射或集合——应用到现有资源，而不是创建同一资源的多个实例呢？

对于这些情况，我们需要看起来像资源但实际上不是的东西，比如 TF `null_resource`。

> "`null_resource`资源实现了标准的资源生命周期，但是没有采取进一步的行动."
> [https://registry . terraform . io/providers/hashi corp/null/latest/docs/resources/resource](https://registry.terraform.io/providers/hashicorp/null/latest/docs/resources/resource)

当需要修改现有资源时，可以使用`null_resource`。让我们把上面的例子改为只创建一个服务器，然后在这个服务器上运行多个命令。`for_each`将遍历命令数组，然后在目标服务器上一个接一个地执行每个命令。

# 操作中的 depends_on 参数

使用`depends_on`参数，我们可以定义创建 TF 资源的顺序。有了这个指令，我们可以告诉 TF 在创建数据库之前生成一个存储卷，或者让 TF 在重新启动服务器之前完成资源设置。

在下面的要点中，我们可以看到两个 TF 模块:

*   Prometheus 活动用户导出器模块
*   重启模块

重启模块依赖于普罗米修斯模块。因此，TF 将在 Prometheus 之后实例化重启模块。从设置的角度来看，这一点很重要:重启必须在服务器设置完成后进行。

如果你对 Prometheus 主动用户输出器感兴趣，几周前我写了一个关于它的故事。查看下面的链接:

[](https://towardsdatascience.com/how-i-monitor-active-ssh-sessions-with-prometheus-and-grafana-f4811da0a8cc) [## 我如何监控与 Prometheus 和 Grafana 的 SSH 会话

### 这是时间的问题，而不是事情是否会发生。最好做好准备。

towardsdatascience.com](https://towardsdatascience.com/how-i-monitor-active-ssh-sessions-with-prometheus-and-grafana-f4811da0a8cc) 

# 摘要

基础设施即代码(IaC)工具允许我们将基础设施定义为人类可读的代码。它们的静态性质使得通常很容易阅读和理解设置了什么资源。然而，在某些情况下，灵活性和可扩展性可能比可读性更受青睐。

TF 提供了指令`count`、`for_each`和`depends_on`，在一定程度上允许动态配置。

感谢阅读。如果您有任何反馈或进一步的想法，您可以通过 Twitter [@stfsy](https://twitter.com/stfsy) 联系我