# 在 Python 中，停止使用“或”来测试一个变量的多个值

> 原文：<https://betterprogramming.pub/stop-using-or-to-test-multiple-values-against-a-variable-in-python-abe0a77c287a>

## 使用“in”关键字进行多次比较

![](img/7561772d59736144132d631431e8219b.png)

弗拉基米尔·库迪诺夫在 [Unsplash](https://unsplash.com/s/photos/symetry?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

如果您曾经需要检查一个变量是否等于许多可能值中的一个，您可能已经编写了类似这样的代码:

```
if x == 1 or x == 10 or x == 100:
  pass
```

嗯，有更好的办法！

让我们来看看如何使用`in`关键字而不是`or`来重新创建上面的表达式。

# 什么是“in”关键字？

关键字`in`可用于搜索整个序列，并根据是否找到左操作数返回`True`或`False`。

这里有一个简单的例子，检查字符串中的字母`e`:

```
result = 'e' in 'The'
print(result) # True
```

由于`in`关键字可以用于任何序列，我们将所有与变量进行比较的值放在一个序列中。现在的问题是，哪种数据类型是最好的？

最初的反应可能是使用一个列表:

```
if x in [1,10,100]:
  pass
```

这很好，尽管我们可以通过使用元组甚至集合来获得更好的性能:

```
# tuple example
if x in (1,10,100):
  pass# set example
if x in {1,10,100}:
  pass
```

使用`in`关键字不仅减少了最初输入的语法，而且更易于阅读和维护。随意在单独的行上创建元组，并在`if`语句中使用它，以保持代码的可读性。

```
matches = (1,10,100)if x in matches:
  pass
```

记住，这种技术只适用于等式比较。但是，您可以反转表达式来有效地测试不相等性:

```
if x not in (1,10,100):
  pass
```

当简单变得霸道时，想办法写出更简洁、易读、易维护的代码。

我希望这是有帮助的，在下面分享你的想法和评论！