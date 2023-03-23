# Python 中有哪些 Try/Except 语句？

> 原文：<https://betterprogramming.pub/how-to-start-using-try-statements-in-python-5043fe69058d>

## 保护代码的介绍

![](img/e7c8c7b2a5e1260413be3b1f0c25efb5.png)

卡斯帕·卡米尔·鲁宾在 [Unsplash](https://unsplash.com/s/photos/free-code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

当我们开始编程时，我们的脚本简单明了，最重要的是，我们倾向于假设一个完美的操作环境。每一个变量都会一直存在，数据也会适当地形成。要是现实世界这么可靠就好了！

事实是，如果某件事会失败，它就会失败。对任何程序员来说，为这些失败做好准备都是一个重要的里程碑。

在 Python 中——以及大多数其他语言——我们使用`try`语句来保护我们的代码。

让我们了解一下 try 语句的作用以及如何开始使用它们！

# 什么是例外？

在我们深入研究 try 语句的语法和实现之前，确定什么是异常是很重要的。

异常是 try 语句中的代码失败的结果。把一个异常想象成你的代码被侮辱——“我反对这个请求！”

有各种各样的异常类型，我们将通过本教程介绍其中的一些。

# 如何使用 Try 语句

try 语句通常被称为 try 块，实际上至少由两部分组成:`try`和`except`。

我们想要保护的代码放在块的`try`部分。之后，我们定义当`except`中出现问题时会发生什么。

```
try:
   print(x)
except Exception:
   print("Something broke!")# Something broke!
```

在上面的例子中，我们使用 catch-all 值`Exception`将任何异常发送到块的这一部分。我们还打印了一个模糊的信息。但是如果我们想要来自 Python 的实际错误消息呢？

您可以在一个 try 中定义多个 except 块，指定应该路由到每个块的确切异常类型。此外，您可以将错误消息设置为一个变量——`e`很常见——以便在您的程序中使用。

```
try:
   print(int(x))
except NameError:
  print("The variable is undefined")   
except Exception as e:
   print(e)# with x undefined => The variable is undefined
# with x as 'text' => invalid literal for int() with base 10: 'text'
```

现在我们开始工作了！我们的代码是安全的、有组织的，并且易于扩展。

让我们看看这个任务是收集整数输入的`while`循环是什么样子的。

```
while True:
  try:
    num = int(input("Enter an int: "))
    break
  except Exception as e:
    print(e)print("Your number is",num)# Enter an int: a
# invalid literal for int() with base 10: 'a'
# Enter an int: 1.1
# invalid literal for int() with base 10: '1.1'
# Enter an int: 1
# Your number is 1
```

# 不同的尝试/例外变体

到目前为止，我们已经使用了一个`try` / `except`，甚至一个`try` / `except` / `except`，但这只是故事的三分之二。

一个`try`块还有另外两个可选段:`else`和`finally`。这两个可选的区块将在`try`和`except`之后**出现。此外，没有什么可以阻止你在一个语句中同时使用`else`和`finally`——但是如果你这样做的话，保持它们的顺序。**

让我们逐个检查一下，看看它们是如何扩展简单的`try/except`的行为的。

## 试试/Except/Else

当将`else`语句附加到`try/except`的末尾时，该代码将在`try`的完成后**执行，但只有在**没有异常发生**时才会执行。**

我们可以举前面的例子，提示用户输入一个整数，并使用一个`else`块来感谢用户的有效输入和跳出`while`循环。

```
while True:
  try:
    num = int(input("Enter an int: "))
  except Exception as e:
    print(e)
  else:
    print("Thank you for the integer!")
    break# Enter an int: a
# invalid literal for int() with base 10: 'a'
# Enter an int: 3
# Thank you for the integer
```

## 尝试/例外/最后

当将`finally`语句附加到`try/except`的末尾时，该代码将在`try`**完成后**执行，不考虑异常**。**

同样，我们将使用前面的示例并添加一个简单的计数器来说明这种行为。

```
count = 0
while True:
  try:
    num = int(input("Enter an int: "))
    break
  except Exception as e:
    print(e)
  finally:
    count += 1
    print("Attempt #:",count)# Enter an int: a
# invalid literal for int() with base 10: 'a'
# Attempt #: 1
# Enter an int: 3
# Attempt #: 2
```

这可能看起来有点奇怪，因为`break`仍然在`try`内部。有理由认为`finally`在适当的输入下会被缩短，然而事实并非如此。无论`try`如何退出，`finally`部分仍将执行。

*把我们在这里演示的东西拿来，形成一个* `*try/except/else/finally*` *陈述，这是很小的一步。我相信你不会有任何问题，所以我把它留给你去试验和尝试。*

*如果你习惯于使用* `*try*` *语句，你最常见的用例是什么？下面分享一下你的经历吧！*