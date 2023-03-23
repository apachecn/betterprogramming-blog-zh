# JavaScript 中 Slice()的 5 个用例

> 原文：<https://betterprogramming.pub/5-use-cases-for-slice-in-javascript-13462f2e177f>

## 通过学习 slice()的不同用例来丰富您的 JavaScript 知识

![](img/242bfbf7c6ce9c7d349703c51e76715f.png)

Stephanie Harvey 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

`slice()`方法将数组部分的副本返回到一个新的数组对象中。这个对象是从`start` 到`end`中选择的。注意，原始数组不会被修改。

此外，如果向其中一个数组添加新元素，另一个数组将不会受到影响。

# 参数是什么？

`slice()`方法的参数是一个开始和结束指标。

## **开始**

它是一个从零开始的索引，用来开始复制数组的一部分。如果未定义，默认值为 0。如果`start` 大于数组的索引范围，`slice()`方法将返回一个空数组。

还有，对于`start`，也可以使用负索引。提取数组的最后一个元素。它类似于 Python

## **结束**

该参数是可选的。如果你的`slice()`函数中只有一个参数，那就是`start` **。**如果省略，`slice()`方法提取到序列的末尾。

如果它大于序列的长度，`slice`在它被省略的情况下提取到序列的末尾。

它是结束提取之前的索引。它不是包容性的。因此，索引的最后一个元素不包括在数组的副本中。例如，`slice(1,3)`提取第二个和第三个元素。

```
y = [1, 2, 3, 4, 5, 6]
y.slice(2, -2) // will return [3, 4]
```

这意味着 y 将从索引 2 到索引 2 独占地对数组进行切片。

# 1.复制数组

第一个功能是`slice()`功能的基本功能。没有参数的数组复制原始数组。有时，您可能希望更新数组中的一些元素。

但是，您可能希望保护原始数组中的元素。因此，您可以创建原始数组的浅层副本。

```
const midtermGrades = updatedGrades.slice();
```

# 2.构造一个从 n 开始的子数组

`slice()`方法的第二个用例是复制一个以 n 开始的子数组。例如，您正在分析考试中获得 80 分或更高分数的学生。你发现学生的指数从 10 开始。所以，你可以复制成功学生的指数。

```
const successfulStudents = allStudents.slice(10);
```

也可以得到不满意的学生。

```
const unsatisfactoryStudents = allStudents.slice(-10);
```

# 3.将类似数组的对象转换为数组

您可以使用`slice()`方法将看起来像数组的对象转换成数组。例如，按如下方式创建函数。

作者创建的要点

# 4.将节点列表转换为数组

NodeList 对象是从文档中提取的节点的集合。您可以使用`querySelectorAll()`方法返回一个 NodeList 对象。例如，您可以选择 HTML 文档中的所有`<p>`节点。使用`slice()`，您可以将选择的节点列表转换成数组。

```
var p = document.querySelectorAll(‘p’);
var pNodes = Array.prototype.slice.call(p);
```

# 5.替换字符串中的特定索引

您可以使用`slice()`功能创建替换功能。

作者创建的要点

# 结论

学习 JavaScript 内置函数的用例可以帮助你提高编码技能。你可以在需要的时候运用你的知识。你可以优雅地实现函数。

`slice()`也是一个有用的内置函数。知道怎么用，遇到需要用的问题就可以用。您可以使用`slice().`轻松获得数组的副本

总之，本文中`slice()`的用例如下:

*   复制数组
*   从 n 开始构造子阵列
*   将类似数组的对象转换为数组
*   将节点列表转换为数组
*   替换字符串中的特定索引