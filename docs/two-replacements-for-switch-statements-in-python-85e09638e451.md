# Python 中 Switch 语句的两种替代

> 原文：<https://betterprogramming.pub/two-replacements-for-switch-statements-in-python-85e09638e451>

## 。get 和 if/elif/else

![](img/750696e88d369e41f3e7b02a08e51f01.png)

照片由[蒂姆·高](https://unsplash.com/@punttim?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/railroad-switch?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

JavaScript 等其他编程语言使用 switch 语句，这是一个代码块，其中的值与一组事例相匹配。当大小写匹配时，执行该块中的代码(以及其后的所有代码)。可选地，当没有匹配的事例时，默认事例可以用作总括。

Python 中没有 Switch 语句；然而，有一些方法可以模仿他们的行为。这里有两种方法。

# 使用字典函数。获取()

这个方法利用 Python 的字典数据类型来实现类似的结果:匹配一个大小写(键)并使用`.get()`允许一个默认值

```
def switch_dict(x):
   case_dict = {
      "a": 1,
      "b": 2,
      "c": 3
   } default_value = -1 return case_dict.get(x, default_value)
```

为了优化我们的代码并进一步精简它，我们可以这样写:

```
def switch_dict(x):
   return {
      "a": 1,
      "b": 2,
      "c": 3
   }.get(x, -1)
```

注意字典在函数中，这可能更有效，但是这样更容易阅读和维护。

# 用 if/elif/else 语句重构

使用一系列的`if`、`elif`和`else`语句来重新创建 switch 语句，这是一种古老而有益的方法。

```
def switch_dict(x):
   if x == "a":
      return 1
   elif x == "b":
      return 2
   elif x == "c":
      return 3
   else:
      return -1
```

这似乎比简单的字典更费力；但是，它确实有优点。如果您需要在每种情况下执行多个操作，那么使用 If 语句将更容易管理。此外，if 语句将有利于允许更复杂的匹配条件。