# 如何在 Python 中打印带有自定义分隔符的 Iterable

> 原文：<https://betterprogramming.pub/how-to-print-an-iterable-with-a-custom-delimiter-in-python-613e2f6c2075>

## 更好的调试

![](img/affa13f06a3989177845ce5e1946e4fa.png)

卡斯帕·卡米尔·鲁宾在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

人们很容易将打印等简单任务视为理所当然。您将很快发现 Python 自带的 [PrettyPrint](https://medium.com/better-programming/how-to-pretty-print-in-python-9b1d8764d151) 模块，它只需要一个简单的`import`。

然而，用一个简单的`print()`函数还可以做更多的事情。在这篇文章中，我们将介绍一种使用自定义分隔符打印可重复项的技术。我们将获得如下输出:

```
P y t h o n
P,y,t,h,o,n
P
y
t
h
o
n
```

我们开始吧！

# Splat 运算符

虽然官方称它为 splat 操作符(取自 Ruby 的一个术语),但星号符号在函数调用中使用时，会对 iterable 进行解包。

```
print(*"Python") # P y t h o n
```

有关`*`或`**`操作符的更多信息，请查看以前的文章[Python 中的*args 和**kwargs 是什么？](https://medium.com/better-programming/what-are-args-and-kwargs-in-python-6aaf9e3cad73)

# sep 参数

许多人不知道的是，`print()`函数实际上有可选的参数来修改它的行为。默认情况下，`sep`参数设置为不间断空格。

通过打印两个字符串，我们可以看到`sep`的效果，一个没有传递参数，另一个带有自定义分隔符。

```
print("Hello","World")         # Hello World
print("Hello","World",sep=":") # Hello:World
```

# 解决方案

为了打印带有自定义分隔符的 iterable，我们将结合 splat 操作符并传递一个自定义分隔符。

```
msg = "Python"
print(*msg, sep=",") # P,y,t,h,o,n
```

如果我们想逐行打印每个字符，使用换行符作为自定义分隔符。

这种技术并不局限于字符串。如果要打印列表、元组或集合，请使用相同的组合。

# 结论

你最喜欢的解包 iterables 或使用自定义分隔符的真实用例是什么？感谢您的阅读，感谢您的支持。