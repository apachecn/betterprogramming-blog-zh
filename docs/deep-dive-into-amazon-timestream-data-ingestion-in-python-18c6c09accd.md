# 深入探究 Amazon Timestream——Python 中的数据摄取

> 原文：<https://betterprogramming.pub/deep-dive-into-amazon-timestream-data-ingestion-in-python-18c6c09accd>

## 如何将数据接收到 AWS 无服务器时间序列数据库中

![](img/cbfbfed2c1a3d563687acba5dab21e94.png)

[玉婷高](https://www.pexels.com/@leofallflat?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)摄于[像素](https://www.pexels.com/photo/silhouette-of-two-persons-stargazing-1567069/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

Timestream 是 AWS 提供的无服务器时序数据库服务。它可用于运营分析、物联网设备监控、金融预测以及更多处理时序数据的用例。有关这项服务的更多背景信息，请查看我以前的文章:

[](https://towardsdatascience.com/amazon-timestream-is-finally-released-is-it-worth-your-time-e6b7eff10867) [## 亚马逊时间流终于发布了——值得你花时间吗？

### AWS 无服务器时间序列数据库终于可以在两年后预览-第一次审查的主要…

towardsdatascience.com](https://towardsdatascience.com/amazon-timestream-is-finally-released-is-it-worth-your-time-e6b7eff10867) 

在本帖中，我们将深入探讨如何将您的数据摄取到时间流中。有几种方法可以做到这一点。我们来看看这三种方法:

1.  通过利用特定编程语言的 AWS SDK，比如 Python 中的`boto3`
2.  通过使用 AWS CLI
3.  借助`awswrangler`包

# 演示数据

作为实时演示数据，让我们使用来自 [Cryptocompare API](https://min-api.cryptocompare.com/) 的加密货币值，如下所示:

上述请求返回一个类似于以下内容的字典:

我们要做的就是提取每种加密货币的价格，如下所示:

然后，我们可以使用下面显示的一种获取方法将这些值获取到 Amazon Timestream 中。

# Boto3

Boto3 区别于:

*   用于摄取和 CRUD 风格操作的**“时间流写入”**客户端，
*   以及用于读取数据和执行 GET 操作的**“时间流-查询”**客户端。

我们可以使用`boto3` timestream-write 客户端创建一个数据库和一个表，以及用于摄取。代码如下:

请注意，上面的命令指定我们希望将新获取的数据仅在内存中保留一个小时，然后在磁存储器中保留一年。Timestream 根据我们指定的保留策略负责在这些存储层之间移动数据。

到目前为止，我们的数据库和表已经配置好了，样本数据已经准备好进行进一步的处理。在接收这些数据之前，我们需要将其转换为适当的格式，包括时间流度量和维度。以下是我们实现这一目标的方法:

为了将这些加密货币的价格加载到 Timestream 中，我们需要在 write 客户机上调用`write_records`方法，并提供上面创建的记录列表。如果接收失败，此方法将返回被拒绝记录的列表。

总之，下面是一个使用 boto3 的完整摄取示例:

# AWS CLI

让我们尝试使用 AWS CLI 复制我们用 boto3 做的一切。来创造资源。我们可以使用以下命令:

**注意**:确保选择一个有时间流的 AWS 区域。

和以前一样，在创建表时，我们需要指定内存和磁存储保持期。

为了使数据适合通过 CLI 接收，我们需要构建一个特定格式的字典。要查看预期的格式，您可以运行以下命令:

这将生成一个框架，我们需要填充它来通过 CLI 用 JSON 有效负载向 Timestream 发送数据。

在上一节中，我们已经构建了一个`records`列表。我们可以使用它为 CLI 接收生成一个 JSON 文件，如下所示:

最后，使用 CLI 接收我们的记录:

# Awswrangler

如果您经常使用`Pandas`数据帧，`awswrangler`会非常有用，因为它为几乎所有 AWS 数据库服务提供了`Pandas dataframe`抽象，包括 Timestream。如果您想将数据从关系数据库或数据湖文件(如 CSV 或 parquet 文件)移动到 Timestream 中，Awswrangler 是最有用的(与 boto3 和 CLI 相比)。

为了展示 awswrangler 在将数据摄入 Timestream 时的实力，这一次，我们将不再使用加密货币演示数据。相反，我们将利用`pandas-datareader`以数据帧的形式获取时序数据。

让我们从安装所需的库开始:

```
pip install awswrangler pandas-datareader
```

我们现在必须像以前一样创建一个数据库和一个表:

现在，为了获得表格格式的演示时间序列数据，让我们使用下面所示的`pandas-datareader`:

如果您想要复制上面的例子，您需要获得一个免费的 [Alpha Vantage API](https://www.alphavantage.co/) 密钥，并设置以下环境变量:

```
export ALPHAVANTAGE_API_KEY=XYZ
```

或者来自 Python:

```
import os
os.environ["ALPHAVANTAGE_API_KEY"] = "XYZ"
```

最后，我们可以一次接收整个数据帧:

# 后续步骤

本文主要关注数据摄取。我们研究了三种将时间序列数据放入时间流的方法。在下面的文章中，我们将看看:

*   如何使用特定于时间序列的 SQL 函数检查和分析这些数据，
*   如何操作接收，以确保我们的数据库每天都能可靠地接收新数据，
*   以及如何用这些数据构建一个接近实时的 Grafana 仪表板。

如果不想错过，可以在这里订阅[。](https://annageller.medium.com/subscribe)

感谢您的阅读！

# 参考资料和其他资源

[1] [AWS 博客](https://aws.amazon.com/blogs/aws/store-and-access-time-series-data-at-any-scale-with-amazon-timestream-now-generally-available/)

[2] [AWS 文档](https://docs.aws.amazon.com/timestream/latest/developerguide/getting-started.python.html)