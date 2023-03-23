# 如何组织你的 AWS CDK 项目

> 原文：<https://betterprogramming.pub/how-to-organize-your-aws-cdk-project-f1c463aa966e>

## 过去，我会使用 Terraform 以代码的形式构建我的基础设施。最近，我发现了 AWS CDK，并决定尝试一下

![](img/87cfd28b6d19b4ad4800fb3caffa54ae.png)

[阿兰·范](https://unsplash.com/@alain_pham?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

在本文中，我将与您分享如何组织和调整您的 [CDK](https://aws.amazon.com/cdk/) 存储库来提供多阶段 AWS 基础设施。

我们将构建一个简化版本的基础设施，由一个 VPC 及其子资源、一个 Kubernetes 集群和一个 [Elasticsearch](https://www.elastic.co/elasticsearch/) 集群组成。
这个项目是用 Python 开发的，源代码可以在 [GitHub](https://github.com/aftouh/cdk-template) 上找到。

# 启动 CDK 应用程序

第一步是使用以下 CDK 命令行初始化 Python 项目:

```
cdk init sample-app — language python
```

这会生成几个文件和目录。这些是保留的元素:

*   `setup.py`:定义 Python 包(名称、文件、包元数据、运行时依赖等。).
*   `cdk.json`:告诉 CDK 工具包如何执行应用程序。
*   `app.py`:项目的切入点。
*   `tests/unit`:最后，可以像对任何其他应用程序一样对 infra 进行单元测试。

这个 CDK `sample-app`是一个很好的起点，但显然不足以建立一个生产就绪的多阶段基础设施。

# 组织堆栈和构造

CDK 框架的核心组件是堆栈和构造。
一个*栈*是部署的单位。堆栈中定义的所有资源(或结构)都作为一个实体提供或销毁。*构造*是定义一个或多个 AWS 资源的基本构建块。

在项目开始时，花点时间考虑如何在 CDK 应用程序中组织堆栈和构造非常重要。稍后试图重新组织你的应用程序可能会变得混乱，并引入突破性的变化。

在我们的环境中，我们最终有三个堆栈:

*   网络堆栈:定义网络资源，如 VPC、子网和安全组。
*   计算堆栈:定义无状态资源，如 Kubernetes 集群、bastion。
*   数据堆栈:定义有状态资源。在我们的上下文中，这是一个弹性搜索集群。

这种分裂的主要原因是:

*   灵活性:如前所述，将所有 AWS 资源放在一个堆栈中迫使我们一次创建或销毁所有资源。例如，通过这种三堆栈设置，可以销毁数据和计算堆栈，同时保持网络堆栈中定义的弹性 IPs 和 VPC 对等配置。
*   可维护性:这种分离提供了更好的开发人员体验。
    例如，如果贡献者正在计算堆栈上工作，他们不需要在短暂的贡献者阶段创建数据堆栈来测试他们的更改。在我们的例子中，我们不需要等待 30 分钟来创建 Elasticsearch 集群。
*   成本优化:这种拆分提供了在周末安排删除非生产阶段的计算堆栈的可能性，同时保留我们的数据集。我们仍然可以为数据堆栈设置不同的销毁/创建策略。

这三个堆栈位于`stacks`目录下，每个都在一个单独的子目录中。在每个堆栈中，我们定义了一个或多个使用一个或多个 CDK 内置结构的定制结构。例如，`Eks` 自定义构造使用`aws_eks.Cluster`、`aws_eks.NodeGroup`、`aws_iam.Role`、作为内置构造:

这有助于我们代码的可读性和可维护性。在这个层次上，它更多的是关于如何像组织其他应用程序一样组织代码。

现在我们有了三个栈，我们需要做的就是在`app.py`文件中实例化它们:

# 文件配置

Infra as 代码的优势之一是能够为不同阶段(例如开发、生产)复制相同的堆栈。

使用 Python 这样的编程语言构建基础架构，让我们有机会像处理其他应用程序一样处理我们的 CDK 应用程序配置。一种常见的方法是使用 YAML 文件。在我们的例子中，这些文件存储在`config`目录中，并使用 Python [benedict](https://pypi.org/project/python-benedict/) 库进行解析:

*   `common.yaml`:包含应用于所有阶段的共享配置和默认值。
*   `<stage-name>.yaml`:每个阶段都有一个专用的配置文件，该文件定义了特定阶段的输入(AWS 帐户、区域、网络 CIDR)，并允许覆盖通用配置文件中设置的默认值(实例类型、集群大小等)。).

现在我们已经设置好了配置，我们可以使用以下命令行之一开始部署阶段:

```
# Deploy the 3 stacks in the prod stage
cdk deploy -c stage=dev '*'# Deploy the Network and Compute stacks in the dev stage
cdk deploy -c stage=dev NetworkStack ComputeStack
```

请看一下 [Makefile](https://github.com/aftouh/cdk-template/blob/main/Makefile) 以获得关于阶段生命周期命令的更多细节。

# 其他元素

我想在这里提一下这个项目的其他一些要素:

*   如你所料，它存储了应用程序的当前 CDK 版本。这很有用，因为 CDK 版本在不同的地方使用(Python 依赖、CI 等)。).把它放在一个地方真的很有帮助——尤其是对于升级。
*   `utils`:包含一些在堆栈中使用的 util 函数(如配置解析器)和标签生成器函数。

这基本上就是我们的 CDK 存储库的组织方式。它非常适合我们的环境，通过基于 GitOps 工作流设置可靠的 CICD，我们平静地投入生产。

如果你想了解更多关于 GitOps 工作流程的信息，我推荐你阅读[这篇文章](https://medium.com/better-programming/aws-cdk-continuous-integration-and-delivery-using-travis-ci-ee5dd7549434)。

# 结论

如果您习惯于地形，使用 CDK 构建您的云环境需要不同的思维和组织方式来处理基础架构项目。通过使用 Python 作为编程语言，我有了处理这个项目的感觉，就像处理任何其他标准 Python 项目一样。团队中其他不太习惯 infra 人员的成员也不太愿意为这个项目做贡献，我相信这是因为不需要学习新的“编程”语言。

CDK 是一个很有前途的工具，可以帮助组织在其 DevOps 文化中走得更远。