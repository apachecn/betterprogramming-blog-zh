# 如何使用 Python 字符串文字修饰符

> 原文：<https://betterprogramming.pub/how-to-use-python-string-literal-modifiers-9e8021b2c415>

## 学习这个修改 Python 字符串行为的简单技术

![](img/55ab4025c7ff890be2735e512fe64985.png)

照片由[大卫·拉古萨](https://unsplash.com/@davideragusa?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

Python 如此简单而灵活——我喜欢它。这种语言易于阅读，我发现自己在语法和约定方面做了最少的谷歌搜索。

Python 字符串文字修饰符是直接在字符串前面的字母。对于外行人来说，它们看起来像是错别字。他们实际做的是改变字符串的行为——取决于使用的字母。

让我们介绍每个修改器并演示修改结果。

# Unicode 字符串修饰符

此字符串修饰符仅适用于 Python 2.x。在字符串开头使用字母`u`会将其转换为 Unicode，以占用更多内存为代价解锁更宽的字符集。

```
sys.getsizeof("hello world")  # 48
sys.getsizeof(u"hello world") # 96
```

对于 Python 3.x，默认情况下字符串是 Unicode 的，所以这个修饰符没有任何作用。

# 字节串修饰符

这个字符串修饰符与 Unicode 字符串修饰符相反。通过将字母`b`放在字符串的开头，它将被转换成 ASCII。以更小的字符集为代价，这个字符串将占用更少的内存。

```
sys.getsizeof("hello world")  # 96
sys.getsizeof(b"hello world") # 48
```

如果您还不明白，字节字符串修饰符只适用于 Python 3 . x——因为 Python 2.x 字符串默认为 ASCII 格式。

# 原始字符串修饰符

在一个字符串前使用字母`r`来表示该字符串——一个字符一个字符地——原样。这意味着转义字符不会被表示为特殊字符，而是表示为纯文本。

```
print("hello\nworld")
"""
hello
world
"""print(r"hello\nworld")
"""
hello\nworld
"""
```

# 格式字符串修饰符

格式字符串修饰符是在 Python 3.6 中引入的，它允许您在一个字符串中表达变量。

```
name = "Jonathan"
print(f"Hello {name}") # Hello Jonathan
```

在字符串中打印变量有多种方法，但是 f-strings 已经成为我的首选方法。只要确保你运行的是 3.6+版本的代码，否则你会收到一个语法错误。