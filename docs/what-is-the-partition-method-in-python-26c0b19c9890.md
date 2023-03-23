# Python 中的 partition()方法是什么？

> 原文：<https://betterprogramming.pub/what-is-the-partition-method-in-python-26c0b19c9890>

## 学习通过特定匹配来拆分字符串

![](img/951775b55b9649b6a848d9cfe119c49d.png)

Alesia Kazantceva 在 [Unsplash](https://unsplash.com/s/photos/partition?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Python 的数据类型是内置好东西的宝库。其中一个好处是分区方法——流行的`split()`方法的替代方法。

`partition()`方法用于根据指定的子串和三值元组中原始字符串的所有部分来分解字符串。

# 实现 partition()方法

由于`partition()`是一个方法，它必须从一个字符串对象中被调用。这意味着它不能作为独立的函数调用。

```
myString.partition() # valid
"Python".partition() # valid
partition(myString)  # invalid
```

该方法接受一个必需的参数，即要搜索的字符串。

```
myString.partition("search string")
```

该方法将返回一个具有三个值的元组——不可变的有序数据类型。假设找到了子串，这三个值将是(1)前面的文本，(2)子串，和(3)后面的文本。

如果找不到子串会怎么样？该方法仍将返回一个三值元组，但索引 2 和 3 将是空字符串。

# 你将如何使用这个？

除非您是带着问题阅读本文的，否则可能很难理解如何使用这种方法。为了有所帮助，让我们比较一下`partition()`和更常用的`split()`方法。

这两个方法分开一个字符串；然而，这些品质使得`partition()`独一无二:

*   只拆分字符串一次。
*   跟踪分隔符。
*   总是返回相同的数据结构。

因此，如果您只需要在第一次出现时分割字符串，稍后使用分隔符，或者依赖有保证的数据结构，那么`partition()`方法很可能是完成这项工作的最佳工具。

感谢阅读，请在评论下面分享你的评论和经验！