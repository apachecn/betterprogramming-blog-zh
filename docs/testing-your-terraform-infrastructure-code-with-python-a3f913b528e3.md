# 使用 Python 测试 Terraform 基础设施代码

> 原文：<https://betterprogramming.pub/testing-your-terraform-infrastructure-code-with-python-a3f913b528e3>

## 让我们用 Terraform HCL 和 Python 来介绍一个 API 用例

![](img/8fb4f7e7031cfc5f4cb9e57f73949a4e.png)

作者图片

如今，大部分基础设施代码都是通过 [Terraform](https://www.terraform.io/) 完成的。它已经存在了一段时间，有一个强大的社区，并且是多云的。然而，当测试 Terraform 代码时，事情开始变得棘手。虽然 Terraform 使用自己的语言(HCL)，但它的后端是用 Golang 编写的。

Terraform 模块测试的一个好模式是 [terratest](https://terratest.gruntwork.io/) ，但是正如你可能已经猜到的，你将需要用 Golang 编写这些。在这里，我们将展示如何在现有的 Terraform HCL 代码上使用普通 Python 和一个强大而简单的库 [tftest](https://github.com/GoogleCloudPlatform/terraform-python-testing-helper/) 。

# Tftest

[Tftest](https://github.com/GoogleCloudPlatform/terraform-python-testing-helper/) 是 Google 的一个小 Python 库。它使您能够以编程方式执行 Terraform 操作(plan|deploy|destroy ),并检索执行计划、输出变量等。

tftest 的强大之处在于与`pytest`的潜在组合。此外，Python 在不同的云提供商上有非常好的 SDK 支持，这使得它非常适合测试云基础设施。

# 个案研究

我们的设置将涉及一个简单的[云运行 API](https://cloud.google.com/run) (无服务器容器运行时)，但是您可以将上述方法应用于您部署的任何基础设施！

## 简单的基础设施测试

在第一个例子中，我们将做一个简单的测试来了解`tftest`。让我们试试下面的方法

*   创建一个`plan`夹具
*   断言我们的容器图像的输出名称是我们所期望的
*   检查预期的输出变量

plan fixture 只是查看我们需要查找哪个 terraform 模块/目录(并执行`apply`命令),并使输出变量作为一个对象可用。

正如您所看到的，您可以轻松地检索任何输出和/或变量来执行您想要的任何测试！

## 高级 e2e 基础设施测试

我们现在要做一个 e2e 测试，它将完成以下任务:

*   在云运行时部署 API
*   获取已部署服务的 URL
*   为请求生成准备好的身份验证会话
*   执行请求并断言响应
*   摧毁 API

前三点可以再次放在一个夹具中，以保持我们的测试功能最少，并能够为其他请求重用这一点。

这一次，我们的夹具将在测试结束时执行`apply`和`destroy`。

它还将根据允许执行请求的已部署 URL 生成一个 auth 会话。我们将通过一个`request_wrapper`函数来结束这个认证会话。

让我们把这个夹具放在`conftest.py`中:

下面是我们的测试文件，它将执行请求并断言响应:

这个文件保持最小，并且夹具可以很容易地在其他端点测试中重用。

# 警告

这种设置非常适合任何没有长时间冷启动的无服务器组件(如云运行)。一些云服务有时可能需要 15-20 分钟才能准备就绪，这使得将它们作为 CI 渠道的一部分变得很繁琐。

# 没有更好的解决办法吗？

这里介绍的设置在现有的 terraform 代码库上运行良好。但是，如果你正在开始一个新项目，有更合适的解决方案。

Terraform 已经有了测试版 [Terraform CDK](https://www.terraform.io/cdktf) ，它允许你直接使用 Python(或任何其他支持的编程语言)来声明你的基础设施，这使得测试变得更加容易。

普鲁米也是一个很好的候选人，它在 CDK 这边更成熟。

如果你想只使用 AWS，你也可以使用 AWS CDK，但是你失去了在一个没有厂商限制的 IAC 框架中投资知识的好处。

无论如何，很高兴终于能够使用标准编程语言来管理基础设施，因为您可以直接利用其中包含的所有测试工具包！

测试愉快。

*链接完整演示 GitHub 资源库* [*这里*](https://github.com/mehd-io/cloudrun-terraform-tftest-demo) *。*

```
**Want to Connect?** Follow me on [YouTube](https://www.youtube.com/channel/UCiZxJB0xWfPBE2omVZeWPpQ) or [LinkedIn](https://linkedin.com/in/mehd-io/)
```