# Python 有字符串“包含”子串方法吗？

> 原文：<https://betterprogramming.pub/does-python-have-a-string-contains-substring-method-2a3a4e0e1b19>

## 简短的回答是:“是的。”下面是如何使用它

![](img/7b6fa98a1f77f4d16b23b0fb32cf3e04.png)

[Zan](https://unsplash.com/@zanilic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/programmer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

对于不熟悉 Java 的人来说，`.contains()`方法属于`String`类，如果提供的子串存在，则返回`true`或`false`。这类似于 JavaScript 中的`.includes()`方法。

全面地说，识别一个子串就是回答两个问题中的一个:“*子串存在吗？”和“*其中*是子串存在吗？”这两个问题都可以用 Python 来回答，但是每个问题都有自己的解决方案。*

# 子字符串是否存在？

要回答子字符串是否存在的问题，请使用`in`关键字。

`in`关键字意在搜索一个 iterable，比如[字典](https://medium.com/better-programming/how-to-check-if-a-given-key-already-exists-in-a-dictionary-556a56e03c22)或列表。字符串也是一种可迭代的数据类型，结果表达式将根据计算返回`True`或`False`。

```
a = "Shinra"
print("ra" in a)  # True
print("tai" in a) # False
```

这个方法是最直接和最 Pythonic 化的，尽管它给出的布尔答案是有限的。

# 子串出现在哪里？

不仅要找出*是否存在子串，还要找出*在哪里，使用包含在`String`类中的`.find()`方法。**

与 JavaScript 中的`.indexOf()`类似，`.find()`方法将返回给定子串的起始索引位置。如果没有找到子串，将返回值`-1`。

```
a = "Shinra"
print(a.find("ra"))  # 4
print(a.find("tai")) # -1
```

这种方法是有益的，因为您可以简单地获取返回，并使用表达式`>= 0`导出与使用关键字`in`相同的答案。

```
a = "Shinra"
if a.find("ra") >= 0:
  # substring was found
```