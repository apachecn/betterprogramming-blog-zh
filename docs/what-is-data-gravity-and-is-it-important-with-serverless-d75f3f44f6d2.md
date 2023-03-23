# 什么是数据重力，它对无服务器是否重要？

> 原文：<https://betterprogramming.pub/what-is-data-gravity-and-is-it-important-with-serverless-d75f3f44f6d2>

## 当谈到供应商锁定时，人们很少谈到数据重力。去无服务器需要考虑吗？

![](img/e5ad1b25f7c510cdb25121de1e584487.png)

美国宇航局在 [Unsplash](https://unsplash.com/s/photos/gravity?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

几年前当我开始使用无服务器时，你很少会不听到“*供应商锁定*”这样的话。

对于那些很幸运以前没有听过这句话的人来说，这意味着通过承诺使用特定的供应商，你将自己与他们捆绑在一起。意思是你不能和他们分开，即使你想分开(或者很难分开)。

虽然无服务器本身并没有错，但这真的重要吗？我们曾经调用供应商锁定*来做出决定*。您选择一个供应商，使用他们的特性集，然后构建您的软件。

但这让一些人感到不快，这没什么。然而，在选择云供应商来托管和构建您的软件时，您必须做出决定。有些人不遗余力地构建“独立于供应商”的解决方案，他们可以在周一从 AWS 获取代码，并在周二在 Azure 中运行。

做这件事的人值得称赞。这增加了解决方案的复杂性，但他们增加了在服务提供商之间移动代码的能力，以防出现可怕的错误(无论是从灾难恢复的角度还是从价格上涨的角度)。

*还是他们有？*

虽然代码可能会移动，但是您的所有数据呢？这将如何在供应商之间转移？你不能重新开始，你会失去所有的顾客。它需要和你的代码一起移动。

# 数据引力到底是什么？

随着应用程序中存储量的增加，移动的能力变得越来越困难。这类似于*引力*，其中[体积越大，引力](https://coolcosmos.ipac.caltech.edu/ask/300-What-is-gravity-#:~:text=The%20more%20massive%20an%20object%20is%2C%20the%20stronger%20its%20gravitational%20pull%20is)越强。因此得名**数据引力**。

据估计，[每天会产生 250 万兆兆兆字节的数据](https://earthweb.com/how-much-data-is-created-every-day/)。如今，数据就是一切。世界范围内的数据量之大令人震惊。您可能也会对应用程序中的数据量感到惊讶。

在无服务器应用程序中，数据重力来自几个不同的方面:

**数据库记录**——也许是最明显的数据重力来源，无论是 [DynamoDB](https://aws.amazon.com/dynamodb/) 还是 [Aurora](https://aws.amazon.com/rds/aurora/) ，这些记录都会随着应用程序中的交互而积累。当您的消费者使用您的 API 时，数据的重要性会随着记录的保存而增加。

**文档存储** —借助 AWS， [S3](https://aws.amazon.com/s3/) 是一项保存从分析数据、视频和图片到存档数据等一切内容的服务。它甚至允许你用它来托管你的网站。随着时间的推移，S3 中的对象数量可以增长到 Pb、EB 或更高的规模，从而导致严重的数据重力。

**日志** —默认情况下，您的日志存储在 AWS 的 [CloudWatch](https://aws.amazon.com/cloudwatch/) 中。跨 Lambda 函数、Step 函数工作流和 API 网关的所有执行、错误和调试日志都整合在一个位置。对于无服务器应用，日志是成功监控和故障排除的关键。随着应用程序的运行，您的日志将会增长，从而产生另一个数据重力源。

**事件**——根据[无服务器设计原则](/7-aws-serverless-design-principles-for-solutions-architects-2be22717713b)，您应该构建您的应用程序来触发来自事件的事务。事件有很多来源，比如 [SNS](https://aws.amazon.com/sns) 、 [SQS](https://aws.amazon.com/sqs) 和 [EventBridge](https://aws.amazon.com/eventbridge) 。在一个[容错应用](/3-ways-to-retry-failures-in-your-serverless-application-84b5102f620)中，您应该存储事件，以便在恢复场景中重放它们。像 EventBridge 这样的服务有一个[事件存档](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-archive-event.html)来做这件事。这产生了您必须考虑的另一个数据重力来源。

# 最小化数据重力的影响范围

如您所见，数据引力在无服务器应用程序中有相当大的影响。关键数据积累在如此多的地方，以至于你觉得不可能从应用程序的所有角落获取数据。

为了在实际打包和更换供应商的情况下最大限度地减少工作量，从*在尽可能少的位置整合数据开始*。为此，您可以将内容聚合到 S3 存储桶中，或者将附加数据存储到数据库中。

对于原木，你可以很容易的[直接出口到 S3](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/S3ExportTasks.html) 。由于您通常不想永远保留日志，您可以通过在存储桶或单个项目上设置[存储生命周期](https://docs.aws.amazon.com/AmazonS3/latest/userguide/how-to-set-lifecycle-configuration-intro.html)来自动归档或删除它们。

事情并不像整固那样直截了当。在整合 EventBridge 之外的某个位置的事件时，您需要亲自动手。备份事件的一个解决方案是配置一个通用规则来捕获所有事件，将它们传递给 Lambda 函数，并让该函数将事件的内容写入 DynamoDB。

```
BackupEventBridgeEventsFunction: 
  Type: AWS::Serverless::Function 
  Properties: 
    CodeUri: functions/backup-eventbridge-events 
    Policies: 
      - AWSLambdaBasicExecutionRole 
      - Version: 2012-10-17 
        Statement: 
          - Effect: Allow 
            Action: 
              - dynamodb:PutItem 
            Resource: !GetAtt EventBackupTable.Arn 
    Events: 
      EventFired: 
        Type: EventBridgeRule 
        Properties: 
          Pattern: 
            account: 
              - !Ref AWS::AccountId
```

这将匹配源自您的 AWS 帐户的所有事件，并将它们发送到`BackupEventBridgeEvents`函数，然后该函数可以将内容以按时间顺序排序的方式写入 Dynamo。

通过将日志备份到 S3，将事件备份到 Dynamo，现在在迁移重要数据时，您只需要考虑两个位置。

# 值得吗？

因为您已经在实践无服务器，所以您已经被您的云供应商锁定了。在灾难发生时，你能够及时成功转换供应商的机会非常小。这就可以了！

当你“全押”在像 AWS 这样的供应商身上时，你得到的一个主要好处是所有的服务都可以很容易地相互集成。您最大限度地发挥了工具的潜力，因为您没有创建变通方法来避免被锁定。

这导致*更快的构建时间，更多的创新，以及最终更低的云成本*。按照预期的方式使用服务，您将构建出最好的应用程序。

最终，我会说“不，不值得”去实施一个*拿起就跑*的策略。现在，如果您遇到真正的灾难，备份您的日志和事件可能会很有用。您需要重放系统中发生的事件，以便可以重建数据并达到您的 RPO。

但在我看来，因为不信任云供应商而实施替代措施是不值得花时间的。

对于独立于供应商的纯粹主义者来说，永远不会出现这样的场景:您真的没有工作可以转移到其他地方。如果您决定在 EC2 实例中托管您自己的数据库，您仍然需要将它转移到其他地方。 [EBS 卷](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volumes.html)是特定于 AWS 的供应商。你不能原封不动地拿起这个卷，然后就在 Azure 中开始使用它。您需要某种备份/转换/恢复流程来成功迁移数据。

# 结论

数据引力是真实存在的。随着时间的推移，你积累的数据越多，就越难转移到其他地方。可以是您的云供应商，也可以是提供实用服务的第三方供应商。

> *一件东西用的时间越长，就越难离开。*

但这是一个好问题。您的开发人员将与云供应商建立专业知识。转移到不同的提供商将迫使你的工程师重新学习事情如何运作的细微差别，这有其自身的成本。

我发现最大的成功来自全力以赴。完全致力于你的解决方案。动摇和怀疑会给你的解决方案带来不稳定性。将你的整个应用构建成一个巨大的提供者模型会增加维护的复杂性和难度。

那么，数据重力是无服务器应用的一个问题吗？*不为那些做得对的人*。

编码快乐！