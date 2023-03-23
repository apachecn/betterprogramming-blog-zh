# 用 Python 和 pandas 清理数据

> 原文：<https://betterprogramming.pub/data-cleaning-with-python-pandas-an-introduction-1cfd5cde6884>

## 介绍

![](img/9278b9fbcbd4a8bd0fb070064da5aed4.png)

由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/data?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

你可能听说过数据科学家花费大约 80%的时间清理数据。

清理数据很重要，因为脏数据会导致脏分析和脏预测。

这些肮脏的数据可能会被用来做出预测，最终导致公司或个人损失大量资金，并可能导致一些人失业。

通过努力确保您正在处理的数据是干净的和高质量的，可以避免一场噩梦。

今天，我们将用 [pandas](https://pandas.pydata.org/) 来做这件事，这是一个用于数据操作和分析的 Python 库。

如果你使用过 Excel 或者任何电子表格工具，这与使用熊猫数据框架很相似，除了你编写 Python 代码来进行计算和操作。

也就是说，你不需要成为一个 Python 专家来和熊猫一起工作！

# 在这篇文章中

*   项目设置-收集数据并创建熊猫数据框架。
*   用熊猫做数据分析。
*   数据质量检查。
*   缺少值。
*   熊猫`apply()`。
*   [NumPy](https://numpy.org/) `where()`。
*   正常化。
*   自然语言处理的文本规范化。

# 数据是如何变脏的？

*   缺失数据。
*   数据不一致。
*   重复数据。

举几个可能出错的例子。数据变得非常混乱的方式数不胜数。

有时，在最初输入数据时，验证检查不够充分。

如果用户在表单域中以任意格式输入数据，但没有适当的准则或表单验证检查来强制符合特定格式，那么用户将按照他们认为合适的方式输入。

可能有一个州(美国)的输入字段，您有一些数据是两个字符的缩写， *NY* 然后其他的有 *New York* ，然后有潜在的拼写错误和打字错误…

数据也可能在传输或存储过程中遭到破坏。

# 用 Python 和 pandas 清理数据

我们将使用 [pandas](https://pandas.pydata.org/) 来检查和清理来自[纽约市建筑部(DOB)](https://www1.nyc.gov/site/buildings/index.page) 的建筑违规数据集，该数据集可在纽约市开放数据上获得。

这里是[数据集](https://data.cityofnewyork.us/Housing-Development/DOB-Violations/3h2n-5cm9)。

在这个演示中，我将使用数据的一个子集——从 API 中检索的 10，000 条记录。

我有另一篇关于从 API 访问纽约开放数据和获取 API 密钥的文章。

# 设置

*   为这个项目创建一个虚拟环境[。](https://virtualenv.pypa.io/en/latest/)
*   安装 [Python-requests](https://requests.readthedocs.io/en/master/) 和熊猫。
*   从纽约市开放数据 API 中检索 2019 年 10，000 条违规数据记录。
*   用我们将在后面的文章中用到的数据创建一个熊猫数据框架。

```
mkvirtualenv cleandata pip install requests 
pip install pandas
```

把这两个都和 NumPy 一起导入，NumPy 和 pandas 一起安装，我们也将使用它。

注意:将 API 键放在脚本之外是个好主意。使用环境变量或其他工具来管理敏感信息。

所以我从 API 中抓取了数据，并用它创建了一个 pandas [DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) `df`，我们将在本文的剩余部分使用它。

# 分析数据

让我们看看我们在处理什么。

```
In [1]: df.shape 
Out[1]: (10000, 18)
```

这个数据集中有 10，000 条记录。

# 看看前几行

我们可以通过调用 DataFrame 上的`head()`来查看前几行。

```
In [3]: df.head()                                                                                                                                                     
Out[3]: 
       bin  block boro         ...         violation_number                                     violation_type violation_type_code
0  3059563  02136    3         ...                    00339  JVCAT5-RESIDENTIAL ELEVATOR PERIODIC INSPECTIO...              JVCAT5
1  3215627  07777    3         ...                    00951                    AEUHAZ1-FAIL TO CERTIFY CLASS 1             AEUHAZ1
2  3137310  05631    3         ...                 NRF01042                                  LL2604S-SPRINKLER             LL2604S
3  2003035  02504    2         ...                    00162                    AEUHAZ1-FAIL TO CERTIFY CLASS 1             AEUHAZ1
4  1001389  00113    1         ...                    00284  ACC1-(OTHER BLDGS TYPES) - ELEVATOR AFFIRMATIO...                ACC1

[5 rows x 18 columns]
```

该数据集中有 18 列。

# 列/功能名称

让我们看看列名。

```
In [4]: df.columns
Out[4]: 
Index(['bin', 'block', 'boro', 'description', 'device_number',
       'disposition_comments', 'disposition_date', 'ecb_number',
       'house_number', 'isn_dob_bis_viol', 'issue_date', 'lot', 'number',
       'street', 'violation_category', 'violation_number', 'violation_type',
       'violation_type_code'],
      dtype='object')
```

`bin`栏代表*建筑识别号*，是纽约市 DOB 用于建筑的唯一标识符。

# 缺少/空值

调用`isnull()`和`sum()`来计算每一列中有多少空值。

```
In [5]: df.isnull().sum()
Out[5]: 
bin                        0
block                      2
boro                       0
description             2446
device_number           3772
disposition_comments    7349
disposition_date        7358
ecb_number              8093
house_number               0
isn_dob_bis_viol           0
issue_date                 0
lot                        2
number                     0
street                     0
violation_category         0
violation_number           0
violation_type             0
violation_type_code        0
dtype: int64
```

我们将在后面的文章中更多地讨论缺失值。

# 删除不相关的列

我主要感兴趣的是查看每个建筑收到的违规类型，以及它们是已经关闭还是仍然开放，因此我将从一开始就删除一些列，这些列只是关于建筑位置和 DOB 的管理数据的更多信息。

```
In [6]: columns_to_delete = ['block', 'boro','house_number','lot','street','violation_number', 'device_number', 'disposition_comments', 'isn_dob_bis_viol', 'disposition_date','ecb_number','description'] 
In [7]: df.drop(columns_to_delete, inplace=True, axis=1)
```

在这里，我列出了我想要删除的列，然后将其传递给`df.drop()`。

许多 pandas 功能可以就地运行*。*

*在 pandas 中，轴 0 代表行，轴 1 代表列，所以我用`axis=1`表示，因为我要删除列。*

*很好，现在让我们看看哪些列还在数据框中。*

```
*In [8]: df.columns
Out[8]: 
Index(['bin', 'issue_date', 'number', 'violation_category', 'violation_type',
       'violation_type_code'],
      dtype='object')*
```

*好了，我们已经检查了数据，并对其进行了一些删减，现在是时候查看每一列了。*

# *描述统计学*

*您可以调用`describe()`来获得数据帧中每一列的摘要。*

```
*In [9]: df.describe()                                                                       
Out[9]: 
            bin           issue_date                number        violation_category violation_type violation_type_code
count     10000                10000                 10000                     10000          10000               10000
unique     8545                  327                  9996                         9             27                  27
top     3397861  2019-11-08 00:00:00  V*030619AEUHAZ100139  V-DOB VIOLATION - ACTIVE     E-ELEVATOR                   E
freq          8                 1554                     2                      7330           2214                2214
first       NaN  2019-01-01 00:00:00                   NaN                       NaN            NaN                 NaN
last        NaN  2019-12-31 00:00:00                   NaN                       NaN            NaN                 NaN*
```

*对于数字数据，您还可以获得平均值、中值、标准偏差和百分位数。*

*`top`值是该列中最常见的值，例如，在这里，ID 号为 3397861 的建筑物似乎在该数据集中拥有最多的条目。*

*在[文档](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.describe.html)中阅读更多关于`describe()`的信息。*

# *数据质量*

*接下来的几个部分将涵盖数据质量检查。*

*   *数据类型*
*   *限制*
*   *同样*
*   *准确(性)*
*   *数据一致性*
*   *复制*

*在维基百科上阅读更多关于数据质量的信息。*

# *数据类型*

*每一列中的数据值都是您期望的正确数据类型，这一点很重要。*

*例如，如果数据应该是数字格式，但您在列中找到了字符串数据。*

*稍后，当你进行数字计算时，你可能会得到令人惊讶的结果，当你认为你在把两个数字相加，但它们是字符串，所以它们被连接起来。*

*属性`dtypes`将显示数据帧中每一列的数据类型。*

```
*In [10]: df.dtypes
Out[10]: 
bin                    object
issue_date             object
number                 object
violation_category     object
violation_type         object
violation_type_code    object
dtype: object*
```

*pandas 对象数据类型可以是混合类型，有字符串和数字数据，但 pandas 会将其视为字符串。*

*对于这个数据集，我主要关心的是`issue_date`列。*

```
*In [11]: df['issue_date'].head()                                                             
Out[11]: 
0    20190520
1    20190516
2    20190903
3    20190904
4    20190906
Name: issue_date, dtype: object*
```

*日期是 YYYYMMDD 格式的字符串。*

*我想把它转换成一个 [Python datetime](https://docs.python.org/3.6/library/datetime.html) ，pandas 用`to_datetime()`方法有一个简单的方法——阅读[文档](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.to_datetime.html)了解更多。*

```
*In [12]: df['issue_date'] = pd.to_datetime(df['issue_date'])  
In [13]: df['issue_date'].head()                                                             
Out[13]: 
0   2019-05-20
1   2019-05-16
2   2019-09-03
3   2019-09-04
4   2019-09-06
Name: issue_date, dtype: datetime64[ns]*
```

*Pandas 很聪明，通常不用你指定它就能计算出日期格式。*

*我没有对其他列进行任何数值计算，所以我将它们保留为对象类型。*

*我可以将`bin`列转换成整数格式，但是我没有对它进行任何数值计算，并且在数据集的 [API 文档](https://dev.socrata.com/foundry/data.cityofnewyork.us/3h2n-5cm9)中，它是一个文本字段。*

*其余的大多是字母数字或字符串文本。*

# *数据约束*

*确保尊重数据上的任何约束。*

*对于给定的数据集，您可能需要遵循许多类型的约束，在清理数据集时，您会希望确保测试任何相关的约束。*

*   *日期范围，*
*   *数字范围—列的最小值或最大值。*
*   *非空约束-如果值不应为空，但为。*
*   *唯一约束-两个或更多列值在数据中必须是唯一的。*
*   *外键约束。*
*   *模式—如果数据应该是电子邮件地址或电话号码的格式，可以使用这些模式的正则表达式进行验证。*
*   *如果您正在处理经度和纬度等地理数据，并且知道数据点应该位于何处，则可以为可接受的纬度和经度坐标设置边界，如果数据点位于边界之外，则需要进一步检查。*

*…以及更多。*

# *`issue_date`栏中的测试日期*

*建筑违章数据应该都是 2019 年的日期。*

*我已经将该列转换为 Python datetime 格式，因此我可以通过创建一个布尔掩码来测试日期范围是否正确——为该列中的每一项创建一个由`True`或`False`值组成的数组——然后确保返回`True`的所有行加起来达到我期望的行数。*

```
*In [14]: mask = (df['issue_date'] > '2018-12-31') & (df['issue_date'] <= '2019-12-31') 
In [15]: df.loc[mask].shape Out[15]: (10000, 18)*
```

*10000 行都是 2019 年的，所以它通过了测试！*

# *同样*

*数据集中的值应该是正确的数据类型，并且它们在*单位*中也应该是一致的。*

*如果一列中的一些样品以英里/小时为单位，而其他样品以公里/小时为单位，那么任何分析都将基于部分不正确的数据。*

*您需要确保数据被转换成相同的单位。*

# *数据准确性*

*检查数据的准确性可以与我们谈到的约束紧密联系在一起。*

*用户可能以“000–000–0000”的格式输入美国电话号码，这是正确的格式，可以传递任何类型的正则表达式，但它可能不是真实的电话号码。*

*如果没有可靠的资源来比较数据，则很难检查数据的准确性。*

*例如，您可以使用一些 API 来验证美国邮政地址。*

# *一致性*

*跨数据源的数据应该是一致的。*

*如果您有关于零售产品的数据，并且在两个不同的表中价格不同，这可能是一个问题。*

*应调查同一实体的冲突值。*

# *复制*

*如果用户不小心提交了两次表单，或者您在没有意识到的情况下抓取了两次网站，或者在许多其他情况下，您可能会得到重复的数据。*

## *熊猫`drop_duplicates()`*

*您可能拥有重复的整行，或者当一列应该是唯一的时，在该列中只有重复的值。*

*熊猫中的`drop_duplicates()`功能在这两种情况下都可以使用。*

*要么整行都是重复的，要么您可以使用`subset`参数指定要检查重复的列，该参数采用列名列表。*

*`keep`参数指定是否保留任何副本。默认是先有*后有*，所以它会保留第一个副本并丢弃其余的副本。*

**最后一个*将删除除最后一次出现之外的所有重复项。*假*会把*全部*掉。*

```
*df.drop_duplicates(inplace=True)*
```

*还有就地运行它的选项。*

*在[文档](https://pandas.pydata.org/pandas-docs/version/0.17/generated/pandas.DataFrame.drop_duplicates.html)中阅读更多信息。*

# *缺少值*

*让我们再看一下数据集中空值的汇总，只是现在我们有了更少的列。*

```
*In [16]: df.isnull().sum()                                                         
Out[16]: 
bin                    0
issue_date             0
number                 0
violation_category     0
violation_type         0
violation_type_code    0
dtype: int64*
```

*在这种情况下没有丢失值。*

*熊猫意识到一些东西缺少价值。*

*   *空单元格*
*   *“那”*
*   *如果数据的类型不适合列，例如，数字列中的字符串数据。*

## *丢失的值怎么办？*

*这可能是一个单独的职位，所以我只会总结。*

***首先，尝试找出数据丢失的原因***

*有了建筑违规数据，似乎有理由认为违规应该总是有一个`issue_date`，所以如果它丢失了，我会认为它可能有一个值，但它没有被正确记录，或者类似的事情。*

*另一方面，像`disposition_comments`(这是我之前删除的列之一，但我只是用它作为例子)这样的其他列可能没有输入注释，或者还没有输入，或者注释是可选的。所以它可能有一个空值。*

*阅读数据集的文档，尝试找出丢失数据的原因。*

***删除或替换***

*当数据集有缺失值或空值时，决定在项目环境中如何处理它们是很重要的。*

*有两个基本选项。*

1.  *删除缺少值的行或列。*
2.  *替换丢失的值。*

# *删除*

*您可以使用`dropna()`删除 pandas 中缺失/空值。*

## *列*

*如果您想删除任何缺少值的列。*

```
*df.dropna(inplace=True,axis=1)*
```

*或者删除有一定数量的缺失值的所有列。*

*您可以选择一个阈值并使用参数`thresh` —该阈值是您希望该列具有的*非* -null 值的数量。*

```
*df.dropna(thresh=int(df.shape[0] * .9), axis=1)*
```

*这将删除任何非 -NA 值小于 90%的列。*

## *行*

*另一个选项是删除行。您可以选择删除任何在*任意*列中缺少值的行。或者您可以删除列的子集中缺少值的行。*

*如果我想分析每栋建筑的违规情况，我可以删除没有建筑标识号的行。*

```
*df.dropna(subset=['bin'],inplace=True)*
```

*`subset`参数允许您指定列的列表。[单据](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.dropna.html)。*

# *替换值*

## *熊猫`fillna()`*

*调用数据帧上的`fillna()`来填充缺失值。*

*如果你想用一个零来填充所有缺失的值。*

```
*df.fillna(0)*
```

*或者缺失值也可以通过传播同一列中它之前或之后的值来填充。[单据](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.fillna.html#pandas.DataFrame.fillna)。*

*反向传播价值观。*

```
*df.fillna(method='bfill')*
```

*向前传播价值观。*

```
*df.fillna(method='ffill')*
```

*有许多方法可以确定用什么来替换丢失的数据。*

*   *中位数*
*   *平均*
*   *定值*
*   *回归预测正确值*
*   *随机回归*
*   *热卡插补-随机选取另一个在其他变量上具有相似值的行，并使用其值。*

# *用熊猫清理数据`apply()`*

*接下来，让我们清理一下`violation_category`栏。*

```
*In [9]: df['violation_category'].unique()
Out[9]: 
array(['V*-DOB VIOLATION - Resolved',
       'VW*-VIOLATION - WORK W/O PERMIT DISMISSED',
       'V*-DOB VIOLATION - DISMISSED', 'V-DOB VIOLATION - ACTIVE',
       'VP*-VIOLATION UNSERVED ECB- DISMISSED',
       'VW-VIOLATION WORK WITHOUT PERMIT - ACTIVE',
       'VPW*-VIOLATION UNSERVED ECB-WORK WITHOUT PERMIT-DISMISSED',
       'VH-VIOLATION HAZARDOUS - ACTIVE',
       'VH*-VIOLATION HAZARDOUS DISMISSED', nan,
       'VP-VIOLATION UNSERVED ECB-ACTIVE', 'V%-UB VIOLATION',
       'VPW-VIOLATION UNSERVED ECB-WORK WITHOUT PERMIT-ACTIVE'],
      dtype=object)*
```

*这些分类来自这个网站上的[表格。](https://www1.nyc.gov/site/buildings/business/types-of-dob-violations.page)*

*这些字符串中最能提供信息的部分是违规代码，它是字符串的第一部分，在'-'之前: *VW** 、 *V** 、 *V* 等。*

*如果你看链接表，代码后面的部分只是一串代码的意思。 *VW* 代码用于无许可证违章作业。*

*看看这两个违规类别。*

```
*'V*-DOB VIOLATION - Resolved', 
'V*-DOB VIOLATION - DISMISSED'*
```

*一个写着“已解决”，一个写着“驳回”，但他们的违规代码都是一样的， *V** 。*

*这是一个数据输入时缺乏验证的例子。*

*根据链接表，代码 *V** 意味着违规被驳回，所以有人可能只是写了“已解决”,因为没有标准的数据输入方式。*

*它看起来好像有比实际更多的违规类别，但这两个类别实际上代表的是已被驳回的同一 DOB 违规类别。*

*另一个非标准化格式的例子是，单词“without”在其中的几个中有不同的写法。*

```
*'VW*-VIOLATION - WORK W/O PERMIT DISMISSED', 
'VW-VIOLATION WORK WITHOUT PERMIT - ACTIVE'*
```

*这两个代表不同的类别——*VW **和 *VW* ，但我只是把这种事情作为一个标志，以便更仔细地检查数据。*

*我将通过用上面链接中的违规类别字符串数据创建一个代码字典`dob_violation_codes`来清理这个问题，并从列中的每个条目中提取违规代码，并用我的字符串替换它。*

*这将使列中的值标准化。*

*你可以在这个要点里找到整本字典[。](https://gist.github.com/lvngd/594f77fafa7181ff0456aff239bcf11a)*

*该函数通过提取违例代码子串并在字典中查找描述来替换`df['violation_category']`中的值。*

*如果在字典中找不到代码，它就返回原来的值。*

*现在调用`df.apply()`并传递给它`replace_value`函数。*

```
*In [10]: df['violation_category'] = df.apply(replace_value,axis=1)*
```

*列的缺省值是`axis=0`，但是我们希望对每行都应用这个函数。*

*现在看看这一列中的唯一值。*

```
*In [11]: df['violation_category'].unique()                                                                                                                    
Out[11]: 
array(['V-DOB Violation', 'V*-DOB Violation Dismissed',
       'VP-Violation Unserved ECB-Active',
       'VP*-Violation Unserved ECB-Dismissed',
       'VPW-Violation Pending - Work Without a Permit',
       'VPW*-Violation Pending - Work Without a Permit Dismissed',
       'V%-Precept issued for Unsafe Buildings Violation',
       'VW-Violation - Work Without a Permit'], dtype=object)*
```

*它看起来更整洁，并且我们对每个类别代码都有一个类别。*

*我希望这有意义——如果有任何问题，请告诉我！*

# *用 NumPy 清理文本数据`where()`*

*接下来，我们来看看`violation_type`栏。*

```
*In [12]: df['violation_type'].head(10) 
Out[12]: 
0                                           E-ELEVATOR
1                                       C-CONSTRUCTION
2                                           E-ELEVATOR
3                                           E-ELEVATOR
4                    LL1081-LOCAL LAW 10/81 - ELEVATOR
5                                           E-ELEVATOR
6                     LL6291-LOCAL LAW 62/91 - BOILERS
7    ACC1-(OTHER BLDGS TYPES) - ELEVATOR AFFIRMATIO...
8                                           E-ELEVATOR
9                                           E-ELEVATOR
Name: violation_type, dtype: object*
```

*调用`violation_type`列上的`unique()`来查看唯一值。*

```
*In [13]: df['violation_type'].unique()
Out[13]: 
array(['E-ELEVATOR', 'C-CONSTRUCTION',
       'LL1081-LOCAL LAW 10/81 - ELEVATOR',
       'LL6291-LOCAL LAW 62/91 - BOILERS',
       'ACC1-(OTHER BLDGS TYPES) - ELEVATOR AFFIRMATION OF CORRECTION',
       'LBLVIO-LOW PRESSURE BOILER', 'AEUHAZ1-FAIL TO CERTIFY CLASS 1',
       'EVCAT1-ELEVATOR ANNUAL INSPECTION / TEST',
       'FISPNRF-NO REPORT AND / OR LATE FILING (FACADE)',
       'JVCAT5-RESIDENTIAL ELEVATOR PERIODIC INSPECTION/TEST',
       'VCAT1-ELEVATOR ANNUAL INSPECTION / TEST',
       'FISP-FACADE SAFETY PROGRAM', 'HBLVIO-HIGH PRESSURE BOILER',
       'LL2604S-SPRINKLER', 'LANDMK-LANDMARK', 'UB-UNSAFE BUILDINGS',
       'PA-PUBLIC ASSEMBLY', 'B-BOILER', 'LANDMRK-LANDMARK',
       'EVCAT5-NON-RESIDENTIAL ELEVATOR PERIODIC INSPECTION/TEST',
       'RWNRF-RETAINING WALL', 'LL1198-LOCAL LAW 11/98 - FACADE',
       'ES-ELECTRIC SIGNS', 'LL1080-LOCAL LAW 10/80 - FACADE',
       'P-PLUMBING', 'BENCH-FAILURE TO BENCHMARK',
       'LL11/98-LOCAL LAW 11/98 - FACADE',
       'L1198-LOCAL LAW 11/98 - FACADE', 'EARCX-FAILURE TO SUBMIT EER',
       'JVIOS-PRIVATE RESIDENTIAL ELEVATOR', 'CMQ-MARQUEE',
       'LL10/80-LOCAL LAW 10/80 - FACADE', 'LL5-LOCAL LAW 5/73',
       'IMEGNCY-IMMEDIATE EMERGENCY', 'LL2604E-EMERGENCY POWER',
       'Z-ZONING', 'EGNCY-EMERGENCY',
       'HVIOS-NYCHA ELEV ANNUAL INSPECTION/TEST',
       'ACH1-(NYCHA) - ELEVATOR AFFIRMATION OF CORRECTION',
       'ACJ1-(PRIVATE RESIDENCE) - ELEVATOR AFFIRMATION OF CORRECTION',
       'CS-SITE SAFETY', 'LL10/81-LOCAL LAW 10/81 - ELEVATOR',
       'LL2604-PHOTOLUMINESCENT', 'A-SEU', 'CLOS-PADLOCK',
       'LL16-LOCAL LAW 16/84 - ELEVATOR',
       'HVCAT5-NYCHA ELEVATOR PERIODIC INSPECTION/TEST'], dtype=object)*
```

*目前还不清楚其中的一些是什么，但其中许多都与电梯、锅炉、建筑违规、管道等类似的事情有关。*

*假设我只是想从总体上看一下最常见的违规类型。*

*我们可以看看每一个的价值计数。*

```
*In [27]: df['violation_type'].value_counts() 
Out[27]: 
E-ELEVATOR                                                       3202
LL6291-LOCAL LAW 62/91 - BOILERS                                 2748
LBLVIO-LOW PRESSURE BOILER                                        832
C-CONSTRUCTION                                                    819
AEUHAZ1-FAIL TO CERTIFY CLASS 1                                   559
LL1081-LOCAL LAW 10/81 - ELEVATOR                                 374
ACC1-(OTHER BLDGS TYPES) - ELEVATOR AFFIRMATION OF CORRECTION     305
EVCAT1-ELEVATOR ANNUAL INSPECTION / TEST                          235
BENCH-FAILURE TO BENCHMARK                                        120
LANDMK-LANDMARK                                                   105
UB-UNSAFE BUILDINGS                                                68
P-PLUMBING                                                         56
ES-ELECTRIC SIGNS                                                  51
FISPNRF-NO REPORT AND / OR LATE FILING (FACADE)                    47
EVCAT5-NON-RESIDENTIAL ELEVATOR PERIODIC INSPECTION/TEST           40
Z-ZONING                                                           33
EARCX-FAILURE TO SUBMIT EER                                        32
JVIOS-PRIVATE RESIDENTIAL ELEVATOR                                 32
B-BOILER                                                           31
L1198-LOCAL LAW 11/98 - FACADE                                     31
LL1198-LOCAL LAW 11/98 - FACADE                                    28
VCAT1-ELEVATOR ANNUAL INSPECTION / TEST                            27
IMEGNCY-IMMEDIATE EMERGENCY                                        24
CMQ-MARQUEE                                                        23
EGNCY-EMERGENCY                                                    22
LL11/98-LOCAL LAW 11/98 - FACADE                                   20
LANDMRK-LANDMARK                                                   18
FISP-FACADE SAFETY PROGRAM                                         16
LL1080-LOCAL LAW 10/80 - FACADE                                    14
LL10/80-LOCAL LAW 10/80 - FACADE                                   11
HBLVIO-HIGH PRESSURE BOILER                                        10
PA-PUBLIC ASSEMBLY                                                  9
ACH1-(NYCHA) - ELEVATOR AFFIRMATION OF CORRECTION                   9
RWNRF-RETAINING WALL                                                9
LL2604S-SPRINKLER                                                   6
LL2604E-EMERGENCY POWER                                             5
JVCAT5-RESIDENTIAL ELEVATOR PERIODIC INSPECTION/TEST                5
LL5-LOCAL LAW 5/73                                                  4
CS-SITE SAFETY                                                      4
HVIOS-NYCHA ELEV ANNUAL INSPECTION/TEST                             3
ACJ1-(PRIVATE RESIDENCE) - ELEVATOR AFFIRMATION OF CORRECTION       3
LL10/81-LOCAL LAW 10/81 - ELEVATOR                                  2
LL2604-PHOTOLUMINESCENT                                             2
LL16-LOCAL LAW 16/84 - ELEVATOR                                     2
CLOS-PADLOCK                                                        2
A-SEU                                                               1
HVCAT5-NYCHA ELEVATOR PERIODIC INSPECTION/TEST                      1
Name: violation_type, dtype: int64*
```

*大多数违规行为都与电梯和锅炉有关。*

*我主要感兴趣的是查看违规的一般类型，因此这里我将把任何与电梯相关的违规重新标记为“电梯”，将锅炉违规重新标记为“锅炉”，以使其更清晰。*

*这可以通过 NumPy 的`where()`函数来完成。[单据](https://docs.scipy.org/doc/numpy/reference/generated/numpy.where.html)。*

*格式为`np.where(condition[, x, y])`。*

*它采用一个布尔数组，然后如果数组中的一项是`True`，则产生 x，否则产生 y。*

```
*elevators = df['violation_type'].str.contains('ELEVATOR') 
boilers = df['violation_type'].str.contains('BOILER') df['violation_type'] = np.where(elevators,'ELEVATOR', np.where(boilers, 'BOILER', df['violation_type'])*
```

*对列调用`str.contains(substring)`会返回一个布尔掩码，表明该行是否包含`substring`。*

*您还可以嵌套`np.where()`调用，因此，在本例中，我嵌套了对“BOILER”的搜索，然后如果既没有找到“ELEVATOR”也没有找到“BOILER”，就返回`df['violation_type']`的内容。*

*看看现在的价值计数。*

```
*In [8]: df['violation_type'].value_counts()                                                                                                                           
Out[8]: 
ELEVATOR                                           4412
AEUHAZ1-FAIL TO CERTIFY CLASS 1                    1856
BOILER                                             1550
BENCH-FAILURE TO BENCHMARK                          993
C-CONSTRUCTION                                      394
FISPNRF-NO REPORT AND / OR LATE FILING (FACADE)     179
RWNRF-RETAINING WALL                                138
LL2604S-SPRINKLER                                   107
EARCX-FAILURE TO SUBMIT EER                         100
FISP-FACADE SAFETY PROGRAM                           73
P-PLUMBING                                           72
IMEGNCY-IMMEDIATE EMERGENCY                          40
LANDMK-LANDMARK                                      35
UB-UNSAFE BUILDINGS                                  19
HVIOS-NYCHA ELEV ANNUAL INSPECTION/TEST              14
EGNCY-EMERGENCY                                      10
Z-ZONING                                              4
CLOS-PADLOCK                                          3
CS-SITE SAFETY                                        1
Name: violation_type, dtype: int64*
```

*为了进一步澄清这一点，我想研究其他违规类型，如“aeu HAZ 1-未能认证类 1 '”—不清楚这是什么。*

*有时你必须挖掘更多关于数据集的信息。*

*我将让读者查看其他列，看看如何用 NumPy `where()`或 pandas `apply()`函数清理它们。*

# *正常化*

*您可能不经常需要对您的数据进行规范化，只有当您想要进行依赖于正态分布数据的统计分析时才需要这样做，如 t 检验、ANOVA、线性回归等。*

# *文本规范化*

*如果你在做[自然语言处理](https://en.wikipedia.org/wiki/Natural_language_processing)，作为预处理的一部分，你可能需要规范化你的文本，使其更加统一。*

*它可能涉及多种技术。*

*   *标记化*
*   *删除空白*
*   *删除标点符号*
*   *删除任何非字母数字字符*
*   *堵塞物*
*   *词汇化*
*   *删除停用词*
*   *在英语中，你可以把英式英语转换成美式英语。*

# *感谢阅读！*

*Pandas 是一个非常有用的数据清理和操作工具，任何人都可以把它放在自己的数据工具箱里。*

*一本对熊猫来说很有参考价值的书是 [Python for Data Analysis](http://shop.oreilly.com/product/0636920023784.do) 。它是由韦斯·麦金尼写的，他是熊猫的主要作者之一。*

*当然，如果您有任何问题或意见，请告诉我！把它们写在下面。*