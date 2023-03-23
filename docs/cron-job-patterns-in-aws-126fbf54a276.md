# 在 AWS 中运行 Cron 作业的 4 种方法

> 原文：<https://betterprogramming.pub/cron-job-patterns-in-aws-126fbf54a276>

![](img/2cffa9461339329c2988793a52c6b602.png)

在 AWS 中有多种方式运行定期作业。在这篇文章中，我将分享一些我过去成功使用过的方法。这些都不需要长期的基础设施，您只需为作业运行期间使用的资源付费。我在这里的目标是分享几个选项，以便您可以选择最适合您需求的选项。

# **云观察事件+λ**

如果你的代码可以被打包成一个 [AWS Lambda](https://aws.amazon.com/lambda/) 并且工作将在 15 分钟内完成([当前对 Lambda 调用的时间限制](https://docs.aws.amazon.com/lambda/latest/dg/limits.html))，这可能是最简单的选择。

为此，创建一个 [CloudWatch 规则](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/Create-CloudWatch-Events-Scheduled-Rule.html)，并选择“Schedule”作为事件源。您可以使用 cron 表达式，也可以提供一个固定的频率(比如每 5 分钟一次)。

接下来，选择“Lambda 函数”作为目标。您应该能够从下拉列表中选择您的 Lambda，甚至提供定制的 JSON 作为 Lambda 函数调用的输入。

我通常根据它们的时间表命名我的 CloudWatch 规则(例如每 15 分钟或午夜 CST)，并有多个目标连接到它们。这有助于我轻松查找按特定时间表运行的所有作业。

# **ECS 计划任务**

该选项要求将代码打包成 Docker 容器，并且没有 Lambda 调用的时间限制。否则，它与 CloudWatch Events + Lambda 选项非常相似。

为此，您需要创建一个 Docker 映像，并将其上传到一个容器注册中心，比如 Amazon Elastic Container Registry([ECR](https://aws.amazon.com/ecr/))或 [Docker Hub](https://hub.docker.com/) 。

接下来，在 Amazon 弹性容器服务( [ECS](https://aws.amazon.com/ecs/) )中创建一个任务定义，使用 [Fargate](https://aws.amazon.com/fargate/) 作为启动类型。任务定义是您为任务提供参数的地方—图像、环境、命令等。

如果您已经有一个正在运行的 ECS 集群，您还可以使用 Amazon Elastic Compute Cloud([EC2](https://aws.amazon.com/ec2/))作为启动类型。如果集群上有运行作业的容量，这将是理想的。但是如果这不是一个选项，Fargate 将允许您在不管理服务器的情况下运行容器，并且您将只为作业使用的资源(CPU 和内存)付费。

要调度作业，可以像上面那样使用 CloudWatch(只需选择“ECS task”作为目标，而不是 Lambda)。您还可以进入 ECS 中的“默认”群集(这是在您首次开始使用 ECS 时为您创建的),并从“计划的任务”选项卡中对其进行计划。第二个选项仍然创建一个 CloudWatch 规则，但是它使得从 ECS 控制台创建更容易。

# **云观察事件+ Lambda + EC2**

如果您不能(或不想)将代码打包成 Lambda 或 Docker 容器，并且希望使用一个 EC2 实例，该实例在工作完成时启动和关闭，那么这是一个很好的选择。

EC2 有一个名为[“用户数据”](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html)的特性，它在启动时运行用户提供的脚本，可以启动这项工作。诀窍是一旦作业完成就关闭实例。完成作业后，脚本应该做的最后一件事是终止实例。

概括地说，使用 CloudWatch 规则来启动 Lambda。这将启动一个带有“user-data”的 EC2 实例，它将包含一个 Base64 编码的脚本，该脚本在实例启动时运行。该脚本将运行作业，并在作业完成后终止实例。

这里有一个 Python Lambda 来做这件事，它使用[点实例](https://aws.amazon.com/ec2/spot/)来进一步节省 EC2 成本…

下面是如何从 Linux shell 脚本中终止实例…

# **AWS 批次**

对于更复杂的工作，考虑 [AWS 批次](https://aws.amazon.com/batch/)。这使您可以定义多级管道，其中每一级都依赖于前一级的完成。

在每个阶段中，作业可以在多个节点上并行执行。AWS Batch 负责调度作业，为每个作业分配必要的 CPU 和内存，重新运行失败的作业，并在前一阶段的所有作业成功时开始下一阶段。它本质上是一个用于批处理作业的简单工作流引擎。

但是，它确实需要将代码打包成 Docker 映像，因为它使用 ECS 集群来运行作业。同样，您可以使用 CloudWatch 作为触发机制，直接在 AWS Batch 中创建作业实例。你也可以使用 Lambda，它可以应用逻辑将工作分成多个任务。

例如，如果您试图在亚马逊简单存储服务( [S3](https://aws.amazon.com/s3/) )中处理文件，您的 Lambda 函数可以查看文件总数，处理它们，然后将它们分成块，为每个块创建 AWS 批处理中的作业实例。

我应该注意，传统的提取、转换、加载(ETL)工具通常都有自己的作业调度器。如果您使用这些工具(无论是本地工具还是 AWS 工具)，使用内置调度器仍然是有意义的，因为它在运行工具固有的作业方面具有固有的优势。

在这里，我们研究了使用 AWS 本地服务运行定期作业的四种方法，这些服务可以在本地部署中取代传统的 cron 服务器。我希望这有助于您考虑将 cron 工作转移到 AWS。