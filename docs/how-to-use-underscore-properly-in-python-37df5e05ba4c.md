# 如何在 Python 中正确使用下划线(_)

> 原文：<https://betterprogramming.pub/how-to-use-underscore-properly-in-python-37df5e05ba4c>

## 你可能没有意识到它的所有含义

![](img/1ee1624fd15bc2e4d5ef3e14c32b561e.png)

照片由[在](https://unsplash.com/@drew_beamer?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上绘制光束

下划线或下划线(`_`)在 Python 中有许多用途，在开始这篇文章之前，我对其中每一个都有一些误解。我做了假设，阅读了不准确的信息，甚至从我参加的在线 Python 课程中收到了一个不完整的故事。

在这篇文章中，我将演示下划线的用法，并就如何处理它给出我非常偏颇的建议。

在本文中，我将介绍`_`的以下用法:

*   它可以优雅地用作循环和赋值语句中的临时变量。
*   单(`_`)和双(`__`)下划线都可以在类变量名前添加，以表明该变量应该是私有的。它能让你的变量保密吗？有什么区别？
*   它可以在函数名前面加上前缀，以表明函数应该是私有的。下划线让你的函数保持私有吗？

# 1.下划线作为临时变量

`_`可以在循环和赋值语句中代替“虚拟”变量，在这些语句中，函数返回多个值，而你想忽略其中的一个或多个值。

## 在循环中使用

如果你需要 Python 做一件事五次，最明显的方法是在 for 循环中使用一个虚拟变量，如下所示。当我使用虚拟变量时，我巧妙地称它为… `dummy`。下面显示的这种语法的缺点是我不得不创建一个变量名，并希望任何阅读我的代码的人都清楚我再也不会使用`dummy`。

在这个简短的代码片段中很容易看出这一点，但是如果循环更长更复杂，就不那么明显了。

```
for dummy in range(5):
    print("Hello world")
```

我可以用`_`代替。下面的代码片段使用`_`来达到同样的结果，同时向任何阅读代码的人发出信号，我不打算在代码中的任何地方使用循环控制变量。

```
for _ in range(5):
    print("Hello world")
```

我喜欢这种语法，并鼓励其他人使用它。然而，我没有意识到 Python 像跟踪任何其他变量一样跟踪`_`的值！考虑下面的片段；它打印整数`0`到`4`:

```
for _ in range(5):
    print(_)
```

下划线甚至可以在嵌套循环中工作，但是跟踪`_`变量的值会变得更加混乱。考虑下面这个循环。你认为这段代码会产生什么？您认为这段代码完成后会在`_`中存储什么？

```
for _ in ['A','B','C']:
    print(_, end=": ")
    for _ in range(4):
        print(_, end=",")  # what will this line print?
    print()                # newline
```

这是上面代码的输出:

```
A: 0,1,2,3,
B: 0,1,2,3,
C: 0,1,2,3,
```

也许上面的输出没有让你惊讶，但是让我惊讶了。当嵌套的 for 循环完成时，你认为`_`代表什么值？它会包含有效值吗？

```
for _ in ['A','B','C']:
    pass
    for _ in range(4):
        passprint(_)               # what will this line print?
```

我很惊讶上面的代码打印的是`3`。我期待它是无效的或打印`C`。正在发生的事情其实很简单。for 循环的每次迭代都将`_`赋给迭代器中的下一个值。外部 for 循环简单地将`_`赋给`[‘A’,'B’,’C']`中的下一个值，内部 for 循环将`_`赋给迭代器`range(4)`中的下一个值。

这种困惑让我想到了我的第一个建议:

> 不要使用下划线作为函数或操作的输入。如果你需要跟踪一个变量，花时间给它一个描述性的名字。

## 忽略返回值

一些 Python 函数返回多个值，但我通常不需要所有这些值。`_`表示我忽略了那个值。假设我们有一个返回数据集的均值、众数和标准差的函数，但是我们现在只关心标准差。我们可以这样写:

```
_, _, stdev = myfunction(large_dataset)
```

上面的代码告诉任何阅读我的代码的人，我只打算使用由`myfunction`返回的三个值中的一个。比起声明两个永远不会用到的额外变量，我更喜欢这个语法。然而，在幕后，Python 存储了`_`的值—但是是哪个呢？当一行给`_`分配了两个不同的值时，你认为下面的代码会打印出什么？

```
_, _, stdev = myfunction(large_dataset)
print(_)       # what will this line print?
```

上面的代码将显示由`myfunction`返回的第二个值。我不知道会发生什么，直到我在 Python REPL 中进行了如下测试:

```
>>> _, _, stdev = (1, 2, 3)
>>> print(_)
2
```

这种困惑让我稍微修改了一下我之前给出的建议。

> 使用下划线可以忽略函数返回的值。不要使用下划线作为函数或操作的输入。如果你需要跟踪一个变量，花时间给它一个描述性的名字。

# 2.下划线表示类属性是私有的

使用下划线向那些阅读和使用代码的人发出信号，您希望变量是私有的，但遗憾的是 Python 并没有强制执行您的意图。考虑下面这个荒谬的 Python 类，它定义了两个局部变量，一个以单下划线为前缀，另一个以两个下划线为前缀。

```
class MyClass:
    def __init__(self):
        self._myprivate = 12
        self.__myreallyprivate = 42
```

使用这个类的任何人都可以很容易地直接访问这些变量，即使我希望这些变量是私有的。Python 对单下划线和双下划线的处理是不同的(我一会儿会解释这一点)，但是在 Python 中没有办法像在 Java 或 C++中那样真正使类变量私有。下面两行代码将显示这两个变量都可以直接读写:

```
instance = MyClass()
print(dir(instance))
```

类实例的`dir`列表显示了`_myprivate`(单下划线)和`__myreallyprivate`(双下划线)之间唯一的区别是`__myreallyprivate`稍微有些模糊。我可以像这样覆盖作者原本打算私有的两个变量:

```
instance._myprivate = 'overwritten!!'
instance._MyClass__myreallyprivate = 'overwritten!!'
```

你可能想知道如果用一个简单的`dir`或者通过阅读 Python 文档就能很容易地检测出`__myreallyprivate` 的混淆的目的是什么。当您创建具有相同局部变量名的子类时，这种混淆会很方便。

我将用一个更简单的超类和子类的例子来说明这一点，它们都实现了变量`__private`。考虑以下类定义:

```
class Parent:
    def __init__(self):
        self.__private = 42class Child(Parent):
    def __init__(self):
        super().__init__()
        self.__private = 52
```

上面的子类将有两个模糊变量。我可以用下面的代码显示这些变量:

```
mychild = Child()
print(mychild._Parent__private)   # prints 42
print(mychild._Child__private)    # prints 52
```

这些关于类变量名中的单下划线和双下划线的观察给了我一些建议:

如果您是某个类的作者:

*   为您希望类的使用者读取的每个局部变量提供一个“get”方法。
*   使用下划线表示变量应该被认为是私有的。

如果你是其他人写的类的消费者:

*   尽可能避免直接访问类局部变量。
*   绝对避免直接访问带有下划线的类变量。作者将下划线(或一对下划线)放在那里是有原因。

# 3.下划线表示函数是私有的

与类变量一样，`_`用于表示将函数私有的意图，但是 Python 也不强制这样做。考虑一个名为`my_functions.py`的 Python 文件，其函数定义如下:

```
def _private():
    return 'Hello world'
```

如果我用类似于`import my_functions`的通用导入语句导入这个 Python 模块，那么我仍然可以很容易地访问我试图标记为私有的模块。这根本不是函数`_private`的作者想要的。

```
>>> import my_functions
>>> my_functions._private
'Hello world'
```

任何函数——甚至私有函数——都可以显式导入，如下所示。同样，这根本不是函数`_private`的作者想要的。

```
>>> from my_functions import _private
>>> _private()
'Hello world'
```

我甚至可以为私有函数创建一个别名，这样我就不会经常被提醒我正在使用私有函数。

```
>>> from my_functions import _private as p
>>> p()
'Hello world'
```

有趣的是，私有函数唯一不被导入的时候是使用通配符的时候，但是 [PEP8](https://peps.python.org/pep-0008/#imports) 风格指南不鼓励使用通配符，因为它会在名称空间中产生冲突或混淆。

```
>>> from my_functions import *
>>> _private()
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
NameError: name '_private' is not defined
```

不管 Python 允许什么，不允许什么，我的最后一条建议是:

> 即使 Python 不阻止您这样做，也要避免导入已被标记为私有的函数。

# 最后的想法

我所有关于`_`的建议都是围绕着写代码时表达你的意图和使用他人代码时尊重他人的意图。然而，尊重其他作者的意图并不意味着礼貌。这个建议是关于保护你自己的。

我在本文中给出的例子简单易懂。当您使用更复杂的代码时，可能会有您没有预料到的行为。在某些情况下，私有函数或私有变量的行为可能与您预期的不同。

此外，实现这些变量或函数可能会在未来的代码版本中发生变化。毕竟，该代码的作者将它们标记为私有，因此可以随时自由地更改它们。

所以你要保护好自己，避免使用标记为私有的函数和类变量！

希望这篇文章中的一些内容能让你感到惊讶。快乐强调！