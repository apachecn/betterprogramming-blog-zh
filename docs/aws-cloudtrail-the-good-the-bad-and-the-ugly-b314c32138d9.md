# AWS cloud trail——好的、坏的和丑陋的

> 原文：<https://betterprogramming.pub/aws-cloudtrail-the-good-the-bad-and-the-ugly-b314c32138d9>

## 轻松地将您的审计跟踪粘在一起

![](img/389f4d99373b38047b702d8082f69d18.png)

[好人](https://en.wikipedia.org/wiki/The_Good,_the_Bad_and_the_Ugly#The_trio)

一个好的审计追踪记录了足够的关于谁、*什么*、*何时*以及*在哪里*的信息。如果这些中的任何一个缺失或缺乏细节，那么将东西粘在一起就成了一场噩梦。我花了相当多的时间分析 AWS cloud trail(CT)。许多云安全状态管理(CSPM)和云基础设施授权管理(CIEM)使用案例都依赖于 CT。我们来看看 AWS CloudTrail 到底有多好！

# 好人

这捕获了事件发生的时间。这是 UTC 格式，格式正确

`awsRegion`:顾名思义，AWS 事件发生的地区。*如果可用区域和 VPC 也被占领，则*将会完成

`recipientAccountId`:用于跨账户资源访问。这对于检测跨账户活动非常方便

`sharedEventID`:如果同一个事件被交付给多个账户(跨账户等)，可用于粘合事件。)

`vpcEndpointId`:用于检测某些原料药是否存在 VPC 终点。例如，通过互联网而不是 VPC 端点访问 S3 存储桶

`tlsDetails`:这有助于识别对非 FIPS 端点、不安全密码或 TLS 版本的调用

`addendum`:如果由于某种原因，电流互感器未交付或需要纠正，则使用该字段。这很好，但是很难找到带有附录的事件来检查格式和测试这一点😕。

# 坏事

![](img/5ceeb61ccfb56d63d231a9128f210b65.png)

