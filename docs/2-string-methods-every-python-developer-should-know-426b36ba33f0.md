# 每个 Python 开发人员都应该知道的 2 个字符串方法

> 原文：<https://betterprogramming.pub/2-string-methods-every-python-developer-should-know-426b36ba33f0>

## 将可重复项转换为字符串，反之亦然

![](img/a458db0ac9e33ea35c51ea2be31cc170.png)

[davisuko](https://unsplash.com/@davisuko?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

在 Python 中知道两个极其有用的字符串方法是`join` 和`split`。它们可以用来从另一个 iterable(例如，一个列表)中创建一个单独的字符串，或者从一个单独的字符串中创建一个包含多个字符串的列表。

在本教程中，我们将学习这些`join`*`split`*方法是什么以及如何使用它们。**

# **将可重复的字符串连接成一个字符串**

**假设我们想要使用另一个 iterable 对象创建一个字符串，比如下面的列表，它包含多个字符串:**

```
****list_of_strings** = ['Hello', 'I', 'am', 'a', 'test']**
```

**我们可以使用字符串`[join](https://docs.python.org/3/library/stdtypes.html#str.join)`方法将这个字符串列表合并成一个字符串:**

```
**str.join(iterable)**
```

**`join`是串法。分隔符是提供这个方法的字符串，或`str`，它将分隔传入的 iterable 中的元素。这将返回一个字符串，该字符串是 iterable 中字符串的串联。**

```
****string** = ''.join(**list_of_strings**)print(**string**)
# 'HelloIamatest'**
```

**因为分隔符是空字符串，所以 iterable 中的字符串不会被任何东西分隔。**

**我们可以在列表或 iterable 的每个元素之间包含一个空格，如下所示:**

```
****string** = ' '.join(**list_of_strings**)print(**string**)
# 'Hello I am a test'**
```

**或者破折号:**

```
****string** = '-'.join(**list_of_strings**)print(**string**)
# 'Hello-I-am-a-test'**
```

**如果 iterable 包含任何非字符串值，那么将引发一个`TypeError`:**

```
****not_all_strings** = ['Hello', 'I', 3, 'am', 'a', 'test']**string** = ' '.join(**not_all_strings**)print(**string**)
# TypeError**
```

**关于可重复项目的评论:**

**[](https://towardsdatascience.com/iterables-and-iterators-in-python-849b1556ce27) [## Python 中的迭代器和迭代器

### Python 中的可迭代对象、迭代器和迭代

towardsdatascience.com](https://towardsdatascience.com/iterables-and-iterators-in-python-849b1556ce27)** 

# **将单个字符串拆分成一个列表**

**我们也可以做相反的事情——使用`[split](https://docs.python.org/3/library/stdtypes.html#str.split)`方法从单个字符串中制作一个字符串列表:**

```
**str.split(sep=None, maxsplit=-1)**
```

**`str`是将被拆分或用于创建列表的字符串。`sep`是分隔符字符串。`maxsplit`是所需的最大拆分数(因此列表最多有`maxsplit+1`个元素)。如果未指定`maxsplit`(意味着它是`-1`的默认值)，则拆分次数没有限制。**

```
****list_of_words** = **'Hello I am a test'**.split(' ')print(**list_of_words**)
# ['Hello', 'I', 'am', 'a', 'test']**
```

**请注意，使用的分隔符只是一个空格，因为这是我们想要分割字符串的地方。**

```
****list_of_words** = **'Hello-I-am-a-test'**.split('-')print(**list_of_words**)
# ['Hello', 'I', 'am', 'a', 'test']**
```

**如果我们指定`maxsplit` 为`3`，那么在达到最大拆分数之后，字符串的剩余部分将作为列表中的最后一个元素返回:**

```
****list_of_words** = **'Hello-I-am-a-test'**.split('-', 3)print(**list_of_words**)
# ['Hello', 'I', 'am', 'a-test']**
```

**请注意，连续分隔符是*而不是* 组合在一起的。因此，如果我们在一行中有两个破折号，列表中的一个元素将是一个空字符串:**

```
****list_of_words** = **'Hello--I-am-a-test'**.split('-')print(**list_of_words**)
# ['Hello', '', 'I', 'am', 'a', 'test']**
```

**如果未指定`sep` (默认值为`None`)，则连续空格将被用作单个分隔符。此外，如果有任何前导或尾随空格，它将不会包括在返回的列表中:**

```
****list_of_nums** = **'   1     2   3 4   '**.split()print(**list_of_nums**)
# ['1', '2', '3', '4']**
```

**如果你喜欢阅读这样的故事，并想支持我成为一名作家，考虑注册成为一名媒体成员。每月 5 美元，你可以无限制地阅读媒体上的故事。如果你用我的 [*链接*](https://lmatalka90.medium.com/membership) *注册，我会赚一小笔佣金。***

**[](https://lmatalka90.medium.com/membership) [## 通过我的推荐链接加入媒体——卢艾·马塔尔卡

### 阅读卢艾·马塔尔卡的每一个故事(以及媒体上成千上万的其他作家)。您的会员费直接支持…

lmatalka90.medium.com](https://lmatalka90.medium.com/membership)** 

***我希望你喜欢这个关于* `*join*` *和* `*split*` *字符串方法的教程。感谢您的阅读！***