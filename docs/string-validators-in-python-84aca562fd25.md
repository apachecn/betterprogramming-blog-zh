# Python 中的字符串验证器

> 原文：<https://betterprogramming.pub/string-validators-in-python-84aca562fd25>

## 便捷快捷方式的简短指南

![](img/dac4b0bf7ff22220f08ad9e0191b254d.png)

由 [Katerina Limpitsouni](https://twitter.com/ninalimpi) 创作

很多时候，我们使用字符串，并希望快速检查它们是否属于特定类型或包含特定数据。虽然我们可以通过将字符串的字符存储在数据结构中或者显式地写下语句进行验证来做到这一点，但是 Python 中的字符串验证器可以简化我们的任务。这在验证密码字符串或任何其他用户输入时非常有用。字符串验证器本质上是允许我们检查一个字符串是否满足特定标准的函数。

这篇文章是对字符串验证器的介绍，并假设没有关于它们的知识。然而，需要具备 Python 编程的基础知识。让我们开始吧。

# **检查给定字符串是否为字母数字**

如果字符串的所有字符都是数字(0–9)或字母(大写或小写)，则该字符串是字母数字字符串。方法`str.isalnum()`检查字符串`str`是否是字母数字。例如:

```
"aBcD123".isalnum() #True
"ab*".isalnum() #False
"****".isalnum() #False
```

请注意，该方法检查字符串中的所有字符是否都是字母数字。因此，即使包含非字母数字字符和字母数字字符，它也返回 false。由于空字符串不包含任何数字或字母，`str.isalnum()`将返回 false:

```
"".isalnum() #False
```

# **检查给定字符串是否是字母**

如果一个字符串的所有字符都是字母(大写或小写)，那么它就是字母字符串。方法`str.isalpha()`检查字符串`str`是否是字母。例如:

```
"abc".isalpha() #True 
"a98".isalpha() #False
"".isalpha() #False
```

与`str.isalnum()`类似，`str.isalpha()`应用于空字符串时返回 false。这很直观，因为空字符串不包含任何字母。

# 检查给定的字符串是否全是数字

方法`str.isdigit()`检查字符串`str`的所有字符是否都是数字(0–9)。例如:

```
"000".isdigit() #True
"ab0".isdigit() #False
"".isdigit() #False
```

# **检查给定字符串是否小写**

方法`str.islower()`检查给定字符串`str`的所有大小写字符是否都是小写。因此，即使有一个大写字母，它也返回 false，如果没有，则返回 true。例如:

```
"000".islower() #False 
"ra8".islower() #True
"rA".islower() #False
"r*".islower() #True
"".islower() #False
```

# **检查给定字符串是否大写**

方法`str.isupper()`检查给定字符串`str`的所有大小写*字符是否都是大写的。因此，即使有一个小写字母，它也返回 false，如果没有，则返回 true。例如:*

```
"000".isupper() #False 
"RA8".isupper() #True
"rA".isupper() #False
"R*".isupper() #True
"".isupper() #False
```

# **检查给定字符串是否为空**

这是构建 web 表单时的常见场景。方法`str.isspace()`检查给定的字符串是否全是空白。例如:

```
"".isspace() #False
"   ".isspace() #True
"*".isspace() #False
```

直观上，我们称这种方法为“is blank”

# **检查给定字符串的标题是否大写**

方法`str.istitle()`检查给定的字符串是否在[标题框](https://titlecase.com/)中。在标题的情况下，所有的单词都应该以大写字母开头，并且所有的单词都应该用一个空白字符隔开。

例如:

```
"000".istitle() #False
"John".istitle() #True
"john".istitle() #False
"CamelCase".istitle() #False
"Camel Case".istitle() #True 
```

就是这样！我们已经介绍了字符串验证器，并了解了它们是如何工作的。现在，您可以通过解决 HackerRank 的以下问题来验证您的知识(而不是字符串):

[](https://www.hackerrank.com/challenges/string-validators/problem) [## 字符串验证器| HackerRank

### Python 内置了针对基本数据的字符串验证方法。它可以检查一个字符串是否由字母…

www.hackerrank.com](https://www.hackerrank.com/challenges/string-validators/problem) 

希望这篇文章对你有帮助。黑客快乐！