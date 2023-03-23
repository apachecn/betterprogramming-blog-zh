# 理解 Boyer-Moore 算法…以位为单位

> 原文：<https://betterprogramming.pub/understanding-the-boyer-moore-algorithm-in-bits-247f6e66c820>

## 使用 BMA 的简化版本搜索字符串是否包含另一个字符串

![](img/9ff51f86304c662d248c08dd08f51d50.png)

https://www.pexels.com/@pixabay 皮查拜[拍摄的照片](https://www.pexels.com/@pixabay)

如果您经常编写代码，那么有时您必须搜索一个字符串是否包含另一个字符串。一个很好的例子是搜索数据库中的行，以了解用户名是否存在，或者是否有标题中包含特定单词的帖子。

Boyer Moore 算法(BMA)是用来完成这项任务的众多算法之一。本文使用 BMA 的一个简化版本，以比特为单位向您介绍算法背后的思想。

# 天真的方式

在我们进入 BMA **，**之前，让我们来看看任何人都会用来解决这个问题的典型方法。我们先这样做，这样你就可以看到 BMA 是如何优化搜索的。

简单的解决方案是从左到右遍历要搜索的字符串中的每个字符，并将该字符及其后的所有字符与要查找的字符串中的字符进行比较。

然而，这给了我们一个时间复杂度为O(n*m ),这不是很有效。下面的代码片段展示了搜索字符串的简单方法。

# **博耶-摩尔方式**

## 我们如何知道一个字符串是匹配的？

首先，我们将从这个算法的基本情况开始(当两个字符串匹配时)。将两个字符串放在一起比较时，BMA 从右到左进行比较。

下面是一个比较两个长度相等的字符串的非常特殊的代码示例。(注意:这个代码片段不是完整的 Boyer-Moore 算法。这只是为了让你熟悉 Boyer-Moore 如何比较匹配的字符串。

从代码片段中，我们看到 BMA 从右到左比较字符串中的每个字符，直到指针的索引为零。

由于这是两个字符串长度相等的特殊情况，如果在任何字符中发现不匹配，`haystack`的索引被设置为等于字符串长度的值，从而停止`while`循环。

## 如果字符串不等长会发生什么？

在这里，我们将看到 BMA 如何优化它的字符串搜索。

同样，我将使用一个特例来使 BMA 在 O(n)时间内运行。这样，您将关注于该位的主要目标(BMA 如何通过被搜索的字符串移动要被找到的字符串)。

在上面的代码片段中，每当出现不匹配时(对于这个测试用例，它将出现在最后一个字符处)，我们向前移动 n-j 个字符。

当找到一个匹配时，我们继续向后遍历两个字符串，看看整个单词是否匹配。如果被比较的字符之间存在不匹配，那么我们将指针向前移动并重置 j。

这个代码片段的最佳运行时间将是 O(n) 但是当要搜索的字符串以重复字符作为其正确的前缀，并且要搜索的字符串以相同的重复字符作为其后缀时，它将具有 O(nm)的时间复杂度。

下面给出一个例子:

```
haystack = "aaaaaaaaaaaaaaaaaaaaaaaaabaaaaaaaa"
needle = "baaaaaaaa"
```

# 使用火柴提高效率

虽然上面的算法具有 O(n)的最佳时间复杂度，但是这不是完整的 BMA 算法，并且最佳情况的时间复杂度仍然可以提高到 O(n/m)。

BMA 优化搜索的另一种方法是利用两个字符串之间的匹配字符。

如果在比较`haystack`和`needle`时发现不匹配，并且`haystack`的不匹配字符出现在`needle` **，**的某处，其索引可用于跳过更多字符并减少要进行的比较次数。

首先，我们构建一个字典，保存`needle`中的每个字符及其出现的索引。如果出现不匹配，我们就检查它是否包含在字典中，并获取它的索引。

如果它包含在字典中，并且它的索引小于正在检查的当前字符的索引，我们将`needle`移动到这个字符。

如果该字符不包含在字典中，那么我们移动`needle`，直到它的第一个字符现在与`haystack`中不匹配字符之后的字符相比较。

下面的代码片段显示了移位操作:

```
# FIRST ITERATION WHEN MISMATCHED CHARACTER IS NOT FOUND IN NEEDLEHJKD**L**FAADFJ
QWER**T**T & L are compared and they do not match. L is not contained in the needle so we shift the needle so that F & Q are next to be compared# SECOND ITERATIONHJKDL**F**AADFJ
     **Q**WERT # FIRST ITERATION WHEN MISMATCHED CHARACTER IS FOUND IN NEEDLEHJKD**E**FAADFJ
QWER**T**# SECOND ITERATIONHJKDL**E**AADFJ
   QW**E**RT
```

上面的片段显示了完整的 BMA。这在最好的情况下有 O(n/m)的时间复杂度，在最坏的情况下有 O(nm)的时间复杂度，只要你在针中出现的干草堆中有重复的字符。

然而，在大多数语言中，字符不会以这种方式出现。因此，在实践中，我们不太可能让 BMA 出现最坏的情况。

然而，这并不是我让你了解 BMA 的最后一点。

# **最后一位**

当你看字典是如何形成的，你会注意到它是我们选择作为索引的一个字符的最后一次出现。

我们为什么要这么做？我将给出三个场景来解释这一点。

## **1。当两个字符之间出现不匹配时**

在下面的代码片段中，不匹配出现在 t 处。`haystack` 中的不匹配字符是 E，在`needle`中出现了两个 E。

```
ASSD**E**EKDLFGFJGFLGKJHFGKF
QWER**T**E
```

在这种情况下，看起来最佳的选择是从左边选择第一个 E，因为这将给我们更多的跳跃。然而，这可能会导致不准确，你很快就会看到。

## **2。当两个字符**前出现不匹配时

这里，在 BMA 中，我们选择哪个 Es 并不重要，因为它们都将被忽略，因为它们的索引大于不匹配的字符 t。

```
ASSD**E**EKDLFGFJGFLGKJHFGKF
QWTR**T**EE
```

## **3。当两个字符**后出现不匹配时

在这种情况下，我们将看到选择具有较小索引的重复字符是如何的错误。

```
# FIRST ITERATIONAZXEZM**E**LPOI
ZXEZME**L**# SECOND ITERATION -- WHEN THE SMALLER INDEX IS USEDAZXEZM**E**LPOI
    ZX**E**ZMEL# SECOND ITERATION -- WHEN THE LARGER INDEX IS USEDAZXEZM**E**LPOI
 ZXEZM**E**L
```

从上面的片段中我们可以看到，当我们使用较小的索引时，我们冒着跳过第二个重复字符可能出现的匹配(并导致完全匹配)的风险。

因此，通过使用最后一个也是最大的索引，当我们遇到发生在`needle`中的`haystack`不匹配时，我们可以得到最大的、最安全的跳转。