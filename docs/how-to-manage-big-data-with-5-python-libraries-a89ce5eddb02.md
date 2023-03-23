# 如何用 5 个 Python 库管理大数据

> 原文：<https://betterprogramming.pub/how-to-manage-big-data-with-5-python-libraries-a89ce5eddb02>

## Kafka、Hadoop、Spark 等的五个 Hello World 示例

![](img/11a8717200282a75e292b7faa02bac0a.png)

帕特里克·林登伯格在 [Unsplash](https://unsplash.com/s/photos/tech?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在这一点上，Python 确实无处不在。

尽管许多看门人争论说如果一个人不用比 Python 更难的语言编码，他就不是真正的软件开发人员，但它仍然无处不在。

它被用来[自动化](https://www.theseattledataguy.com/4-simple-python-ideas-to-automate-your-workflow/)，[管理网站](https://medium.com/p/e389fff0c8ec)，分析数据，争论大数据。

随着数据的增长，我们管理数据的方式变得越来越精细。

我们不再局限于只使用关系数据库。

这也意味着现在有更多的工具可以与这些新系统交互，如 Kafka、Hadoop(更具体地说是 HBase)、Spark、BigQuery 和 Redshift(仅举几例)。

这些系统中的每一个都利用了像分布、柱状架构和流数据这样的概念来更快地向最终用户提供信息。

对更快、更新信息的需求将推动数据工程师和软件工程师利用这些工具。

这就是为什么我们想提供一些 Python 库的快速介绍来帮助你。

# BigQuery

Google BigQuery 是一个非常受欢迎的企业仓库，它结合了 Google 云平台和 Bigtable。

这项云服务适用于各种规模的数据，并能在几秒钟内执行复杂的查询。

BigQuery 是一个 RESTful web 服务，使开发人员能够结合 Google 云平台对庞大的数据集进行交互式分析。让我们来看看我在[的另一篇文章](https://medium.com/better-programming/analyzing-healthcare-data-with-saturncloud-io-and-bigquery-a9fdac3c0de4)中整理的一个例子。

这个例子向您展示了如何连接到 BigQuery，然后开始提取关于您将与之交互的表和数据集的信息。

在这种情况下，医疗保险数据集是任何人都可以访问的开源数据集。

关于 BigQuery 的另一点是它对 Bigtable 进行操作。理解这个仓库不是一个事务数据库是很重要的。因此，不能将其视为在线事务处理(OLTP)数据库。它是专为大数据设计的。因此，它的工作与处理 Pb 大小的数据集一致。

# 红移，有时还有 S3

接下来，我们有亚马逊流行的红移和 S3。亚马逊 S3 基本上是一种存储服务，用于存储和检索互联网上任何地方的大量数据。使用此服务，您只需为您实际使用的存储空间付费。另一方面，Redshift 是一个完全托管的数据仓库，可以高效地处理 Pb 级的数据。该服务使用 SQL 和 BI 工具提供更快的查询。

亚马逊红移和 S3 一起为数据工作，这是一个强大的组合:使用 S3 可以将大量数据注入红移仓库。这个强大的工具，当用 Python 编码时，对开发人员来说变得非常方便。我们来看一个简单的“你好，世界！”供参考的例子。

这个脚本是一个基本的连接。使用`psycopg2`选择。

在这种情况下，我借用了[jaycoo](https://gist.github.com/jaychoo)的代码。

但是，这又一次提供了一个关于如何连接红移然后从红移中提取数据的快速指南。

# PySpark

好了，让我们离开数据存储系统的世界，让我们来看看帮助你快速处理数据的工具。

Apache Spark 是一个非常流行的开源框架，可以执行大规模分布式数据处理。也可以用于机器学习。

这个集群计算框架主要关注简化分析。它与弹性分布式数据集(rdd)一起工作，并允许用户处理 spark 集群的托管资源。

它经常与其他 Apache 产品(如 HBase)结合使用。Spark 将快速处理数据，然后将其存储到其他数据存储系统的表集中。

为了开始，让我们看一个运行火花的基本例子。

有时安装 PySpark 可能是一个挑战，因为它需要依赖项。您可以看到它运行在 JVM 之上，因此需要 Java 的底层基础设施才能运行。但是，在这个 Docker 盛行的时代，用 PySpark 做实验就变得方便多了。

阿里巴巴使用 PySpark 来个性化网页并提供目标广告——许多其他大型数据驱动型组织也是如此

# 卡夫卡巨蟒

Kafka 是一个分布式发布-订阅消息传递系统，允许用户在复制和分区主题中维护消息提要。

这些主题基本上是从客户机接收数据并跨分区存储数据的日志。Kafka Python 被设计为一个集成了 Python 接口的官方 Java 客户端。它最好与新的代理一起使用，并向后兼容所有旧版本。

## *卡夫卡消费者*

## *KafkaProducer*

正如你在上面的例子中看到的，用 Kafka Python 编码需要引用消费者和生产者。

在 Kafka Python 中，我们让这两个方面并肩工作。`KafkaConsumer`基本上是一个高级消息消费者，打算作为官方的 Java 客户端来操作。

它要求代理支持组 API。`KafkaProducer`是一个异步消息生成器，它的操作方式也非常类似于 Java 客户端。生产者可以毫无问题地跨线程使用，而消费者需要多处理。

# 皮杜普

让我们把这个弄清楚。不，Hadoop 本身不是一个数据存储系统。Hadoop 实际上有几个组件，包括 MapReduce 和 Hadoop 分布式文件系统(HDFS)。

因此 Pydoop 在这个列表中，但是您需要将 Hadoop 与其他层(如 Hive)配对，以便更容易地处理数据。

Pydoop 是一个 Hadoop-Python 接口，允许您与 HDFS API 交互，并使用纯 Python 代码编写 MapReduce 作业。

这个库允许开发者访问重要的 MapReduce 函数，比如`RecordReader`和`Partitioner`，而不需要了解 Java。对于最后一个例子，我认为 Edureka 的人比我做得更好。这里有一个很棒的快速介绍。

Pydoop 本身对于大多数数据工程师来说可能有点太低级了。更有可能的是，你们中的大多数人将在这些系统之上运行的 Airflow 中编写 ETL。但是至少对你正在做的事情有一个大致的了解还是很好的。

# 那么你会从哪里开始呢？

未来几年，管理大数据只会变得更加困难。

由于日益增强的网络能力—物联网、改进的计算等。—向我们涌来的海量数据只会继续增长。

因此，如果我们要跟上时代的步伐，就必须了解一些数据系统和可以用来与这些系统交互的库。

感谢阅读。