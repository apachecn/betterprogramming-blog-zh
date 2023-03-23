# Python 运算符重载

> 原文：<https://betterprogramming.pub/how-to-handle-operator-overloading-in-python-8ada8df7e448>

## 在不同的上下文中赋予普通运算符另一种含义

![](img/2d749f0ee9d8758c940aeab1ff64c0f7.png)

[马丁·施瑞德](https://unsplash.com/@martinshreder?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Python 中的运算符重载意味着在不同的上下文中赋予普通运算符另一种含义。

例如，您可以用加法运算符`+`将两个整数相加:

```
1 + 2
```

但是在默认情况下，您不能对自定义类的实例求和:

```
apple + banana
```

但是你可以改变这一点。要对自定义对象使用加法运算符`+`，需要重载运算符`+`。

今天，您将学习如何重载操作符，使 Python 操作在您的对象上成为可能。

# Python 中的特殊方法

在开始学习 Python 中的运算符重载之前，理解特殊方法(也称为双下划线方法或 dunder 方法)的概念很重要。

特殊方法最简单的例子是一个类的`__init__`方法。当创建类的新实例时，调用此方法。

例如:

上面代码中的最后一行运行了`Bill`类的`__init__`方法。在这种情况下，它为实例赋值。

让我们试着打印实例:

```
print(ten_dollars)
```

输出:

```
**<__main__.Bill object at 0x7fd2bcf99d00>**
```

这种明确的输出毫无意义。如果您想打印默认的金额，您可以提供一个名为`__str__`的特殊方法的实现。它定义了对象的字符串表示。每当您对一个对象调用`print`时，这个方法都会被调用。

例如:

输出:

```
I am a 10 dollar bill
```

所以，打电话

```
print(ten_dollars)
```

与以下内容相同:

```
print(ten_dollars.__str__())
```

现在您已经了解了 Python 中特殊方法的工作原理。让我们跳到操作符重载。

# Python 中的运算符重载

Python 运算符适用于内置类型。但是，同一个运算符在不同的内置类型中可能表现不同。

例如，`+`运算符执行数字的加法。但是在字符串的上下文中，`+`操作符连接两个字符串。这是通过运算符重载实现的。

运算符重载在不同的上下文中赋予运算符不同的含义。

例如，让我们看看如果我们创建一个`Bill`类并添加它的两个实例会发生什么:

输出:

```
TypeError: unsupported operand type(s) for +: 'Bill' and 'Bill'
```

错误消息指出不支持将`Bill`实例与`+`实例相加。

这是因为 Python 不知道如何添加`Bill`实例。所以我们必须在账单的上下文中定义`+`的含义。运算符重载会发生这种情况。

为了重载一个操作符`+`，你需要在你的类中提供一个特殊方法的实现。

无论何时使用`+`，都会调用一个叫做`__add__`的特殊方法。

因此，如果您想在两个账单上使用`+`，在`Bill`类中实现`__add__`方法并定义预期的行为:

输出:

```
15
```

现在您可以添加两个`Bill`的实例。`+`操作符调用`__add__`方法，该方法知道如何将`Bill`的两个实例相加。

所以，打电话

```
five_dollars + ten_dollars
```

相当于

```
five_dollars.__add__(ten_dollars)
```

现在可以把账单加起来了。但是也许你希望能够以类似的方式减去账单。为此，在`Bill`类中实现名为`__sub__`的特殊方法:

输出:

```
8
```

这与`__add__`方法的工作方式完全相同，除了它从彼此中减去钞票的值。

现在你可以做加法和减法，你可能也想做乘法。为此，在`Bill`类中实现名为`__mul__`的特殊方法:

输出:

```
50
```

而且既然你会乘法，也许你也想除法。为此，在`Bill`类中实现一个名为`__truediv__`的特殊方法:

输出:

```
2.0
```

太棒了。现在你可以用一个`Bill`的实例做基本的数学运算，这都要感谢操作符重载。

我们可以在这里结束，但我想给你看一样东西。

现在您有了一个支持加减乘除的`Bill`类。但是它不理解比较，例如:

```
ten_dollars >= five_dollars
```

让我们使用运算符重载来解决这个问题。

# Python 中的比较运算符重载

让我们继续与`[Bill](https://gist.github.com/artturijalli/0b7d862bc6a501058469baefd8a1b4ee)`类合作。你现在可以用`Bill`实例做基本的数学运算，但是你不能比较它们。

例如:

输出:

```
TypeError: '>' not supported between instances of 'Bill' and 'Bill'
```

错误信息很清楚。不支持将`Bill`实例与运算符`>`进行比较。

为了让您的`Bill`实例支持大于比较，您需要依靠操作符重载。

在这样做之前，您需要理解`>`是如何工作的。

当您在 between 之间使用`>`时，一个叫做`__gt__`的特殊方法被称为 under the hood ( `gt`的意思是大于)。为了让`Bill`实例支持`>`比较，它必须实现`__gt__`方法。

为此，将以下内容添加到您的`Bill`类中:

现在您可以测试它是否有效:

输出:

```
Ten is more than five
```

很好，有用。

这是一个很好的概括引擎盖下的比较

```
ten_dollars > five_dollars
```

它相当于

```
ten_dollars.__gt__(five_dollars)
```

因此,`>`操作符调用底层的`__gt__`,它比较钞票的`.value`属性。

现在可以使用`>`检查一个账单是否大于另一个账单。但是它不支持更多的比较，即`<`、`<=`、`>=`、`==`、`!=`。

为了使`Bill`支持所有的基本比较，您需要用各自的特殊方法重载每个比较操作符:

以下是运算符和相关的特殊方法:

*   `<` : `__lt__`
*   `>` : `__gt__`
*   `<=` : `__le__`
*   `>=` : `__ge__`
*   `==` : `__eq__`
*   `!=` : `__ne__`

让我们将这些实现到`Bill`类中:

## __lt__

为了使`Bill`支持比`<`更少的比较，将下面的方法添加到类中:

```
**def** __lt__(self, otherBill):
    **return** self.value < otherBill.value
```

## __le__

为了使`Bill`支持小于或等于与`<=`的比较，在类中添加以下方法:

```
**def** __le__(self, otherBill):
    **return** self.value <= otherBill.value
```

## __eq__

为了使`Bill`支持等同于操作符`==`，在类中添加以下方法:

```
**def** __eq__(self, otherBill):
    **return** self.value == otherBill.value
```

## __ne__

为了使`Bill`支持不等于运算符`!=`,将下面的方法添加到类中:

```
**def** __ne__(self, otherBill):
    **return** self.value != otherBill.value
```

## __gt__

这个你已经做过了，但是为了完整起见，这里是实现:

```
**def** __gt__(self, otherBill):
    **return** self.value > otherBill.value
```

## __ge__

为了使`Bill`支持大于或等于操作符`>=`，在类中添加以下方法:

```
**def** __ge__(self, otherBill):
    **return** self.value >= otherBill.value
```

厉害！现在你的`Bill`类应该看起来像[这个](https://gist.github.com/artturijalli/a6190a1f940a46cad3978a720ccc7641)。它现在支持基本的数学和比较。

示例到此结束，本指南到此结束。我希望你喜欢它！

# 结论

Python 操作符重载意味着在不同的上下文中赋予普通操作符另一种含义。

例如，`+`运算符用于将两个整数相加。但是它也可以被重载以支持添加两个自定义对象。

运算符重载是通过为类中的特殊双下划线方法提供实现来实现的。例如，为了支持使用`+`的加法，您需要在您的类中实现`__add__`方法。

感谢阅读。编码快乐！

我很想加入你的 LinkedIn 网络。在 [Artturi Jalli](https://www.linkedin.com/in/artturi-jalli-29619413a) 随意连接。

# 进一步阅读

[](/50-python-interview-questions-and-answers-5230fe2a0db6) [## 50 Python 面试问答

### Ace 您的下一次编码面试

better 编程. pub](/50-python-interview-questions-and-answers-5230fe2a0db6) [](/10-useful-python-snippets-to-code-like-a-pro-e3d9a34e6145) [## 10 个有用的 Python 片段，让你像专业人士一样编写代码

### 我每天使用的有用的提示和技巧

better 编程. pub](/10-useful-python-snippets-to-code-like-a-pro-e3d9a34e6145) 

# 参考

 [## 文件

### 欢迎光临！这是 Python 3.9.6 的文档。

docs.python.org](https://docs.python.org/3/)