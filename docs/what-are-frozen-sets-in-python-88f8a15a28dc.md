# Python 中的冻结集是什么？

> 原文：<https://betterprogramming.pub/what-are-frozen-sets-in-python-88f8a15a28dc>

## 如果元组和集有一个私生子

![](img/abd3c819ca6306b915bfc11ce00a27c0.png)

照片由[杰米街](https://unsplash.com/@jamie452?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/frozen?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

*   集合是一种无序的可变数据类型，其中每个值都必须是唯一的。
*   元组是一种有序的、不可变的数据类型，其中对重复值没有限制。

以防万一，可变/不可变指的是能够/不能改变存储的值。

希望您可以在一种数据类型中拥有两个世界的优点？你可以，用冷冻的布景。

# 什么是冷冻集？

冻结集是 Python 中的一种原生数据类型，具有集的性质——包括类方法——但像元组一样是不可变的。

要使用冻结集，调用函数`frozenset()`并传递一个 iterable 作为参数。

```
scores = {1,2}
scores.add(3) # {1,2,3}
```

如果将一个集合传递给函数，它将返回相同的集合，这个集合现在是不可变的。

```
scores = {1,2}
frozen_scores = frozenset(scores)
frozen_scores.add(3)
# AttributeError: 'frozenset' object has no attribute 'add'
```

如果您传递另一种数据类型，如列表、元组或字符串，那么它将被视为 iterable。

这意味着该值将被分解成单独的部分，这些部分将被简化为一组不可变的唯一值。

```
myList = [1,1,2,3,4]
myString = "Hello"frozenList = frozenset(myList)
frozenString = frozenset(myString)print(frozenList) # frozenset({1, 2, 3, 4})
print(frozenString) # frozenset({'e', 'l', 'H', 'o'})
```

# 为什么要用冷冻套？

老实说，当选择使用冻结集时，在性能方面没有太大的好处。

使用它们的主要原因是为了编写更清晰、功能性更强的代码。通过将一个变量定义为一个冻结的集合，你在告诉未来的读者:不要修改它！

不幸的是，冻结的集合不能用像带花括号的集合、带方括号的列表或带圆括号的元组这样的字符符号来声明。

如果你想使用一个冻结的集合，你必须使用函数来构造它。

# 结论

您的项目或生产代码使用冻结集吗？是不是觉得申报不够方便？在下面分享你的想法，感谢你的阅读！