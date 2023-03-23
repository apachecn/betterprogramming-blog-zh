# 如何在 Python 中填充字符串

> 原文：<https://betterprogramming.pub/how-to-pad-a-string-in-python-a792b4e6e2bc>

## 将 9 转换为 09

![](img/0b214d518c7824b16eeb1cbb76a6768b.png)

尼克·希利尔在 [Unsplash](https://unsplash.com/s/photos/numbers?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

当你的数字不一致时，你会烦恼吗？不管输入是什么，你的项目需要一个固定的字符长度吗？如果答案是肯定的，那么您很可能需要填充字符串或数字值。

在 Python 中，这个任务通过`.zfill()` Python 字符串方法变得简单。

# zfill()是什么？

`.zfill()`方法是 string 类的一部分，这意味着它只能在 string 值上执行。

```
# will work
result = "9".zfill(2)# won't work
result = 9.zfill(2)
```

该方法需要一个参数，即字符串的最大长度(如果填充)。

这里有三种可能:填充长度小于、等于或大于原始字符串的长度。让我们看看在每种情况下会发生什么。

```
a = "8"
b = "80"
c = "800"print(a.zfill(2),b.zfill(2),c.zfill(2)) # 08 80 800
```

正如您所看到的，当填充长度大于原始字符串时，0 将被添加到字符串的左侧，直到它符合填充长度。当两者相等时，字符串预计不会改变。当填充长度变短时，原始字符串保持不变，不会缩短以匹配填充长度。

那么当我们的变量不是字符串时会发生什么呢？

在这些场景中，答案就像使用`str()`函数将我们的值转换成字符串一样简单。

```
id = 12345
id = str(id).zfill(8)
print(id) # 00012345
```

# 结论

再也不用担心笨拙的循环和手动填充字符串值。使用`.zfill()`的方法，可靠地离开垫你的字符串。