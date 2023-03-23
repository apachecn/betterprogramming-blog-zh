# 为什么应该使用 Python Pandas 而不是 Vlookups 来合并数据

> 原文：<https://betterprogramming.pub/vlookups-are-cancelled-why-python-pandas-are-a-much-better-way-to-merge-data-dbce0126166a>

## 一个简单的介绍和模板脚本，没有必要的 python 经验。

![](img/94f2dedac9915120c6aa8d5de1976cbc.png)

照片由来自 [Pexels](https://www.pexels.com/photo/female-office-worker-relaxing-with-feet-on-table-3790848/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的[安德里亚·皮亚卡迪奥](https://www.pexels.com/@olly?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

VLookups 很棒。在 excel 中打开一些数据，决定要在另一个表中查找的值，并使用它来合并所需的数据。

现在，您已经在一个表中拥有了所有相关数据，可以对其进行进一步的分析。但是等等，你没有确保你的`vlookup`键是按字母顺序排列的，这扰乱了操作，而且你说的是废话。

您在继续您的项目之前没有检查这一点，现在您有一大堆工作要重做。

将数据合并在一起的简单操作应该不难，对吧？

输入 Python Pandas，最棒的是，你不需要有任何编码或 Python 经验就能有效地使用这个操作。

我非常提倡聪明地工作，而不是努力地工作，我知道我的局限性——当我手动做事时，我会犯错误。

如果您有一个或两个集合要合并，并且您已经在 excel 中工作，Vlookups 可能会很快，但是如果您手动添加的越多，就会很快变得耗时且容易出错。通过创建一个读入源数据集的脚本，您可以将操作设置为合并一次数据，包括您想要的任意多的列，并根据您的感觉反复运行。

假设您有一项日常任务，包括下载一些数据，在 excel 中打开它，使用`vlookup`添加一些信息以使数据更有用，关闭文件，将其附加到电子邮件中，然后发送给您的经理。一旦你非常熟练，你可能至少需要 5 分钟的手工工作，或者一周 25 分钟。不多，但是当你想到一年有 20 个小时，你就会意识到没人有时间做这些。

让我们进入*如何*。

我使用 Spyder(Anaconda 套件的一部分)来编辑我的 python 脚本。它很容易安装，最重要的是它是免费的。

下面是一个虚拟脚本，您只需根据自己的需要进行调整:

```
# import the pandas library and name it pd for short
import pandas as pd# import data, in this case csv
dataset1 = pd.read_csv(r’filepath\filename.csv’)#import lookup table, what you would before have used as your vlookup
lookup = pd.read_excel(r’filepath\filename.xlsx’, sheet_name = ‘sheetname’)# merge on date and location
datasetmerged = pd.merge(dataset1,lookup, left_on = [‘id’], right_on = [‘person_id’], how = ‘left’)## write back to csv (or excel or whatever type you like)
datasetmerged.to_csv(r’filepath\filename.csv’, index = False)# ’cause you are one:
print(“SUCCESS”)
```

我读入我的数据集，随心所欲地命名它们(dataset1 和 lookup)，根据我的匹配`id`合并它们，并输出到我命名为 dataset_merged 的`DataFrame`。

最后，我在自己选择的位置把它写回 excel 中。当您合并`how = ‘left’`时，输出将保留您首先提到的所有文件(`dataset1`)，并合并任何匹配的文件，这就是我们在这里想要的。

如果您需要根据多个匹配项进行合并，例如 id 和日期都需要匹配，请改用以下语法:

```
datasetmerged = pd.merge(dataset1, lookup, left_on = [‘id’, ‘date’], right_on = [‘person_id’, ‘date’], how = ‘left’)
```

更重要的是，随着你对熊猫越来越有信心，你可以开始在同一个文件中编写你的分析脚本。例如，您可以创建透视表和聚合值，删除某些数据以消除噪音，删除不需要的列并输出有吸引力的图表，所有这些都是同一脚本的一部分。

这是一个非常简单的介绍，旨在让您快速使用该功能。有更多的文章更详细地解释了合并函数，如果你想继续追求高效的怠惰，我建议你这样做。

有关合并如何工作以及根据需要调整语法的更多信息，请参见熊猫文档:

 [## 熊猫.合并-熊猫 1.3.5 文档

### 将数据框架或命名的系列对象与数据库样式的连接合并。命名的系列对象被视为数据帧…

pandas.pydata.org](https://pandas.pydata.org/docs/reference/api/pandas.merge.html) [](https://medium.com/@hollydalligan/membership) [## 加入 Medium，我的推荐链接- Holly Dalligan

### 作为一名中型会员，您的部分会员费将支付给您所阅读的作家，您可以完全接触到每个故事……

medium.com](https://medium.com/@hollydalligan/membership)