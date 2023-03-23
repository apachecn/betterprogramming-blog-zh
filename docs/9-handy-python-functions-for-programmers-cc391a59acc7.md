# 程序员的 9 个方便的 Python 函数

> 原文：<https://betterprogramming.pub/9-handy-python-functions-for-programmers-cc391a59acc7>

## 给程序员的建议

## 根据我个人的脚本编写经验

![](img/997bee0f0e6b3118e9d439fba52f1d72.png)

照片由[林赛·亨伍德](https://unsplash.com/@lindsayhenwood?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

就这种语言提供的快捷方式而言，Python 非常方便。一些快捷方式非常有用，可以使脚本编写更快，并优化您的代码。

Python 库中提供了这些快捷方式，使用它们可以使您的代码工作得更快、更容易。我们需要习惯这些功能，这样我们就可以在需要的时候使用它们，节省我们的时间。

在本文中，我将分享我根据个人编码经验总结出的九个最有用的函数。

# 1.反转()

在编程时，我们经常需要反转一个字符串或一个列表。对于反转任何对象，Python 提供了一个函数，可以用来优化您的代码，而不是使用循环来反转它。

# 2.zip()

对于并行迭代，当我们需要迭代多个列表时,`zip()`函数非常方便。该函数通常与循环一起使用，用于比较每个列表中相似的索引元素。这里我们可以在`zip()`函数中使用任意数量的列表来遍历它们。

# 3.len()

这个函数用来计算任何物体的长度。这个函数在 Python 中与 range 函数一起迭代对象时最有用。

# 4.isnumeric()

如果你想检查你的字符串是否只有数值，这个函数会很有用。该函数主要用于验证任何对象的正则表达式。

# 5.在

这用于检查一个对象是否包含任何其他对象。对象可以是字符串、列表、字典或元组。

```
bikes = ['trek', 'redline', 'giant']
ans='trek' in bikes
print(ans)**Output**
True
```

# 6.is_upper()

这个函数用于检查字符串中的所有字符是否都是大写的。

```
txt = "THIS IS MeDIUM!"
x = txt.isupper()
print(x)**Output** False
```

# 7.加入()

该函数通常与 Python 中的 split 函数一起使用。在进行拆分时，我们试图将字符串拆分成小字符串。另一方面，`join()`函数将所有拆分的项目连接起来。

```
myTuple = ("John", "Peter", "Vicky")
x = " ".join(myTuple)
print(x)**Output** John Peter Vicky
```

# 8.伊斯南()

这个函数非常方便地检查一个对象是否是 NaN。

# 9.拆分()

该功能主要在使用`input()`功能获取用户输入时使用。它将对象作为一个字符串，并根据任何内容拆分对象。

如果你想用关键字“f”分割一个字符串，你可以在`split(“f”)`函数中传递它。

各位，这篇文章到此为止。这些是我们都应该知道的最常用的 Python 快捷方式。一定要好好练习，以便在日常的脚本任务中使用它们。