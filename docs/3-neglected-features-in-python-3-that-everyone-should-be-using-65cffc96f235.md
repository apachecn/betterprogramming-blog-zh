# Python 3 中每个人都应该使用的 3 个被忽视的特性

> 原文：<https://betterprogramming.pub/3-neglected-features-in-python-3-that-everyone-should-be-using-65cffc96f235>

## 枚举、fstrings 和数据类

![](img/5565dba1cb5a9ace1fc965ebf38feeac.png)

[沙哈达特拉赫曼](https://unsplash.com/@hishahadat?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/python?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片。

Python 3 已经问世一段时间了，大多数开发人员——尤其是第一次接触编程的开发人员——已经在使用它了。但是，尽管 Python 3 推出了许多新特性，但似乎有许多新特性是未知的或未被充分利用的。在今天的文章中，我将讨论三个鲜为人知但非常有用的特性。我开始了解并喜欢其他语言的这些特性，我真的认为这些特性让 Python 3 变得很棒。

# 列举

枚举是我在 Java 和 Swift 中经常使用的东西，我对它们的使用扩展到了 Python。

用 Python 声明一个 enum 非常简单，在 Python 3 之前也可以完成(尽管功能更有限):

在上面的代码中，您可以看到通过声明一个类并使其成为`Enum`的子类，可以很容易地声明一个枚举。从这里开始，您只需在下面几行中定义您的每个状态。

在我的例子中，我有`AIR`、`LAND`和`SEA`可用。

Python 3 中增加的功能是执行`.value`和`.name`的能力。这些将允许您获得与状态相关联的整数值或与之相关联的字符串。

在上面的代码中，打印`State.LAND.name`将返回`LAND`，所以功能不仅仅是一个整数。

当您需要常量的描述性表示时，枚举在代码中很有用。比如，与其检查一个状态是`0`还是`1`，不如检查是`State.MOVING`还是`State.STATIONARY`好得多。你的常量可能会改变，如果有人在看你的代码，`MOVING`比`0`更有意义。因此，可读性大大提高。

更多阅读请点击查看`Enum` [上的官方 Python 3 文档。](https://docs.python.org/3/library/enum.html)

# 格式

在 Python 3.6 中添加的`fstrings`是一种格式化文本的好方法。它们提供了更好的可读性，并且不容易出错(我当然喜欢这一点，因为它来自 Java 这样的语言)。

`fstrings`比之前在 Python 中使用的`format`可读性更好。下面是一个使用`format`的例子:

如你所见，字符串中有空括号，然后我们按顺序列出每个变量的名字。

现在在同样的代码上花了很多时间，但是使用`fstring`它可读性更好，非常类似于在 Swift 中格式化一个字符串。

为了完成这个更简洁的字符串，我们只需在引号前加上字母`f`，然后我们不再使用空括号，而是将变量或数据直接放入括号中。因为变量是写在括号内的，所以你不需要计算格式中的项数就能知道什么变量放在哪里——变量就在它们将要被放置的地方。

做`fstrings`比做类似字符串连接或格式化字符串的事情产生更可读和更可靠的代码。

# 数据类别

数据类可能是一个比我接触过的其他主题更晦涩的主题，所以我将简要地解释它们。在 Kotlin 中，数据类是我越来越喜欢的东西，所以我也很喜欢尝试在 Python 中使用它们。

数据类实际上是一个唯一的目的就是保存数据的类。该类将拥有可以访问和写入的变量，但是在它上面没有额外的逻辑。

假设你有一个程序，你在不同的类之间传递一个字符串和一个数字数组。您可以只使用类似`pass(str, arr)`的方法，但是更好的方法是创建一个只包含一个字符串作为字段和一个数组的数据类。

通过创建一个数据类，你正在做的事情会更加清晰，单元测试也会更加容易。

我将举例说明如何创建一个表示三维向量的简单数据类，但这可以很容易地扩展到表示不同数据的任意组合:

在这里，你可以看到一个数据类的定义非常类似于声明一个普通的类，除了我们在它前面使用了`@dataclass`，然后每个字段都像`name: type`一样被声明。

虽然我们创建的`Vector3D`的功能有限，但 data 类的目的只是提高效率，减少代码中的错误。传递一个`Vector3D`比传递`int`变量要好得多。

关于`@dataclass`的更多细节，请点击查看官方 Python 3 文档[。](https://docs.python.org/3/library/dataclasses.html)

# 结论

如果你尝试过这些新功能，请在评论中告诉我！我很想听听您对它们的不同使用案例。编码快乐！