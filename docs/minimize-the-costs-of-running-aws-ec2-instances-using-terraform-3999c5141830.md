# 如何使用 Terraform 最小化运行 AWS EC2 实例的成本

> 原文：<https://betterprogramming.pub/minimize-the-costs-of-running-aws-ec2-instances-using-terraform-3999c5141830>

## 让我们优化 AWS 基础设施的使用并节省一些资金

![](img/9422f28b7d82bddd0fd01755ef04cde7.png)

照片由[伊梅尔达](https://unsplash.com/@unleashed_?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

**简介**

降低企业的运营成本总是令人感兴趣的，尤其是当涉及到依赖于服务实际使用的可变成本时。

在测试环境中运行 AWS EC2 实例 24/7 来托管和运行软件应用程序是相对昂贵的，并且始终运行这些实例的成本可能会超过分配给项目的预算。

清理 AWS 帐户并终止所有不需要的 EC2 节点是降低运行 EC2 实例成本的第一步。然而，这并不是唯一可以降低成本的方法。在大多数情况下，即使进行了清理，软件开发过程仍然需要许多 EC2 实例。所需实例的确切数量取决于项目的规模以及项目贡献者或团队的数量。

这些实例中的大多数将在软件开发过程中用作测试环境，工程师将在工作时间大量使用这些实例来部署新版本的源代码和测试应用程序的新功能。然而，这些环境不太可能在周末以及下班后使用。因此，自动启动和停止这些实例并使它们仅在工作时间可用是没有意义的。实现这一任务的一个建议如下:

*   自动重启可以应用于基于标签的单个 EC2 节点。这意味着，要在给定的 EC2 节点上应用自动启动和停止，EC2 节点需要用一个具有特定值的特定标记(姑且称之为`Auto-Start`)来标记。
*   所有标有标签`Auto-Start`的 EC2 实例将在每个工作日的早上`6:30 AM`启动。
*   所有带有标签`Auto-Start`的 EC2 实例将在每个工作日的晚上在`7:00 PM`停止。

自动停止和启动 EC2 实例在 AWS 帐户上有详细的记录，可以按照此[页面](https://aws.amazon.com/premiumsupport/knowledge-center/start-stop-lambda-cloudwatch/)上提供的说明来实现。然而，我有一些关于指令的注释。

*   需要从 AWS 控制台手动执行指令😢。
*   受影响的 EC2 节点需要包含在启动和停止脚本中，这意味着如果需要通过包含新的 EC2 节点来扩展解决方案，则有必要使用新节点的*id*来修改这些脚本。

在这篇文章中，我将介绍实现上面提出的解决方案所需的步骤，并使用 Terraform 应用它。

## **实施**

第一步是创建允许以下操作的 IAM 策略:启动 EC2 实例、停止 EC2 实例和列出 EC2 实例。可以使用下面的 terraform 资源定义创建此策略。

下一步是定义 IAM 角色，并将上一步中创建的策略附加到创建的角色。terraform 资源`aws_iam_role`用于创建角色，并使用`assume_role_policy`分配将使用该角色的服务。但是，它不能用于将 IAM 策略附加到角色；为此，我们需要使用另一个名为`aws_iam_role_policy_attachment`的地形资源。下面的代码片段说明了如何在 Terraform 中定义这些资源。

下一步是定义`lumbda`函数，它将处理 EC2 实例的停止和启动。但是在使用 terraform 定义 AWS 中的`lumbda`函数之前，让我们花一点时间来说明可以用于此类函数的 Python 脚本。 [boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ec2.html) 是一个 AWS Python 客户端库，可用于在 AWS 上执行操作。实现的脚本应该提供一个简单的接口，用于根据 EC2 实例标记停止和启动 EC2 节点。下面是一个脚本的实现，它定义了两个函数，一个函数用于停止所有用带有`true`值的标签`Auto-Start`标记的 EC2 实例。另一个函数启动相同的 EC2 实例列表。

现在，用于停止和启动 EC2 实例的 Python 脚本已经准备好了，我们可以通过使用下面的 terraform 资源定义(文件名是 Python 函数的压缩文件的路径)来创建`lambda`函数。

下一步是定义 CloudWatch 规则，该规则将触发上一步中定义的`lambda`函数的执行。为了实现这一点，我们需要为我们想要支持的每种情况定义一个规则(两种情况，一种用于停止，另一种用于启动)。这些规则将在一天中的特定时间根据 Cron 表达式触发。下面的代码片段定义了 stop 用例的规则，它定义了应该触发`lambda`函数的确切时间。相同的代码片段可以被修改并用于触发 EC2 节点的启动。

授予 CloudWatch 权限的最后一步是执行`lambda`函数。这是一个必要的步骤，没有它，CloudWatch 将无法触发*功能。*

*下面是这篇文章提出的解决方案的完整实现:*

# ***结论***

*将我们通常执行的尽可能多的动作自动化总是一个好主意。Terraform 是一款非常棒的工具，可以帮助我们实现基础设施任务和操作的自动化。*

*用 Terraform 实现这样的解决方案使 AWS 基础设施更具可移植性，如果需要的话，很容易被销毁和重新创建。另一方面，优化云基础设施的使用是降低基础设施运行成本的必要条件。*