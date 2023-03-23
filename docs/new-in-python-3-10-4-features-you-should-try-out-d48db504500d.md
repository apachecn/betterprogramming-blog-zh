# Python 3.10 中的新特性:4 您应该尝试的特性

> 原文：<https://betterprogramming.pub/new-in-python-3-10-4-features-you-should-try-out-d48db504500d>

## 期待已久的 switch 语句终于来了！

![](img/7185fcefac2c5b49d280a99e17cfcb4d.png)

照片由 [Kira auf der Heide](https://unsplash.com/@kadh?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

Python 因其简单和优雅而受到许多数据科学家和通用开发人员的喜爱。Python 的最新版本(3.10)目前处于测试阶段，但在 2021 年 10 月最终发布之前不会添加新功能。现在正是提前了解其新特性的时候。

# 多个上下文管理器

我们列表中的第一项是 Python 3.10 中带括号的上下文管理器。作为复习，上下文管理器是特殊的代码构造，允许简单处理资源(例如文件):

```
with open('output.log', 'rw') as fout:
    fout.write('hello')
```

现在，有了带括号的上下文管理器，您可以在一个`with`块中使用多个上下文管理器:

```
with (open('output.log', 'w') as fout, open('input.csv') as fin):
    fout.write(fin.read())
```

这个特性对于使用异步资源的代码非常有用，因为您不再需要多个`with`语句。

# 有用的错误消息

新的 Python 3.10 更新也给我们带来了更多有用的错误消息。其中包括`SyntaxError`、`IdentationError`、`AttributeError`、`NameError`。例如，当您拼错一个变量名时，Python 会建议另一个名称:

```
>>> number_of_cars = 5 
>>> number_of_cats Traceback (most recent call last): 
    File "<stdin>", line 1, in <module> 
NameError: name 'number_of_cats' is not defined. Did you mean: number_of_cars?
```

同样的逻辑也适用于对象的属性。如果你在对象的属性中犯了一个错误，解释器会尽力帮你改正。其他错误消息现在包括丢失的括号:

```
users = {'abacaba': '123', 'ozzy': 'qwerty', 
print(users) File "example.py", line 1 
    users = {'abacaba': '123', 'ozzy': 'qwerty', 
            ^ 
SyntaxError: '{' was never closed
```

对错误信息还有很多其他的改进，包括少了逗号，少了`:`，把`=`和`==`搞混了等等。

# 结构模式匹配

这是一个大的。我们已经要求 switch 声明有一段时间了，所有的等待都是合理的。在我看来，这些实现的方式真正代表了 Pythonic 式的方式。这是一个简单的例子:

在这种情况下，我们评估的是`name`变量。如果匹配`"Misha"`或`"John"`，会返回一个问候语。所有其他名字将被告知走开。这可以用`|`符号简化:

您可以使用`if`语句为匹配添加附加条件:

这个特性对于那些处理大型多轴数据库的人来说特别有用，可以快速地对他们的数据进行分类。

# 类型联合运算符

你们中的许多人可能知道 Python 支持类型提示。它们不保证类型安全，但在开发中是一个有用的工具。我们将专门讨论类型联合，或者为一个变量匹配多个类型。在 Python 3.10 之前，您必须使用来自`typing`的特殊`Union`类型:

```
def square_root(number: Union[int, float]) -> Union[int, float]: 
    return number ** 0.5
```

现在，使用 Python 3.10，您可以编写:

```
def square_root(number: int | float) -> int | float: 
    return number ** 0.5
```

这个语法也适用于`isinstance`函数:

```
>>> isinstance('abacaba', int | str) 
True
```

遵循类型提示范例的代码库将极大地受益于这一新特性。

# 结束语

感谢您阅读我的文章。我希望你喜欢它。请在评论区让我知道你最喜欢的 Python 3.10 更新特性！

如果你没有赶上所有的 Python 更新，看看我对 Python 3.9 特性的[评论。](/take-a-look-at-the-awesome-new-features-coming-in-python-3-9-8753c494de39)