# 如何在 Python 中检查列表是否为空

> 原文：<https://betterprogramming.pub/how-to-check-if-a-list-is-empty-in-python-b29faecaadc1>

## 学习多种技术来检查空列表

![](img/2b270f7c42b791b18ebccfd19b09fe3a.png)

照片由[安德鲁·尼尔](https://unsplash.com/@andrewtneel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/list?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

有许多选项可以检查列表是否为空。在进入解决方案的语法之前，让我们列出在决定使用哪种方法时所涉及的不同因素。

我们设计的表达式可以分为两类，一类是与空列表的显式比较，另一类是对空列表的隐式评估。那是什么意思？

# 显式比较

让我们从显式比较开始。无论我们使用列表符号`[]`还是空函数`list()`，策略都是看我们所讨论的列表是否与空列表完全相同。

```
# both create an empty list
a = []
b = list()print(a == b) # True
```

此外，我们可以使用`len()`函数返回列表中条目的实际数量。

```
a = []if len(a) == 0:
   print("The list is empty")
```

# 隐性评估

相反，隐式求值利用了空列表将被求值为`False`布尔值，而填充列表将被求值为`True`布尔值的方式。

```
a = []
b = [1]if a:
   print("Evaluated True")
else:
   print("Evaluated False")if b:
   print("Evaluated True")
else:
   print("Evaluated False")"""
Evaluated False
Evaluated True
"""
```

所以，这有什么区别吗？

我已经养成了使用显式比较的习惯；然而，如果你试图遵循鸭子打字的做法，那么你想用隐式方法。

# 什么是鸭子打字？

术语*鸭子打字*来源于以下短语:

如果它走路像鸭子，叫起来像鸭子，那它一定是鸭子。

从功能上来说，这是承认对象的实际数据类型将承受更小的压力。相反，重点将放在它的属性的行为上，比如可迭代。最终，目标是成为类型不可知论者。

Duck typing 优先考虑方便性而不是安全性，允许更灵活的代码，可以适应更广泛的用途，并且没有传统约定那么严格。

# 我应该使用哪种技术？

我越来越习惯于隐式求值，并因此更倾向于隐式求值，因为我知道空列表的求值结果是`False`。

```
a = []print(bool(a)) # False
```

这允许我合并更长的表达式检查，例如:

```
# BEFORE
if isinstance(a,list) and len(a) > 0:
   print("Processing list...")# AFTER
if a:
   print("Processing list...")
```

最终，选择将归结为空列表的含义。

你在检查一个列表是否为空，因为你想迭代它吗？隐性评估是阻力最小的途径。

您是否正在检查空列表，因为您的代码计划接下来使用列表方法？在这种情况下，我可能会选择显式比较来验证数据类型。

你喜欢哪种方法？在决定使用什么技术来检查空列表时，决定因素是什么？