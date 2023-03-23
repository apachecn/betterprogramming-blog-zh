# 今天你应该学习的 20 个 Python 片段

> 原文：<https://betterprogramming.pub/20-python-snippets-you-should-learn-today-8328e26ff124>

## 帮助您更快编码的一些提示和技巧

![](img/d0f3cd352aadd2e094ca0f7da8b3d21e.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

Python 是一种无 BS 编程语言。可读性和设计的简单性是它大受欢迎的两个最大原因。

正如 Python 的禅宗所说:

> 漂亮总比难看好。
> 显性比隐性好。

这就是为什么值得记住一些常见的 Python 技巧来帮助改进您的代码设计。这些会省去你每次需要做什么的时候冲浪栈溢出的麻烦。

以下技巧将证明在你的日常编码练习中是很方便的。

# 1.反转字符串

以下代码片段使用 Python 切片操作反转字符串。

你可以在这里阅读更多关于这个[的内容。](https://medium.com/swlh/how-to-reverse-a-string-in-python-66fc4bbc7379)

# 2.使用标题大小写(首字母大写)

以下代码片段可用于将字符串转换为标题大小写。这是使用 string 类的`title()` 方法完成的。

# 3.在字符串中查找唯一元素

以下代码片段可用于查找字符串中的所有唯一元素。我们利用了集合中的所有元素都是唯一的这一特性。

# 4.打印一个字符串或一个列表 n 次

您可以对字符串或列表使用乘法(*)。这使得我们可以随心所欲地将它们成倍增加。

一个有趣的用例是定义一个具有常量值的列表——假设为零。

```
n = 4
my_list = [0]*n # n denotes the length of the required list
# [0, 0, 0, 0]
```

# 5.列表理解

列表理解为我们提供了一种基于其他列表创建列表的优雅方式。

下面的代码片段通过将旧列表的每个元素乘以 2 来创建一个新列表。

你可以在这里阅读更多信息[。](https://medium.com/swlh/list-comprehensions-in-python-e8d409bb216e)

# 6.在两个变量之间交换值

Python 使得在不使用另一个变量的情况下在两个变量之间交换值变得非常简单。

# 7.将一个字符串拆分成子字符串列表

我们可以使用 string 类中的`.split()`方法将一个字符串分割成子字符串列表。您也可以将希望拆分的分隔符作为参数传递。

# 8.将字符串列表组合成单个字符串

`join()`方法将作为参数传递的一系列字符串组合成一个字符串。在我们的例子中，我们使用逗号分隔符将它们分开。

# 9.检查给定的字符串是否是回文

我们已经讨论过如何反转一个字符串。所以在 Python 中回文变成了一个简单的程序。

# **10。列表中元素的频率**

有多种方法可以做到这一点，但我最喜欢的是使用 Python `Counter`类。

[Python 计数器](https://medium.com/datadriveninvestor/an-introduction-to-python-counter-47948fdd9c1a)跟踪容器中每个元素的频率。`Counter()`返回一个字典，以元素为键，以频率为值。

我们还使用`most_common()`函数来获取列表中的`most_frequent element`。

# 11.找出两个字符串是否是变位词

`Counter`类的一个有趣的应用是寻找字谜。

变位词是通过重新排列不同单词或短语的字母而形成的单词或短语。

如果两个字符串的`Counter`对象相等，那么它们就是字谜。

# 12.使用 try-except-else 块

使用 try/except 块可以很容易地完成 Python 中的错误处理。向该块中添加一个 else 语句可能会很有用。当 try 块中没有引发异常时运行。

如果你需要运行一些不考虑异常的东西，使用`finally`。

# 13.使用枚举获取索引/值对

以下脚本使用 enumerate 遍历列表中的值及其索引。

# 14.检查对象的内存使用情况

以下脚本可用于检查对象的内存使用情况。点击阅读更多信息[。](https://code.tutsplus.com/tutorials/understand-how-much-memory-your-python-objects-use--cms-25609)

# 15.合并两本词典

而在 Python 2 中，我们使用了`update()`方法来合并两个字典；Python 3.5 使得这个过程更加简单。

在下面给出的脚本中，两个字典被合并。在交叉的情况下使用第二个字典中的值。

# 16.执行一段代码所花费的时间

下面的代码片段使用`time`库来计算执行一段代码所需的时间。

# 17.展平列表列表

有时你不确定列表的嵌套深度，你只是希望所有的元素都在一个平面列表中。

你可以这样得到它:

如果你有一个格式正确的数组，Numpy flatten 是一个更好的方法。

# 18.从列表中抽样

下面的代码片段使用`random`库从给定的列表中生成`n`个随机样本。

有人向我推荐了[秘密](https://docs.python.org/3/library/secrets.html)库，用于为加密目的生成随机样本。以下代码片段仅适用于 Python 3。

# 19.数字化

下面的代码片段将把一个整数转换成一个数字列表。

# 20.检查唯一性

下面的函数将检查列表中的所有元素是否都是唯一的。

# 结论

这些是我发现在日常工作中非常有用的一些小片段。 [30 秒的 python](https://python.30secondsofcode.org/) 在研究这个故事时帮了大忙。

谢谢你读了这个故事。希望你喜欢。