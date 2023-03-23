# AWS Lambda 的 7 个现成用例

> 原文：<https://betterprogramming.pub/7-out-of-the-box-use-cases-for-aws-lambda-30d7dc5c99f7>

## 无服务器应用的灵感

![](img/1d3b99f16ae1af09f1f5d84b94108bcf.png)

何塞·安东尼奥·加列戈·巴斯克斯在 [Unsplash](https://unsplash.com/s/photos/out-of-the-box?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

[AWS Lambda](https://aws.amazon.com/lambda/) 自 2014 年以来一直存在。如果这是您第一次阅读 AWS Lambda，我们可以将其总结为一种无服务器计算服务，让您(开发人员)在不提供或管理服务器的情况下运行代码。

AWS 运营和支持团队负责运营和管理任务，例如资源供应和扩展、监控系统运行状况、对底层资源应用安全补丁以及代码部署。

你，开发者，写代码上传到云端。AWS Lambda 服务会执行您的代码，并在您的负载增加或减少时自动处理自动缩放的所有复杂细节。

您可以使用 AWS Lambda 运行您的代码来响应事件。由于 AWS Lambda 在 AWS 生态系统中运行，这些事件可以链接到您编写的 Lambda 函数，例如 Amazon S3 桶或 Amazon DynamoDB 表中的数据更改，或者使用 Amazon API Gateway 响应 HTTP 请求的事件触发器。

开发人员只为他们使用的计算时间付费。当你的代码没有执行的时候，没有额外的费用，如果你想在预算有限的情况下开发应用程序，这是一笔非常划算的交易。告别开发人员和运营管理员。

Lambda 自带了对许多编程语言的原生支持，比如 Java、Node.js 和 Python 等等。语言的选择取决于您的语言天赋以及其他因素，如派生时间和模块依赖性支持。

有些语言可能优于其他语言。有一系列允许快速开发和部署的开源框架，如[无服务器](http://serverless.com/)(以前称为 JAWS)[Apex](http://apex.run/)、 [Sparta](http://gosparta.io/) 、 [Zappa](https://github.com/Miserlou/Zappa) 等。

现在，这一切听起来很棒，但当使用 Lambda 时，并不全是玫瑰和阳光。在撰写本文时，还存在一些局限性。以下限制直接摘自 AWS 网站。

请注意经常查看 AWS 网站的更新，因为这些限制可能会随着时间的推移而调整。

# AWS Lambda 限制

截至 2020 年 1 月 21 日的运行时环境限制:

*   磁盘空间限制为 512 MB。
*   默认部署包大小为 50 MB。
*   内存范围从 128 到 3008 MB。以前，您的函数可用的最大内存量是 1536 MB。
*   一个函数的最大执行超时是 15 分钟。
*   Lambda 的请求限制:请求和响应主体有效负载大小最大为 6 MB。
*   事件请求正文最大可达 128 KB。
*   λ*冷启动。*Lambda 函数需要一些时间来处理第一个请求，因为 Lambda 必须启动该函数的一个新实例。这意味着不常使用的无服务器代码可能会遭受更大的响应[延迟](https://en.wikipedia.org/wiki/Latency_(engineering))。(这可以通过定期 ping 您的函数来缓解。)
*   应用程序依赖可能会很麻烦，尤其是如果第三方库链接到外部包，如 Python 代码的 C 程序。这成为 50 MB 包大小限制的一个问题。
*   如果你需要这样的东西，通过云监控日志来监控 Lambda 会很昂贵。

取决于你如何看待这个限制，它并不都是悲观的。这些限制可以从不同的角度来看，作为 AWS 的设计选择。

为了拥有无服务器模型和架构，拥有一个快速且可扩展的系统是必不可少的。这意味着必须实施限制，以达到最佳平衡，即权衡取舍。

因此，如果你决定使用 Lambda，你需要确保你的用例在限制范围之内。

有解决这些限制的变通方法，比如持久化数据或状态机，但是本文的目的不是解决这些限制或提供解决方案。

相反，我们想深入了解 Lambda 在限制范围内可以做的 7 件开箱即用的事情。关于无服务器架构的更多信息，请看这个 [Wiki](https://en.wikipedia.org/wiki/Serverless_computing#Elasticity_versus_scalability) 页面，获得更多本文可能没有涉及的见解。

现在我们已经介绍了基础知识，让我们深入一些开箱即用的用例，在这些用例中，无服务器 Lambda 是用来完成工作的合适工具。

# 1.无服务器网站应用程序

用 Lambda 构建一个无服务器的网站或电子商务网站非常简单，并能让你在高峰时刻爆发。为了充分利用 Lambda，对 Lambda 的每次调用都应该是无状态的；这是为了降低复杂性。

使用无状态，您的请求执行，响应应该在一个执行周期内完成。对于一个基本的网站，你只需要 AWS API Gateway、DynamoDB/RDS、亚马逊 S3 和亚马逊 Cognito 用户池。

为了说明此类网站的流程，如下所示:

*   网站内容 HTML、CSS、JavaScript，托管在 AWS S3 和 [CloudFront](https://aws.amazon.com/cloudfront/) CDN 上，用于分布式多区域内容缓存。UI 之间的通信通过 JavaScript 进行，后端 API 通过 API Gateway 和 AWS Lambda 构建。
*   DynamoDB NoSQL 数据库或 RDS ( [Postgres](https://www.postgresql.org/) ， [MySQL](https://www.mysql.com/) ， [SQL Server](https://www.microsoft.com/en-us/sql-server/default.aspx) )用于通过 API 的 Lambda 函数持久化数据。从 UI 到后端 API 的所有执行应该在 15 分钟或更短时间内完成。你的 Lambda 函数和数据库的托管可以被 VPC 隔离，将它们与其他网络隔离开来，增强你的应用程序的安全性。
*   Amazon Cognito 在安全后端 API 的帮助下用于用户认证和管理。

当使用 AWS 服务时，这里的关键是理解如何将服务链接在一起以构建您的解决方案。只要您了解每个服务的功能，就可以像拼图游戏一样将各个部分组合起来。

# 2.计划的、Cron 的、定期的作业

这些可以是任何事情，从定期支付，将你的 Lambda 连接到 PayPal Stripe 等第三方服务，到运行后台脚本来执行重复任务或向订户群发送大量电子邮件。

我们还可以进一步利用 S3 事件，例如，将新文件拖放或上传到 AWS S3 可以触发 Lambda 事件，进而处理文件或将图像文件转换为缩略图或压缩它们。

为了实现预定的作业，我们可以利用 CloudWatch 事件，这些事件**支持类似 cron 的表达式，可以用来定期触发 Lambda 函数。**

**向占用大量 JVM 内存的 [Spring Batch](https://spring.io/projects/spring-batch) cron 作业和 Python [Celery](http://www.celeryproject.org/) 的周期性作业说再见吧，至少对于短期任务是这样。**

# **3.用 Kinesis 数据流和 Lambda 流大数据**

**Lambda 是高度可按需扩展的；它非常适合在 S3、[红移](https://aws.amazon.com/redshift/)、 [Kinesis](https://aws.amazon.com/kinesis/streams/) 和数据库服务之间转换数据，并进行动态过滤。您可以轻松地将 Lambda 放在服务之间，根据需要转换和加载数据。**

# **4.向亚马逊 Alexa 问好**

**Alexa 是亚马逊基于云的智能语音控制代理。亚马逊提供的语音产品是没有脸或 UI 的无头设备。例如:Echo、Echo Plus、Echo Dot 和 Tap。**

**Alexa 是一种允许我们与设备对话并让它响应我们请求的技术。Alexa 利用的技能是可以执行的命令。**

**每次你问 Alexa 一个请求，本质上有一个技能被触发，然后可以链接到你的 Lambda 函数来处理一个事件。为了实现这一点，你说出一个神奇的词:“Alexa”，然后是你的声音信息，这些信息被传输到云端进行分析。**

**“Alexa”之后的任何东西都是用亚马逊建立的自然语言处理系统处理的。**

# **5.实时通知**

**随着我们走向一个更加互联的世界，即使在发展中国家，宽带也变得相对实惠，为用户提供这种实时体验变得更加容易。**

**有了 Lambda、SQS、SNS 和 AWS Lex，我们可以构建实时响应的聊天机器人。如果你不熟悉 Lex，它是一个 AWS 服务，用于在任何使用语音和文本的应用程序中构建对话式界面。**

**Lex 利用自动语音识别(ASR)的深度学习功能将语音转换为文本，并利用自然语言理解(NLU)来识别文本的意图。**

**这使您能够构建具有高度吸引人的用户体验和逼真的对话式交互的应用程序。Lex 利用了亚马逊 Alexa 使用的相同深度学习技术。**

# **6.中间件**

**无服务器中间件组件是充当其他服务之间桥梁的服务。一个简单的用例是促进交易的中间件。**

**这可能是执行区块链支付发送以太币到目标钱包地址或检查您的以太余额。我们还可以构建一个基于另一个中间件的输入来执行业务流程的中间件。**

# **7.工作流程**

**工作流是我们构建的任何应用程序的关键部分。我们需要通过逻辑条件编排一个可重复的活动模式，以便执行动作或任务。**

**当我们使用 lambdas 时，我们需要引入 AWS 步骤函数来实现工作流的概念。AWS Step Function 服务使分布式应用程序的组件作为可视化工作流中的一系列步骤进行协调变得很容易。**

**这意味着你可以链接你的 lambdas 来完成每一步的特定任务。**

**让我们以构建一个需要多级审批的应用程序为例，以防保险公司需要根据索赔请求支付资金。**

```
GIVEN we have a manager who receives a request for first-level approval THEN approves request, send the request to the second-level manager for second-level approval THEN send request for CEO review THEN on final review initiate reimbursement and send out notification.GIVEN User rejects the request THEN initiate rejection process.
```

**有了这种将步骤函数引入 lambda mix 的能力，您可以快速构建和运行状态机，以可靠和可伸缩的方式执行应用程序的步骤。**

**所有这些都不需要配置第二台服务器。这使您能够构建相当复杂的应用程序。**

# **二等奖；荣誉奖；H 奖**

**作为一种无状态的计算功能即服务，Lambda 为用户提供了一个可以利用分布式计算的单一进程。Lambda 的大多数用例都集中在一个预先定义的时间段内执行一个任务，现在 image 已经能够批量处理所有这些 Lambda 来执行一些分布式工作。**

**如果您曾经使用过 Dask、Apache Spark，那么相同的概念可以在一定程度上应用于 lambda——前提是所执行的工作属于相同的约束 lambda 限制，例如参数调整。想象一下，使用 python 中的 cloudpickle 或某种序列化反序列化技术将数据上传到 s3 bucket，该 bucket 触发向单个 lambda 发送代码和数据，以执行 x N 个 lambda 事件。Lambda 然后执行工作，然后通过预定义的媒介(例如，通过消息队列节流)将这些结果的子集推回给调用用户——本质上，您拥有的是一个执行[分布式计算](http://pywren.io/)的平台。**

# **最后的想法**

**如果您到现在还没有注意到，这些用例中的大多数都可以在不使用 Lambda 的情况下解决，如果不是全部的话，但是它们是独特的属性，正如本文开始时概述的那样，它们确实使 Lambda 变得很好。**

**Lambda 不是一个万能的工具。用例非常广泛，我们几乎没有提到所有的可能性。需要时刻牢记的是，您需要度量和评估您的用例，权衡利弊，并做出 Lambda 是否是这项工作的正确工具的逻辑决定。**

**如果您需要一个高度异步、分布式、高度并发的服务，并且有不频繁的使用需求。有节省成本的要求，它不需要总是打开，有时是零星的，有不可预测的流量需要扩展，是高度无状态和动态的，那么无服务器可能是您想要考虑的事情。**

**要记住的一个关键规则是从小处着手，不断重复。如果 Lambda 变得太受限制，就扩展并利用更传统的部署风格。**

**它们总是随着 AWS 服务不断更新。如果您想了解这些服务的最新信息，请查看即将发布的[公告](https://aws.amazon.com/new/?whats-new-content-all.sort-by=item.additionalFields.postDateTime&whats-new-content-all.sort-order=desc&wn-featured-announcements.sort-by=item.additionalFields.numericSort&wn-featured-announcements.sort-order=asc)。**