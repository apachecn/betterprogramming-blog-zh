# 如何在 Python 中无限期地请求用户输入直到有效

> 原文：<https://betterprogramming.pub/how-to-indefinitely-request-user-input-until-valid-in-python-388a7c85aa6e>

## 无限循环和 try/except 语句的分步指南

![](img/0611440456a61e463cb814ea703a493f.png)

[西格蒙德](https://unsplash.com/@sigmund?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/form?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

用户输入是一件棘手的事情。我很早就知道，仅仅因为你提示用户的“电子邮件地址”或“年龄”，你不会得到一堆乱码。

构建时，你必须假设用户会把事情搞砸…

为了让我们的脚本在恶意和愚蠢中运行，我们需要建立适当的机制来请求用户的命令行输入。

让我们回顾一些基础知识，并为持续请求用户输入建立一个可靠的计划，直到他们输入适当的响应。

# 所需工具

在开始之前，让我们盘点一下我们将在代码中使用的内容。

*   `input()`函数:该函数接受一个字符串参数，这是屏幕上显示的提示。然后，用户将能够提交由该函数返回的响应。
*   `while`循环:我最近写了关于 [while 和 for 循环](https://medium.com/better-programming/how-to-pick-between-a-while-and-for-loop-14ef217c3776)的区别。本质上，我们选择了一个`while`循环，因为我们不知道需要迭代的次数。
*   `try` / `except`语句:`try` / `except`语句将作为我们抵御会抛出异常的值的第一道防线。我在 [try/except](https://medium.com/better-programming/how-to-start-using-try-statements-in-python-5043fe69058d) 上写过类似的入门。
*   `if`带`break`的语句:`if`和`break`语句的组合将帮助我们评估定制条件的有效性——这些条件不会抛出异常，但没有意义。

# 构建循环

好了，现在我们已经介绍了解决方案的四个组成部分，我们将要求用户输入他们的年龄。我们开始工作吧！

## 创建一个无限循环

第一步是创建一个无限循环。通常，当你听到*无限循环，*是在“我不小心写了一个无限循环”的上下文中别担心，这次我们想要一个。

```
while True:
  pass
```

我使用关键字`pass`作为语法占位符。*暂时不要运行这段代码。*

## 添加 try/catch 语句

在我们的无限循环中，第一步是添加我们的`try` / `except`语句。

```
while True:
  try:
    pass
  except Exception as e:
    print(e)
```

这里发生的事情是，语句的`try`部分内的所有内容都将试图被执行。如果抛出任何异常，那么将执行`except`子句。

在我们的例子中，这意味着打印错误消息，它被分配给变量`e`。

## 请求用户输入

现在是时候要求用户输入了。我们将使用`input()`函数，根据定义，它返回一个字符串。我们还将转换成整数，因为我们要求用户的年龄。

```
while True:
  try:
    age = int(input("Enter your age: "))
  except Exception as e:
    print(e)
```

## 定义正例

最后一步，我们将定义有效响应的标准，这样我们就可以退出循环。这里需要定制逻辑，因为 Python 没有“年龄”的概念——解释器不知道负年龄没有意义。

最后，现在我们可以测试我们的代码了！这里有一个输入一些假值的例子。

# 结论

通过这种设置，您可以准确地定制您想要请求的内容。

想用不同的方式处理异常吗？您可以准确地指定您捕获的异常的类型，并独立地处理每个异常。想要更改正值的标准吗？只需更新`if`语句。

您是否有不同的方法来请求满足相同标准的用户输入？下面分享一下你的想法。感谢阅读！