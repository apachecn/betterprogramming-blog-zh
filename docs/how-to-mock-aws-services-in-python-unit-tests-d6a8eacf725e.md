# 如何在 Python 单元测试中模拟 AWS 服务

> 原文：<https://betterprogramming.pub/how-to-mock-aws-services-in-python-unit-tests-d6a8eacf725e>

## 模拟并测试您的代码与 AWS 的交互

![](img/86725edce2ada6d03c0bd7a0ff699c65.png)

[丹尼尔·埃勒杜特](https://unsplash.com/@pixtolero2?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

您需要测试一些与 AWS 交互的代码吗？在本文中，我将介绍 [Moto](https://github.com/spulec/moto) 库，其目的是模拟 AWS 服务。这意味着您不必在 AWS 云中部署任何东西来测试您的代码。

在第一部分中，我们将创建一段与弹性计算(EC2)服务交互的简单代码。我们将检查 Moto 提供的不同嘲讽方式。这会让你对它的可能性有一个大概的了解。

接下来，我们将耦合 Moto 库和 [pytest](https://docs.pytest.org/en/stable/) 框架，以进行干净的、可伸缩的单元测试。

# 先决条件

首先，我们需要使用`pip`安装所需的不同 Python 库:

```
$ python3 -m pip install boto3 moto pytest
```

# Moto 库概述

> " Moto 是一个库，它允许你的测试轻松模拟 AWS 服务."—[GitHub 上的 moto](https://github.com/spulec/moto)

让我们创建一个包含创建 EC2 实例的简单函数的`main.py`:

包含创建 EC2 实例的函数的主文件

Moto 不仅限于 EC2 服务。它支持模拟许多其他 AWS 服务。此外，该库提供了多种用法，我们将在下面的小节中看到。

我们将进行两项测试:

*   断言存在预期的实例计数。
*   断言 EC2 实例正在运行正确的 Amazon 机器映像(AMI)。

## 装饰者

AWS 服务可以用一个简单的装饰器模拟出来:

用 Moto 装饰器模拟 EC2

## 上下文管理器

您还可以将 Moto mocks 用作上下文管理器:

用上下文管理器模拟 EC2

## 原始用途

最后，你可以采用原始方法:

带 Moto raw 用法的 EC2 模拟

# 将 Moto 与 pytest 结合

Pytest 是一个创建小型可伸缩单元测试的框架。它提供测试夹具，这些夹具是在测试函数之前运行的函数。例如，输入一些数据或建立一个数据库连接是很棒的。在这里，我们将定义夹具来配置 S3 嘲弄和伪造的 AWS 凭证。默认情况下，pytest 加载`conftest.py`的内容:

包含 pytest 夹具的 conftest.py

在`test.py`中，我们在运行专用类中的测试之前，使用上下文管理器来创建 S3。一个测试检查 S3 桶是否存在，另一个测试对象的位置:

使用 pytest 进行 S3 测试

用`pytest`命令启动单元测试:

```
$ pytest test.py
```

# 结论

在本文中，我们看到了如何使用 Moto 模拟 AWS 响应。这允许免费快速测试您的 Python 代码。再加上 pytest，它为 AWS 交互提供了可伸缩的、干净的单元测试。

# 资源

[](https://docs.pytest.org/en/stable/) [## 帮助你编写更好的程序

### pytest 和数以千计的其他软件包的维护者正在与 Tidelift 合作，以提供商业支持和…

docs.pytest.org](https://docs.pytest.org/en/stable/) [](https://github.com/boto/boto3) [## boto/boto3

### Boto3 是用于 Python 的 Amazon Web Services (AWS)软件开发工具包(SDK ),它允许 Python 开发人员…

github.com](https://github.com/boto/boto3) [](https://github.com/spulec/moto) [## spulec/moto

### 为特定服务安装 moto:$ pip install Moto[ec2，s3]这将安装 Moto，以及依赖项…

github.com](https://github.com/spulec/moto)