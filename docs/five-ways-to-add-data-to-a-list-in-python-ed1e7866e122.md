# 在 Python 中向列表添加数据的五种方法

> 原文：<https://betterprogramming.pub/five-ways-to-add-data-to-a-list-in-python-ed1e7866e122>

## 追加、插入、扩展等等

![](img/34b2ef9a94ef3ea764de7a5b7e8b4a4b.png)

照片由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/python?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

列表是一种可以包含多个元素的数据结构。这些元素是有序的，由一个从零开始的非负整数表示。

此外，列表是可变的，这意味着它们可以被改变。对列表最常见的更改是添加元素。

以下是将数据添加到现有列表的四种不同方式。

# 的。append()方法

向列表末尾添加数据是使用`.append()`方法完成的。如果你熟悉 JavaScript，这个方法相当于`.push()`。

```
teams = [
  "Jaguars",
  "Colts",
  "Titans"
]teams.append("Texans")print(teams)
# ['Jaguars', 'Colts', 'Titans', 'Texans']
```

追加另一个列表时要小心，因为结果将是下一个可用索引处的嵌套列表。

```
teams = [
  "Jaguars",
  "Colts",
  "Titans"
]teams.append(["Texans", "Eagles"])print(teams)
# ['Jaguars', 'Colts', 'Titans', ['Texans', 'Eagles']]
```

# 的。insert()方法

当你想在列表的开头或中间添加数据时，使用`insert()`方法。请注意，添加新元素的索引是该方法的第一个参数。

```
teams = [
  "Jaguars",
  "Colts",
  "Titans"
]teams.insert(0, "Texans")print(teams)
# ['Texans', 'Jaguars', 'Colts', 'Titans']
```

# 的。extend()方法

如果您的目标是合并两个列表，那么`.extend()`方法会将提供的列表值附加到原始列表的末尾。

```
teams = [
  "Jaguars",
  "Colts",
  "Titans"
]teams.extend(["Texans", "Eagles"])print(teams)
# ['Jaguars', 'Colts', 'Titans', 'Texans', 'Eagles']
```

提供的参数必须是可迭代的，并且将被视为可迭代的，如果没有准备好，会产生有趣的结果。

```
teams = [
  "Jaguars",
  "Colts",
  "Titans"
]teams.extend("Texans")print(teams)
# ['Jaguars', 'Colts', 'Titans', 'T', 'e', 'x', 'a', 'n', 's']
```

# 加号运算符(+)

使用`.extend()`方法的一个同义技术是用加号运算符连接两个列表。这两种技术在性能上差别不大；但是，请注意两个操作数都必须是列表，否则将会抛出 TypeError。

```
first_list = [1,2,3]
second_list = [4,5,6]print(first_list+second_list)
# [1, 2, 3, 4, 5, 6]
```