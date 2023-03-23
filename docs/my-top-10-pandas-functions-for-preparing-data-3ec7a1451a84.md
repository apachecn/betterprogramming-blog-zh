# 我的 10 大熊猫函数用于准备数据

> 原文：<https://betterprogramming.pub/my-top-10-pandas-functions-for-preparing-data-3ec7a1451a84>

## 因为我想用尽可能少的工作创建有用、准确的分析

![](img/15c50d1e6fd83b6802bc88fb5dd8aaa6.png)

[真诚媒体](https://unsplash.com/@sincerelymedia?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

遗憾的是，对数据集进行有用的分析很少像将数据下载到您选择的平台、创建一些图表或指标，并使用结果来帮助和通知决策制定这样简单。

数据是杂乱的，它来自多个地方，可能充满噪音或完全缺失。有时我们需要创建新的列，设置标志，或者删除特定的行，然后才能进入下一步。

以下是来自熊猫图书馆的 10 个操作，至少在我写的每一个脚本中都出现了一个，如果不是更多的话。虽然这些操作中的许多都可以在 excel 中完成，但是为什么不让熊猫帮你完成这些艰苦的工作呢？

# **1。将表格合并在一起**

您可能需要连接查找表中的数据，或者根据一些共同的东西(如名称或时间戳)将多个来源合并到一个地方。类似于 excel 中的`vlookup`,但可以在大型数据集上快速完成，用一个命令连接多个列。

```
df3 = pd.merge(df1,df2, on = 'Datapoint', how = 'left')
```

关于开始合并的更多信息，请参考我以前的[文章](https://medium.com/@hollydalligan/vlookups-are-cancelled-why-python-pandas-are-a-much-better-way-to-merge-data-dbce0126166a)。

# **2。堆叠数据帧**

当列名匹配时，垂直堆栈工作得最好。例如，您可能在不同的时间填写了许多 excel 模板，通过将它们连接起来，您可以将它们都放在一个地方，以备查询。

```
vertical_stack = pd.concat([df1, df2], axis=0) 
```

# **3** 。**用零替换空值或 NaNs**

根据您的数据源，空白记录可能显示为 null 或`NaN`(不是数字)，实际上，可能意味着缺少记录，或者可能意味着“没有”或零。重要的是要检查和澄清，以免不正确地歪曲你的结果。

这在一次分析多个数据集时尤其重要，因为不同数据集的定义可能不同。不幸的是，如果你做一些数值运算，比如求和，并且该列至少包含一个 NaN，熊猫就不会玩球。

```
# change null to 0 
df5.loc[df5['column1'].isnull(),   'column1'] = 0# change nan to 0 
df['column1'] = df['column1'].fillna(0)
```

上面的例子用 0 填充，但是你可以用任何你想要的填充。

# 4.删除缺少数据或包含 nan 的行

与其替换丢失的值或 NaN，您可能希望将它们完全删除。

```
df.dropna(axis=0, how="any", inplace=True)
```

语法`how=’any’`删除*中至少有一列*有缺失/ NaN 数据的行。您还可以编辑输入以删除以下行:

-有*所有*列缺失/ NaN
-有*至少 x 个*列缺失/ NaN
-有*特定*列缺失/ NaN

添加`axis = 1`将应用相同的方法，但是应用于列而不是行。一篇包含更多信息的精彩文章可以在[这里](https://towardsdatascience.com/how-to-drop-rows-in-pandas-dataframes-with-nan-values-in-certain-columns-7613ad1a7f25)找到。

# **5。删除前导和尾随空格**

值前或值后的空格会非常烦人，尤其是当你意识到它们时已经太晚了。下面的代码去掉了这两个部分，运行起来很快，如果我打算使用字符串搜索或其他依赖于格式一致性的东西，我会尝试使用并记住作为标准来运行它。

```
df1['State'] = df1['State'].str.strip()
```

# **6。检查缺失的时间戳或行**

信号丢失、错误、文件放错地方都会导致数据丢失。这些行可能仍然存在空值，或者它们可能完全丢失。时间序列数据的一个示例是:如果系统每分钟记录一次，但暂停了 5 分钟，则这 5 分钟可能会显示为时间戳，或者完全消失并显示为时间跳跃。

如果您知道您的输出集应该是什么样子，您可以创建一个虚拟集，然后将您的实际数据加入其中；比较两者，了解什么是现在，什么不是。

这不仅仅适用于时间序列，例如，您可能从数百个不同的资产中收集数据，很难通过肉眼轻易看出是否有任何数据丢失。加入到你已知的资产列表中，将很容易突出任何没有包括在内的资产。

```
time_range = pd.date_range(startdate , enddate, freq='1min')
         ts =   pd.DataFrame(time_range)
         ts.rename(columns = {ts.columns[0]:'timestamp'},   inplace = True)
## now complete a merge to join the sets together
```

# 7。删除重复

```
df.drop_duplicates(subset=['PersonId', 'RecordDate'], keep='last')
```

这个不言自明。将此与排序结合起来，智能地删除某些重复项(例如最早或最新的记录)。

# 8.基于多个条件设置新的列值

```
df.loc[(df.AvgProduction> 1000000) & (df.Age > 5), 'Category'] = 'Priority 1'
```

使用按位运算符(& and |而不是`AND`和`OR`)构建您需要的任何标准。

# 9.基于字符串匹配过滤数据

您可能只对分析特定的资产类型感兴趣，或者从第 8 点开始，您可能已经设定了识别“可能的错误数据”的标准。现在很容易隔离这些记录，以便进一步调查或删除。

```
df1 = df[df[‘Flag’].str.contains(“CHECK ME NOW”)]
```

您可以过滤现有的`DataFrame`，或者将过滤后的数据插入新的【】(如上图)。

# 10。跨列聚合

```
df['StateAverage'] = df_mo[['school1', 'school2','school3', 'school4']].mean(axis=1)
```

平均值、总和、最大值/最小值通常是我们在后面的分析中使用的信息。通常在 excel 中手动完成这一步骤，效果很好。但是，如果这是一个可重复的过程(如月度报告),或者如果它是一个加载到 excel 中很慢或不切实际的大型数据集，那么在 pandas 中这样做尤其有意义。

这是我常用的一些操作，希望对你有用。感谢阅读。

[](https://medium.com/@hollydalligan/membership) [## 通过我的推荐链接加入 Medium-Holly Dalligan

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

medium.com](https://medium.com/@hollydalligan/membership)