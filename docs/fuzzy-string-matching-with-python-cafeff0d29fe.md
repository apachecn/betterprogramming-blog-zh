# 用 Python 实现模糊字符串匹配

> 原文：<https://betterprogramming.pub/fuzzy-string-matching-with-python-cafeff0d29fe>

## Levenshtein 距离法

![](img/a54e8cd683ed8cc5b7e869b2c4b2f424.png)

Henrik Lagercrantz 在 [Unsplash](/s/photos/words-match?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

最近，我在处理一个数据集，必须匹配字符串。我必须计算字符串匹配的百分比。因为我是用 Python 工作的，所以我知道肯定有它的库。

不管是什么，Python 都有它的库！

计算机科学中有一个概念叫模糊字符串匹配。它是从字符串中匹配模式的技术。如果您拼错了一个单词，但有一个拼写正确的单词，您可以模糊字符串匹配并找到匹配的百分比。

在本文中，我将讨论如何在 Python 中模糊匹配字符串。

# Python 中的 FuzzyWuzzy

在 Python 中处理字符串匹配问题时，可以导入 [FuzzyWuzzy](https://pypi.org/project/fuzzywuzzy/) 。FuzzyWuzzy 是一个 Python 库，它使用 [Levenshtein 距离](https://en.wikipedia.org/wiki/Levenshtein_distance)来计算一个简单易用的包中序列之间的差异。

您可以使用以下命令安装它:

```
#for pip users
pip install fuzzywuzzy#for Jupyter notebook
conda install -c conda-forge fuzzywuzzy
```

安装完这个库之后，有很多方法可以使用这个库进行字符串匹配。

```
from fuzzywuzzy import process, fuzz
```

我已经导入了上面的库。现在，让我们看看这个库的不同用例。

# 模糊模块

## **简单比率**

`ratio`方法比较整个字符串，并遵循两个字符串之间的标准 Levenshtein 距离相似性比率:

它将比较整个字符串并输出匹配的百分比:

```
**[Output 0]:** String Matched: 96**[Output 1]:** String Matched: 91**[Output 2]:** String Matched: 100
```

## **部分比率**

`partial ratio`方法基于“最优部分”逻辑。如果考虑短串 *k* 和长串 *m* ，算法将通过匹配 *k* 串的长度来评分:

```
Str_Partial_Match = fuzz.partial_ratio('Hello World', 'Hello World!')
print("String Matched:",Str_Partial_Match)Str_Partial_Match = fuzz.partial_ratio('Hello World', 'Hello world')
print("String Matched:",Str_Partial_Match)
```

上面的代码将只寻找部分匹配。在第一种情况下，较小的字符串(`Hello World`)将被查找到较大的字符串中，匹配将会发生。

而在第二种情况下，字符串的长度是相同的，但是字母将被匹配:

```
**[Output 0]:** String Matched: 100**[Output 1]:** String Matched: 91
```

## **令牌分类比率**

`token sort ratio`方法按字母顺序对标记进行排序。然后，应用简单的`ratio`方法输出匹配的百分比:

```
Str_Sort_Match = fuzz.token_sort_ratio('Hello World', 'Hello wrld')
print("String Matched:",Str_Partial_Match)Str_Sort_Match = fuzz.token_sort_ratio('Hello World', 'world Hello')
print("String Matched:",Str_Partial_Match)
```

上面的代码将忽略单词的顺序。

```
**[Output 0]:** String Matched: 95**[Output 1]:** String Matched: 100 
```

## **令牌设置比率**

`token set ratio`忽略重复的单词。它类似于`sort ratio`方法，但更灵活。它基本上提取公共令牌，然后应用`fuzz.ratio()`进行比较:

```
String_Matched=fuzz.token_set_ratio('Hello World', 'Hello Hello world')
print(String_Matched)
```

它将忽略重复的值。

```
**[Output 0]:** 100
```

是时候看看每种方法并进行比较了:

我拿了两根弦。现在，我将使用每个模糊方法来了解字符串相似性:

```
**Ratio:** 57 **Partial_Ratio:** 77 **Token_Sort_Ratio:** 58 **Token_Set_Ratio:** 95
```

# 过程模块

## **提取**

来自`process`模块的`extract`方法计算字符串向量的相似性得分:

```
str2Match = "apple inc"
strOptions = ["Apple Inc.","apple park","apple incorporated","iphone"]
```

我必须将字符串列表(`strOptions`)与`str2Match`)进行匹配:

```
Ratios = process.extract(str2Match,strOptions)
print(Ratios)
```

extract 方法将根据与匹配字符串的相似性对列表中的每个字符串进行评分(`str2Match`)。

```
**[Output 0]:** [('Apple Inc.', 100), ('apple incorporated', 90), ('apple park', 67), ('iphone', 30)]
```

## **提取一个**

`extractOne`方法输出字符串向量中最相似的字符串及其分数:

```
Ratios = process.extractOne(str2Match,strOptions)
print(Ratios)
```

最相似的字符串将以这种方式输出:

```
**[Output 0]:** ('Apple Inc.', 100)
```

# 参考

*   [Python 中的模糊字符串匹配](https://www.datacamp.com/community/tutorials/fuzzy-string-python)
*   [模糊不清](https://github.com/seatgeek/fuzzywuzzy)

和平！