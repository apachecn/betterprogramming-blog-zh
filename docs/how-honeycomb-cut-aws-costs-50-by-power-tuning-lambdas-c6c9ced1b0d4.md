# 蜂巢切割 AWS 如何通过功率调谐降低 50%的成本

> 原文：<https://betterprogramming.pub/how-honeycomb-cut-aws-costs-50-by-power-tuning-lambdas-c6c9ced1b0d4>

## 提升了他们的公司文化

![](img/6e00ed35520c1f2fe52dd6ec322093d0.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Ezequiel Junoe](https://unsplash.com/@junoe?utm_source=medium&utm_medium=referral) 拍摄

“对我们(蜂巢)来说，[AWS Lambda]的成本是三件事的函数，”工程经理本·哈特向[在蜂巢博客](https://www.honeycomb.io/blog/aws-costs-service-level/)上写道，描述了他的团队最近的成本削减努力。根据本的说法:

1.  Lambda 的 ARM 架构比 x86 便宜
2.  内存较少的小兰博达比大兰博达便宜
3.  完成得快的兰姆达斯比完成得慢的兰姆达斯便宜

在确定了架构、RAM 大小和持续时间这三个节省成本的变量之后，蜂巢团队需要获取这些数据。他们的目标是识别和划分每个客户和每个查询的 AWS 成本。

该团队装备了 Honeycomb 的查询引擎，将客户特定标识符的遥测数据以及查询源直接发送到 Honeycomb 应用程序，根据用户操作隔离查询。

结果是，Honeycomb 的每个 Lambdas 都记录了它的客户 id、用户操作(例如，手动运行查询或加载仪表板)、配置的内存和关于查询的其他信息。

# 监控大客户和 AWS 异常情况

使用这个相当于黑匣子的飞行记录器，他们计算的成本是持续时间(以毫秒计)乘以每个架构的价格乘以内存大小除以 Lambda 的最大大小(10240GB)。

根据他们的初始数据集，他们确定 arm64 体系结构的每 GB 成本为 1.33e-7 美元，而 x86 体系结构的每 GB 成本为 1.67e-7 美元。将这些数字乘以 RAM 和持续时间等于成本。

他们的计算(使用 Honeycomb 中的“派生列”来自动化事情)不仅有助于跟踪成本，而且他们还确定了发生 Lambda 账单明显高于和低于平均水平的客户。

在实际削减成本之前，该团队做的最后一件事是设置一个 Slack 触发器，每当任何一个“挥金如土”的客户在一天内在 AWS Lambda 上花费超过 100 美元时，该触发器就会自动发出通知。

# 通过功率调谐降低 AWS Lambda 成本

该团队最大的问题是“我们选择的λ大小是否正确，”本写道。为了找到答案，他们使用 [AWS Lambda 功率调谐](https://docs.aws.amazon.com/lambda/latest/operatorguide/profile-functions.html)对大量不同的配置进行实验，然后比较成本。

Ben 文章中的一张图片显示，据我推测，Honeycomb 的 Lambda 成本每月从 10，272.67 美元下降到 5，200.15 美元，这意味着他们的 AWS Lambda 支出下降了近 50%。

> “呜！Lambda 成本降低 50%，同时提供一致的客户体验，这是一大胜利。”
> 
> 本·哈特向

这是一笔可观的节省，全部来自看起来简单的可监控性项目。当他们设置空闲通知时(非常酷！)对于超支，真正的胜利来自跟踪和实验。

# 成本节约对公司文化的影响

除了我确信他们每年为公司节省 60，000 美元(据我估计)后对团队士气的鼓舞之外，这个项目还对 Honeycomb 的公司文化产生了持久的影响。

“让(对 AWS 支出的)影响反映在实际的美元上，”本写道，“更容易(知道)何时[…]优化工作[对我们的开发人员来说]真正值得付出努力。”这就是我所说的管理！

通过严格测量和跟踪他们的 AWS 成本数据，然后系统地调整他们的 Lambda 策略，Honeycomb 不仅节省了成本，还确定了如何最好地利用开发人员时间的客观措施，他们计划继续这样做。

蜂巢团队的每个人都做得很好，我很赞赏他们将成本节约融入他们的文化。我希望这篇文章能激励你削减(或至少跟踪)自己在 AWS Lambdas 上的支出。

编码快乐！

[德里克·奥斯丁](https://www.linkedin.com/in/derek-austin/)博士是《职业规划 [*:如何在 6 个月内成为一名成功的 6 位数程序员*](https://www.amazon.com/dp/B0BRJDLJ43) 的作者，现在亚马逊上有售。