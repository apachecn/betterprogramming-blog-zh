# Python 的 Moto 库简介——轻松模拟 AWS 服务

> 原文：<https://betterprogramming.pub/introduction-pythons-moto-library-easily-mock-out-aws-services-9d9d3d7e100>

## 增加测试的有效性

![](img/55aed8e6215a8df0b2e56eff818014fc.png)

[日出王](https://unsplash.com/@sunriseking?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

单元测试是我们抵御回归代码变更的第一道防线。
如果您的 Python 代码涉及到 AWS 资源的使用，您可能会发现这篇文章对您的测试覆盖很有用。

# 资源乐观主义

[资源乐观](https://testsmells.org/pages/testsmells.html#ResourceOptimism)是测试用例对外部资源做出乐观假设时出现的测试气味。

要么测试函数本身使用外部资源，要么它测试的代码部分使用外部资源——不保证在测试执行时可用。

资源乐观的结果可能是测试执行中的碎片化，换句话说，当对相同的代码库执行时，测试显示一个`pass`或`fail`结果。

另一个问题可能是需要在测试设置中管理这些外部资源，这使它变得不现实和麻烦。

`AWS` 资源，如`S3`桶或`sqs`队列是外部资源，因此，它们应该被视为乐观主义的怀疑对象。

因此，当运行我们的单元测试时，我们希望确保它们不依赖于可用的 AWS 资源。实现这一点的常见做法是嘲讽。

# 介绍 MOTO

[Moto](https://pypi.org/project/moto/) 是一个开源库，为 Python 的内置单元测试模拟库提供了一个简单的抽象。

它的文档非常棒，它支持丰富的 AWS 功能集，减轻了自己开发模拟的需要。这样你可以专注于编写高覆盖率的单元测试。

# 把手放在某物或者某人身上

让我们来看看一个简单的 Python 类:

`Project`类定义了一个项目的状态。

初始化时，它创建两个资源:一个是`s3`桶；另一个是`SQS`队列，它也使用 boto3 库为 AWS SQS 和 s3 生成一个客户端对象。

除此之外，它还有一个状态，用 UUID 表示。

当`save`被调用时，状态改变，输入`message`被存储到 s3 桶中。

当调用`restore`时，从 s3 调用之前保存的消息，然后向`SQS`队列发送通知。

之后，消息被返回到外部上下文。

让我们编写一个简单的单元测试:

这个测试很糟糕，因为它依赖于外部的 AWS 资源，但多亏了 moto，只用三行代码就可以轻松改进(懒惰的程序员会喜欢:D)。

首先，让我们安装 PyPI 的 moto:

```
pip install moto
```

现在，让我们在测试代码中使用 moto:

首先，我们从`moto.`进口`s3_mock`和`sqs_mock`

这两个是`decorators`，它们可以修饰函数或类，在修饰的代码块中创建所有 AWS 资源处理调用的模拟。

现在我们可以`decorate`类`TestMyAws`并且在所有后续的代码块中，AWS 将被嘲笑。

在这段代码中，我们还可以看到，我们不需要管理秘密，因为所有的调用都被嘲笑。

让我们进行测试:

```
python -m unittest test/test_basic.py
```

输出:

```
.
----------------------------------------------------------------------
Ran 1 test in 1.074sOK
```

# 最后一点

在我们的 init 测试代码中，我们想要尽可能多的隔离。

然而，这并不总是适用于更健壮的测试、集成测试、API 测试、回归测试或 E2E。

有时，我们确实需要使用实际的资源或者使用模拟服务器。

Moto 允许你编写稳定的测试，并且让你的测试代码更加简洁。

感谢阅读。