# 如何用 Python 创建 CSV 文件

> 原文：<https://betterprogramming.pub/how-to-create-a-csv-file-in-python-aff1773b2de1>

## 熊猫来了

![](img/bd8231b9c53bb7a2ce0525e5f5c18f54.png)

照片由[米卡·鲍梅斯特](https://unsplash.com/@mbaumi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

在本文中，您将学习如何用 Python 构建 CSV 文件。这是保存数据的一个很好的方法，您稍后可以对这些数据进行分析。很多文章都是从读取这样的 CSV 文件或者`DataFrame`开始的。

因此，作为一名数据爱好者，您必须知道如何自己构建这样一个文件。它允许您自己试验、更改和创建大型 CSV。这些文件在网上随处可见，使用它们是开始学习数据科学的好方法。

# 安装熊猫

我们将使用一个名为熊猫的图书馆。Pandas 自 2008 年问世，是一款灵活、强大的数据分析和处理工具。对于高达 1GB 的小数据，它非常高效。如果您有一个较大的文件，在移动到一个更复杂的库之前，考虑分块读取它。

```
import pandas as pd
```

# 写入 CSV 文件

在向 CSV 文件写入内容之前，我们必须先创建一个字典。就我个人而言，我喜欢为每个列构造一个大小相等的列表。这样，我们就可以用一种可读性很强的方式来构造字典:

```
data_frame = {'room': rooms, 'swimming pool': pools, 'price': prices} 
```

现在我们有了包含所有数据的字典，让我们继续。有了这个字典，我们实例化一个`DataFrame`:

```
data_frame = pd.DataFrame(data=my_data)
```

`DataFrame`允许我们以适合数据分析的方式呈现数据。它还提供了多种数据过滤方法和其他实用工具。其中一个实用程序是将数据导出到 CSV，这正是我们所需要的！

```
data_frame.to_csv('data.csv')
```

# 结论

就像许多其他事情一样，用 Python 创建 CSV 文件很容易。在本文中，我们已经看到了创建这样一个文件的非常易读的方法。

如果你觉得这很有帮助或者有什么有趣的补充，请在下面的评论中告诉我。