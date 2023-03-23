# Pyspark — save 与 saveToTable

> 原文：<https://betterprogramming.pub/pyspark-save-vs-savetotable-50cfac32a5e2>

## 一个会让你挠头一分钟的副作用警示故事

![](img/b649537fc261840b44e71e89291a91dd.png)

照片由[塞德里克·范登堡](https://unsplash.com/@cedericvandenberghe?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

最多产的编程迷因可能是这样一个:展示一个筋疲力尽、备受折磨的开发人员，双手抱着头，一些文本表明已经过了无限长的调试时间，却发现这是一个；在某处失踪。

现在，有了现代的 ide，这并不相关，但情绪是存在的，在很多情况下，您会花费比预期多得多的时间来调试某些东西，有时会产生重大影响，这最终是由意外匹配有效关键字的打字错误、错误的缩进、忘记的调试语句或任何本不应该给你带来痛苦的微小而恼人的细节引起的。

我将分享一个类似的例子，其中一个函数的意外副作用让我有点困惑。这就是为什么副作用是错误的，不要在你的代码中这样做，就是不要。

让我来介绍一下场景——我正在用 pyspark 在 Databricks 中开发一个新的 POC 代码，并在一个 Jypither 笔记本上进行实验。在初始阶段，我拉取、处理数据，并用`saveAsTable`持久化数据，因为我使用的集群只是作为一个`test/dev/`实验环境，所以它在使用后会关闭。

在大多数情况下，为了让您的数据对其他服务或人员有用，为了进行分析或查询，需要将数据放入有意义的外部存储中，在本例中就是雪花。幸运的是，从 databricks 向雪花写入数据非常简单:

```
user = dbutils.secrets.get("snowflake", "<user>")
password = dbutils.secrets.get("snowflake", "<password>")df.write \
      .format("snowflake") \
      .option("sfUrl", "<url>") \
      .option("sfUser", user) \
      .option("sfPassword", password) \
      .option("sfWarehouse", "<warehouse>") \
      .option("sfDatabase", "<db>") \
      .option("sfSchema", "<schema>") \
      .option("sfRole", <role>) \
      .option("dbtable", "MY_TABLE") \
      .mode("<mode>") \
      .option("inferSchema","true") \
      .**save**(result_table)
```

现在，注意，保存在那里，嗯，我无意中没有改变它从以前的`saveAsTable`。相反，我只是添加了相关的选项，运行它，等一会儿，它就出现了——命令成功了，正如所预期的，表真的在雪花中创建好了。没有迹象表明这不是我应该保存数据的方式，一点也没有。

于是，我完成了任务，过了几天又开始下一件事。然后，正如您在开发的早期阶段所期望的那样，需要进行更改——重命名、删除、添加列。我在 Snowflake 中直接做了这些，因为当时我没有注意到这不是我的表的唯一实例(在这里插入戏剧性的音乐)。此外，我还必须处理另一批数据(使用新模式)并将其添加到已经存在的表中。

当我再次运行同一组命令时，保存到表的最后一步失败了，因为列数不匹配。确切的错误消息要详细得多，它将列出您试图保存的数据和您试图保存的表中的所有列。这是有趣的部分——它列出了旧的一组列，那些在我在雪花中编辑之前的列，这让我很困惑。好吧，也许有某种本地缓存——尝试了最终的解决方案——关闭再打开。运气不好。

我花了比我想承认的更多的时间，直到我发现我正在使用`saveAsTable`,并且在 Databricks 数据选项卡中，我有了旧模式的相同表。这让我非常生气。为什么该函数会静默地写入两者？如果它对两者都进行了写入，那么它为什么没有发现两者不同步的事实呢？

更重要的是，在官方文件中没有提到这个[。它只讨论保存到指定的表。我不想听起来好像我把所有的责任都推到 pyspark 身上，不，我对我的部分负全部责任。然而，在幕后默默做出改变是违背所有最佳实践的，尤其是那些只工作一次的。如果它同时向两者写入，就这样通告它，检查两者是否同步，并给出合理的错误消息。如果没有，就抛出一个错误。](https://spark.apache.org/docs/3.1.1/api/python/reference/api/pyspark.sql.DataFrameWriter.saveAsTable.html)

无论如何，我希望这对其他人有用。

感谢阅读！