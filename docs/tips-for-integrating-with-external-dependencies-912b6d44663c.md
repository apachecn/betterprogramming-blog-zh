# 与外部依赖项集成的提示

> 原文：<https://betterprogramming.pub/tips-for-integrating-with-external-dependencies-912b6d44663c>

## 通过询问以下问题来保护您的组件

![](img/cbddb55a0e059d1591cca2d38d8297bd.png)

罗伯特·阿纳奇在 [Unsplash](https://unsplash.com/s/photos/steampunk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

> "韧性是基于对自己的同情以及对他人的同情."——莎伦·萨尔茨堡

让我们看看在将一个组件与一个外部依赖项集成时需要考虑的一些事情。

# 连接安全吗？

当与外部依赖项交换数据时，使用加密的安全协议。采用加密通道作为默认策略将防止任何攻击者恶意访问信息，无论攻击发生在何处，是在本地网络内还是通过互联网。

# 连接是受控的吗？

启动与外部依赖项的连接的组件负责确保这种连接得到正确管理。

*   使用异步和非阻塞模式来处理组件和外部依赖关系之间的交互。例如，使用一个`Promise`来启动一个连接，然后对一个事件作出反应或者捕捉一个异常，这是在不阻塞其他处理的情况下完成的。
*   使用可接受的超时来关闭挂起的连接。
*   在适当的时候采用重试策略。重试一次就足够了。
*   处理来自外部依赖的反压力。使用`Dead Letter Queue`存储失败的事件，并在指定的保留期内重试这些事件，以避免无限期地重试事件。
*   当无法建立与外部依赖项的连接时，采用默认策略。

# 是否处理异常？

将一个组件与外部依赖项集成需要一个错误处理策略，因为事情不可避免地会出错。要么流经的数据将无法处理，要么传输数据的基础架构将经历中断。

区分与系统故障相关的异常和与正在交换的数据相关的异常非常重要。其原因是，异常的性质决定了需要采取什么措施来解决或防止此类事件。

# 错误日志有用吗？

记录错误的目的是支持团队中负责操作相关组件的个人。

因此，错误日志需要以这样一种方式形成，即人们可以快速访问有用的信息，了解错误的影响、原因，并决定行动计划。

错误日志使用某种时间戳来标注日期，比如`2022–05–31T15:11:25.349Z.`

错误日志包括错误代码，可快速帮助识别错误的性质和受影响的组件。

错误代码的建议格式是`[{COMPONENT}-{DEPENDENCY}-{SYSTEM|DATA}]`

例如，下面的代码`[ABC-XYZ-SYSTEM]`告诉我们，组件 ABC 在与依赖 XYZ 通信时遇到了系统问题。

错误代码`[ABC-XYZ-DATA]`告诉我们组件 ABC 正在与依赖 XYZ 交换无效数据。它告知组件中受此错误影响的功能。

错误日志还应该包含确认错误性质的消息，以便确认错误代码导致的初始假设。

例如，这样的错误消息`[ABC-XYZ-DATA] All-But-Crazy failed to submit recipe to Xtra-Yummy-Zinger`用简单的英语确认组件在处理由依赖关系返回的数据时失败。

最后，错误日志应该包括传递给依赖项的输入和从依赖项接收的输出。理想情况下是以易于查询的形式，比如 JSON。

因此，一个有用的错误日志实际上是这样的:

```
2022-05-31T15:11:25.349Z [ABC-XYZ-DATA] All-But-Crazy failed to submit recipe to Xtra-Yummy-Zinger.
Inputs:
{
  "ingredient1": "chocolate",
  "ingredient2": "sand",
  ...
}
Outputs:
{
  "message": "The value for ingredient2 is not supported: 'sand'"
}
```

# 是否监控错误？

监控错误对于了解系统的健康状况至关重要。它有助于显示随着时间的推移，在处理持续的数据流时，随着系统的变化，系统运行状况的降级或改善。

通过监控，可以改进当前已有的错误处理策略，从而不断提高系统的弹性，防止过去的事件再次发生。

这种监控可以通过在错误代码上创建度量来实现，然后在必要时使用这些度量来创建可视化或设置警报。

通过这些精细的洞察来衡量系统的健康状况。调整一下，决定下一步。

# 最后但同样重要的是，你是谁？

识别与外部依赖项集成的组件。这确实是在与第三方进行积极沟通之前最起码要做的事情。

> “嗨，我快疯了。很高兴见到你。”

感谢阅读:)