[坏人](https://en.wikipedia.org/wiki/The_Good,_the_Bad_and_the_Ugly#The_trio)

AWS 大约有 13000 个动作！动作、API 调用和权限可以互换使用。但它们并不总是相同的。`eventName`捕获进行了哪个 API 调用。但在某些情况下，这并不完全以`action/permission`命名。例如，为 Lambda 服务记录的不是`ListTags`，而是`ListTags20170331`。如果用户/组/角色策略使用 Lambda 权限，请检查您的 CIEM 工具是否建议移除 Lambda 权限。很可能是因为它盲目地将 IAM 策略权限与 CT `eventName`进行比较。

`eventVersion`:CT 记录格式版本。这里没什么可看的。所有值都将是 1.08(当前版本)。理想情况下，这应该在 CT 文件名中。这样调用者就可以知道如何解析 JSON。在每个事件中烘烤这个是没有价值的。我想如果在 CT 配置中启用 CT 日志可能会有用

`eventSource`:请求所针对的服务。格式是`service.amazonaws.com`。理想情况下，`eventSource`中的服务应该匹配 IAM 动作中的前缀。AWS 文档:[这个约定有一些例外。比如亚马逊 CloudWatch 的](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html) `[eventSource](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)` [就是 monitoring.amazonaws.com](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)。CloudWatch 的 IAM 操作前缀是 cloudwatch。我没有找到任何关于 eventSource 的所有可能值和所有异常的文档。还有一个特殊的控制台登录 signin.amazonaws.com！因此，客户需要自己解决这个问题

`sourceIPAddress`:发出请求的 IP 地址。文档中有一些例外。如果这个字段没有过载 DNS 名称或文本 AWS Internal，那就太好了。还有其他捕获源服务的字段，如`userIdentity.invokedBy`。当没有要报告的地址时，AWS 可以忽略这一点。如果源是一个支持网络的资源，如 EC2 实例，AWS 可能已经在这个字段中记录了实例的内部地址

`additionalEventData`:这提供了不属于行动请求或响应的有用信息。但是一些服务决定在 CT 中创建自己的字段，而不是利用想到的`additionalEventData. ec2RoleDelivery`

`readOnly`:AWS 可以添加一个字段来捕获它们在 IAM 中公开的权限组，而不是记录调用是只读还是非只读的布尔字段:`Read`、`List`、`Write`、`Permissions`、`Tagging`

`eventCategory`:捕获事件的类别。管理 vs 数据 vs 洞察力。继续阅读，看看为什么这不是很有用，并可以得到巩固

`managementEvent`:管理事件与否。这里没什么可看的。为什么会被记录下来？这不是已经在`eventCategory`中捕捉到了吗？

`eventType`:AWS 是否可以创建一个字段来替换`eventType`、`managementEvent`、`readOnly`和`eventCategory`？

`eventID` : AWS 文档:[由 CloudTrail 生成的 GUID，用于唯一标识每个事件。您可以使用该值来标识单个事件。例如，您可以使用 ID 作为主键，从可搜索的数据库中检索日志数据。给定一天的 CT 数据，我总能找到重复的条目。如果成批插入联系类型条目，并且将其用作主键，则在某些数据库中成批插入记录会失败](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)

`requestID`:标识请求的值。被调用的服务生成这个值。还没有找到一个好的用途！

`errorCode`:除了检查这是否有价值之外，尝试分析内容并无重大价值。每个 API 以不同的形式报告相同的错误:`AccessDenied`、`NoSuchEntityException`、`Client.UnauthorizedOperation`、`FAILED`等。AWS 和它的客户可以从标准化错误代码中大大受益

`errorMessage` : [一些 AWS 服务提供 errorCode 和 errorMessage 作为事件中的顶级字段。其他 AWS 服务提供错误信息作为 responseElements 的一部分](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)！这说明了一切。

`apiVersion`:标识与`AwsApiCall eventType`关联的 API 版本。它具有任意格式的值:2020_05_31、20140328、2015–08–24 是来自实际 CT 日志的一些示例

`serviceEventDetails`:这真的有必要吗？这会不会被塞进了`additionalEventData`。同样，JSON 格式完全是个谜

# 丑陋的

![](img/6d6773daf7f3d3d25e53c1c3ca2202a4.png)

[丑陋的](https://en.wikipedia.org/wiki/The_Good,_the_Bad_and_the_Ugly#The_trio)

`userAgent`:发出请求的用户代理。在某些情况下，这是 DNS 名称。在某些其他情况下，会报告 AWS 内部 SDK 用户代理。例如，对于某些调用，访问分析器服务报告了以下信息

`aws-internal/3 aws-sdk-java/1.11.1030 Linux/5.4.200-mr.82.metal1.x86_64 OpenJDK_64-Bit_Server_VM/25.212-b03 java/1.8.0_212 vendor/Oracle_Corporation cfg/retry-mode/standard`

这告诉我，访问分析器正在使用三年前的 JDK 和一年半前的 AWS SDK。AWS 可以避免为内部调用记录该字段。DNS 名称是无用的。下一次出现类似`*Log4Shell*`的漏洞时，AWS 会公布哪些服务可以成为攻击者的目标！

这里还有一个陷阱。AWS 文档说:[对于 AWS 发起的事件，这个字段通常是 AWS Internal/#，其中#是用于内部目的的数字](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)。攻击者可以使用以 AWS Internal 开头的自定义用户代理进行呼叫。对于某些用例，一些云安全工具会忽略这些事件，认为它们源自 AWS

`requestParameters`:这是为了捕捉事件的请求输入。这很好，因为它可以用来获取 API 调用访问、创建、更新了哪些资源(如果有的话),等等。但不幸的是，这个字段的结构或内容对于每个动作都是不同的。为了正确解析这个字段，需要理解每个动作的 JSON 格式。祝你好运破译成千上万的 API 的输入格式。在某些情况下，这是完全缺失的。此外，请求的序列化方式因操作而异。在某些情况下，顶级请求对象被捕获。在其他情况下，则不然。在尝试 API 和检查 CT 日志之前，人们不会知道他们将得到什么:

```
{
  "GetManagedPrefixListEntriesRequest": {
    "PrefixListId": "pl-0ced06dee"
  }
}

Or?

{
  "PrefixListId": "pl-0ced06dee"
}
```

`responseElements`:如果 API 返回一个响应，这个字段以 JSON 格式捕获输出。与`requestParameters`类似，根据 API 动作的不同，JSON 格式有数千种变化。如果操作不改变状态(例如，获取或列出对象的请求)，则省略此元素。因此，没有办法确定只读请求访问了哪些资源

`resources`:事件中访问的资源列表。太棒了。但是等等，为什么不是每个 CT 活动都有这个？`resources`很少出现，大多数 API 调用都没有。理想情况下，AWS 可以跳过`responseElements`并记录每个动作访问了哪些资源

*CT 中的谁*大多被`userIdentity`覆盖。它需要自己的深入分析，我将在另一篇文章中介绍。我想提几个额外的想法:

*   服务名称、动作、许可等没有单一的真实来源。在许多情况下，在线文档是不完整的、错误的或过时的。例如，S3 是最古老的 AWS 服务。有一个动作叫[头桶](https://docs.aws.amazon.com/cli/latest/reference/s3api/head-bucket.html)。在 [S3 动作/许可](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazons3.html)文档中找不到它。没有叫`HeadBucket`的权限。进行这个 API 调用需要`ListBucket`权限
*   捕获 API 调用使用/需要哪些权限将非常有帮助。AWS 正在评估权限。记录它们对帮助客户大有帮助。例如，`iam:PassRole`从未出现在`eventName`中，但却是创建数百种资源类型所必需的权限。有 750 多个权限不是操作
*   `resources`领域是伟大的。但是除了 S3 和其他一些服务之外，这种情况很少被记录。如果能把这个分成两个数组就太棒了。一个用于捕获被创建、更新或删除的资源。另一个是记录被访问/读取的资源。这也可能使`requestPareters/responseElements`变得多余
*   文档中的许多字段表明，如果超过 XY 千字节，它们将被截断。当一个 JSON 字段被截断时，应该如何解析它呢？也许这是罕见的。但是攻击者可以利用这些细节来创建逃避解析的事件
*   `sharedEventID`很棒，但是当承担跨帐户角色时，除非源帐户 CloudTrail 可用，否则很难确定执行该操作的用户/角色/资源。两个帐户中记录的详细程度几乎相同，目标帐户中缺少源身份 ID/ARN。也许 AWS 认为这是一个安全问题
*   具有生成凭证的动作，如`AssumeRole`、`AssumeRoleWithSAML`等。，凭证详情埋在`responseElements`里。例如，使用`AssumeRole`，STS 临时凭证到期被记录为 2022 年 10 月 13 日，上午 6:44:41。这个重要的细节不应该以某种奇怪的数据/时间格式记录。还有，临时~重要到不能埋没在`responseElements`里。这些应该是像`issuedAccessKeyId`一样在自己的领域里捕捉到的

而不是添加带有新字段的新格式(1.09？)并将其强加给所有客户，CT 最好遵循 VPC 流日志模型。创建联系类型时，客户可以选择他们想要的格式

# 参考

*   [CloudTrail 记录内容](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html)
*   好的、坏的、丑陋的电影