# 您应该对无服务器应用程序进行快速优化

> 原文：<https://betterprogramming.pub/quick-optimizations-you-should-make-to-your-serverless-applications-9cc73ec464b9>

## 发现一些微调无服务器应用程序的方法，使其性能比以前更好

![](img/13c0da270652db1213f2e5d63e18abc5.png)

照片由 [Jungwoo Hong](https://unsplash.com/es/@hjwinunsplsh?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/improvement?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

上周，我对我和我的团队已经开发了几年的一个应用程序进行了一系列负载测试。我们都很兴奋地看到我们的完全无服务器的应用程序将如何在我们预期的高峰时间的三到四倍的负载下扩展。

我们开始了小规模的第一轮试验。*一切都完美地进行着*。

然后我们把它提高到刚刚超过预期的峰值容量。*它仍然工作得很好*。

然后我们故意试图打破它。比我们预期的要高。它仍然(大部分)完美地工作着。

直到几天后，当我整理一些统计数据时，我才恍然大悟。应用程序可能已经扩展并响应了正确的状态代码，但是它的性能并没有达到我想要的标准。

这不是负载测试使应用程序过度紧张的结果，它在负载下表现不佳。这就是我们构建应用程序的方式。直到我们运行负载测试，并真正深入一些分析，我才意识到它比它应该慢。它也有几个令人讨厌的数据丢失的惊喜。

我很清楚我们必须做点什么。但这不仅仅是提升所有 Lambda 函数的内存。

> 拥有一个无服务器的应用程序不仅仅意味着“它是用 Lambda 编写的”

AWS 无服务器服务包括(但不限于) [EventBridge](https://aws.amazon.com/eventbridge/) ， [SQS](https://aws.amazon.com/step-functions/) ， [SNS](https://aws.amazon.com/sns/) ， [DynamoDB](https://aws.amazon.com/dynamodb/) ， [Step Functions](https://aws.amazon.com/step-functions/) ， [S3](https://aws.amazon.com/s3/) ，以及 [API Gateway](https://aws.amazon.com/api-gateway/) 。无服务器应用程序是这些服务的组合，每个服务扮演一个特定的角色。

负载测试显示的分析表明，我需要对这一系列服务进行一些调整。在不必要的高额账单、比可接受的 API 端点更慢的速度和一些无法追踪的故障之间，是时候回到绘图板了。

# λ优化

您可以基于 Lambda 函数的运行时执行各种优化。但是您可以对所有函数做一些事情。

将 Lambda 函数调优为[优化内存使用](https://aws.amazon.com/blogs/compute/operating-lambda-performance-optimization-part-2/)不仅可以提高函数的性能，还可以为您省钱。通过在一个函数中找到允许内存的最佳点，您可以减少运行所需的内存量，以弥补增加的分配内存的成本。

有两个很好的选项来调整您的函数。由 [Alex Casalboni](https://twitter.com/alex_casalboni) 开发的 [AWS Lambda 功率调谐](https://github.com/alexcasalboni/aws-lambda-power-tuning)解决方案是一个状态机，它在不同的内存中运行您的功能，并为您提供它在不同级别上如何执行的详细分析。您可以选择针对性能、成本或两者进行调优。这种方法要求您创建合成有效载荷来进行评估。

另一个选择是为 Lambda 使用 [AWS 计算优化器。这是一项选择加入的服务，它分析您的功能的利用率和规格指标。根据它的分析，它将提供推荐的内存，并告诉您现有内存与现有内存的成本差异。因为这是分析以前的调用，所以您不需要做任何额外的事情来获得建议。](https://docs.aws.amazon.com/compute-optimizer/latest/ug/view-lambda-recommendations.html)

对于 NodeJS 用户来说，您可以对函数进行一些优化，使它们的执行速度保持一致。(免责声明:这些可能也适用于其他运行时，但是我没有对它们做任何研究。)

## 重用 HTTP 连接

通过将`AWS_NODEJS_CONNECTION_REUSE_ENABLED`环境变量设置为`1`，您可以利用一种内置的方式告诉节点[重用连接以获得最佳性能](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/node-reusing-connections.html)。这意味着如果您有多个 SDK 调用，该函数将只创建一次 HTTP 连接，并将其用于所有调用。如果您使用 KMS 加密您的数据，这个命令特别有用。

## 全局缓存变量

利用函数中的全局作用域。您可以定义 SDK 客户端，建立连接，并将其他不可变变量[存储在处理程序之外，以便跨 Lambda 调用重用变量](https://docs.aws.amazon.com/lambda/latest/operatorguide/global-scope.html)。这消除了全局范围内所有内容的初始化时间，还让您可以在内存中缓存查找，这样您就不必多次调用来一次又一次地检索相同的值。

**注意**:这是用于热启动功能调用。在冷启动时，一切都将被初始化。

# REST API 优化

API 性能可以说是任何应用程序最重要的组成部分之一。你的客户如何看待你的应用，会直接影响你的用户留存率。向他们提供[一贯快速的 API](/power-tune-your-serverless-api-for-happy-customers-289e83e3938f)是给你的消费者灌输快乐的好方法。

## 使用 VTL 进行直接服务集成

对于只需要转换的简单查找的端点，您可以[直接代理 DynamoDB](https://www.andmore.dev/blog/build-serverless-api-with-no-lambda/) 来加载数据。根本不需要 Lambda 函数。使用 [VTL](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-mapping-template-reference.html) 加速你的执行(并节省你的钱！)通过直奔源头。

## 将阶跃函数用于复杂的工作流

Step 函数允许您直接从 API 网关同步执行 express 状态机[。使用阶跃函数和](/how-to-build-lightning-fast-apis-with-aws-step-functions-d1725624aaaa)[直接 SDK 集成](https://docs.aws.amazon.com/step-functions/latest/dg/supported-services-awssdk.html)完全消除了 Lambda 冷启动，在许多情况下，[提供了比使用 Lambda 更快、更便宜的选择](/lambda-vs-step-functions-the-battle-of-cost-and-performance-5f008045e2ab)。

## 利用 HTTP APIs

AWS 提供了两种类型的 API，REST APIs 是全功能的前代 API。HTTP APIs 是更新、延迟更低、成本更低的替代产品，功能不太丰富。如果您的 API 需求很简单，您可以用 HTTP API 替换 REST API。在决定从一个移动到另一个之前，一定要检查一下[特性奇偶校验列表](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-vs-rest.html)。

## 转向异步处理

更高级一点，异步处理是无服务器开发的核心。使用 API，您返回一个 [202 —接受状态代码](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/202)，让调用者知道将采取进一步的行动。你要么让呼叫者轮询更新，要么通过像 [WebSockets](/introduction-to-aws-websockets-8b336a92c379) 这样的机制向他们推送更新。您可以通过采用[存储优先的方法](/build-better-serverless-apis-by-going-storage-first-597784f8f399)来开始异步处理。

# 运营优化

应用程序的操作指的是它如何运行。这比我们用 Lambda 和 API Gateway 做的优化更高级。在这一级别，我们更多地处理服务配额、指标和数据流。这里的优化更倾向于面向编排。

## 节流低优先级功能

AWS 账户的默认软限制[是每个地区 1000 个并发 Lambda 执行](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html)。这意味着最多可以同时运行 1000 个功能。因为我们知道 Lambda [在负载](https://docs.aws.amazon.com/lambda/latest/dg/invocation-scaling.html)下水平伸缩，所以我们需要考虑低优先级函数。

例如，如果我们有一个 Lambda 函数，它在作业完成后向用户发送电子邮件，我们不希望它向外扩展并消耗掉我们 1，000 个限制中的很大一部分。为了解决这个问题，我们可以设置[保留并发](https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html)来限制特定 Lambda 可以拥有的并发执行的数量。像发送电子邮件这样的低优先级异步功能在延迟执行的情况下没有问题。将扩展留给任务关键型功能。

## 按需扩展 DynamoDB

DynamoDB 有两种扩展方式:按需扩展和资源调配。提供了默认的扩展机制，这意味着您可以控制 DynamoDB 允许使用的读写容量单位。这是在大容量应用程序中防止成本失控的一个好方法。但是，这也意味着如果没有正确调整，它可能会导致节流。

如果您不知道需要什么容量，或者您的应用程序有明显的峰值和谷值，那么可能是时候考虑按需扩展了。它会根据您的应用需求自动上下伸缩。这在低流量时期为您节省了资金，同时仍然允许您的应用程序达到峰值。

## 到处添加死信队列

这与其说是优化，不如说是“你以后会感谢我的。”鉴于无服务器应用程序强烈的事件驱动架构，数据很容易在以太中丢失。幸运的是，AWS 为[提供了向 DLQ](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html) 发送几乎所有数据的选项。

从使用 EventBridge 的[失败事件交付到当异步流程中的某个事件](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rule-dlq.html)失败时的 [Lambda 目的地，您可以捕获失败事件，以便可以在下游处理它。即使您一开始没有自动处理 DLQ 错误，捕获故障以查看您的问题所在也是值得的。](https://aws.amazon.com/blogs/compute/introducing-aws-lambda-destinations/)

# 结论

当我第一次进入无服务器模式时，我认为优化是为了影响成本而进行的严格的修改。但远不止如此。它是性能，您处理伸缩的流畅程度，以及您使您的应用程序易于维护的程度。

我经常谈到[总拥有成本(TCO)](https://www.readysetcloud.io/blog/allen.helton/lambda-vs-step-functions-breakdown#total-cost-of-ownership) ，这比您的账单要多得多。这就是我上面所说的一切。对生产问题进行故障排除和修复的速度有多快？你的解决方案有多明显？雇佣和培养新的工程师有多容易？

通过整合上面列出的优化，我们可以降低总体拥有成本。上面列出的所有增强功能可能不适用于您的应用程序，并且您可能有我遗漏的其他功能。软件既是福也是祸，因为“视情况而定”似乎适用于几乎所有情况。

无论如何，给他们一个机会。我希望他们能像帮助我一样帮助你。

编码快乐！