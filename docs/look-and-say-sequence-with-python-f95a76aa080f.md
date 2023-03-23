# 用 Python 看一看说一说序列

> 原文：<https://betterprogramming.pub/look-and-say-sequence-with-python-f95a76aa080f>

## 使用 groupBy 方法的力量解决一个小游戏

![](img/8270a82e37c5dd06598883752308f824.png)

Cyrus Lopes 在 [Unsplash](https://unsplash.com/collections/1388266/vintage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

模式…无处不在，我们的物种已经进化到能够识别它们。上周我在 Twitter 上看到一个有趣的帖子:

我有一个挑战给你！下面是一个数列:
`1–11–21–1211–111221–312211`
你能猜出下一个数字吗？

通常，模式或序列是可视的，但这一个不是。只要看着它，你就能知道这是一个数列，但是顺序是什么呢？

这是一个棘手的问题，因为你必须大声说出来才能找到答案！为了破译下一个元素，我们必须计算一个数字在序列的给定状态中出现了多少次。让我们看看进展如何:

1.  1 —“一”1(成为系列中的下一个数字)。
2.  11 —“二”一
3.  21 —“一”二，“一”一
4.  1211 —“一”一，“一”二，“二”一
5.  111221 —等等，等等…

*引号中的数字是一个数字在序列中出现的次数。*

然后，在同一条推文中，有一个奖励:

你能写一个程序来计算数列的下一项吗？

在多次尝试使用字典、列表理解和 NumPy 数组之后，我找到了解决这个问题的正确模块:`itertools`。前提是:你需要计算一个数字在下一个数字之前在序列中出现了多少次。这可以使用`groupby`方法来完成。

根据`itertools`文档，`groupby`从一个 iterable (list，tuple，dictionary)返回一个带有多个`keys`和`groups`的迭代器。让我们来看一个`itertools`例子:

```
>>>[key for key, group in groupby('AAAABBBCCDAABBB')]
[A, B, C, D, A, B]
```

如您所见，这些键是组成每组的字母。当`groupby`发现一个与上一个字母不同的新字母时，它会创建一个新的密钥。例如，它返回“A”和“B”的两个键，因为即使它们是相同的字母，它们也被其他字母的组分隔开。

现在我们要去集合了。在这种情况下，`group`是分组项目的 iterable。因此，我们将把它的元素解包为一个列表。

```
>>>[list(group) for key, group in groupby('AAAABBBCCDAABBB')]
[['A', 'A', 'A', 'A'], ['B', 'B', 'B'], ['C', 'C'], ['D'], ['A', 'A'], ['B', 'B', 'B']]
```

下一步是获取每组的物品数量:

```
>>>[len(list(group)) for key, group in groupby('AAAABBBCCDAABBB')]
[4, 3, 2, 1, 2, 3]
```

你能看到我们如何使用`groupby`来完成“看和说”序列吗？如果还不清楚，让我们更进一步，将每个组的计数和它的键结合起来:

```
>>>[(len(list(group)),key) for key, group in groupby('AAAABBBCCDAABBB')]
[(4, 'A'), (3, 'B'), (2, 'C'), (1, 'D'), (2, 'A'), (3, 'B')]
```

正如你所看到的，我们知道元组包含每个组的计数和代表它的值。我们可以使用上面的逻辑用 Python 计算外观和说法序列！

我将使用递归方法，从序列的第一个元素开始，在本例中是 1，计算下一个元素，并将其作为字符串添加到名为`arr`的列表中。该方法再次重复，直到迭代次数为 0。

答案`final_sequence`，用`iterator = 15`，看起来大概是这样的:

就是这样！这是一个微不足道的游戏，但它展示了`groupby`方法是多么强大。