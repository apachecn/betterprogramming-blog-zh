# 经验丰富的开发人员必须了解的 5 个 Python 概念

> 原文：<https://betterprogramming.pub/must-know-python-concepts-for-experienced-developers-4554ceea3d95>

## 深入了解高级 Python 特性

![](img/0bc3bc6dfdf9d8433970b518d81e1066.png)

[安德烈·梅捷列夫](https://unsplash.com/@metelevan?utm_source=medium&utm_medium=referral)在[解锁](https://unsplash.com?utm_source=medium&utm_medium=referral)时拍摄的照片

*这个故事最初是在我的个人博客*[](https://bettersoftwarewithsid.com/must-know-python-concepts-for-experienced-developers)**上发表的，bettersoftwarewithsid.com***。***

****先决条件-*** *本文假设对诸如 OOPs、类、继承等编程概念有中级的理解。**

*开发人员以不同的能力使用 Python 编程语言。许多人用它来对独立的组件进行胶着编码，其他人用它来自动化任务，许多人把它作为他们的主要编程语言。作为一个严肃的 Python 程序员，你需要了解除了基本的 Python 构造之外的概念。*

*下面是一些这样的概念的汇编，没有特别的顺序，这有助于加深对漂亮的 Python 语言的理解。*

# *1.多重继承及其 MRO 处理*

*多重继承是一种 OOPs 构造，其中一个子类可以有多个父类，比如*

```
*class Toddler(ParentA, ParentB):
    pass*
```

*Python 允许多种继承，但是许多其他高级语言如 Java 不允许。*

*这类语言不赞成多重继承，主要是为了避免**钻石问题**。下面是我们`Toddler`类的父类。*

```
*class ParentA:
    def how_to_talk():
        print("Hello")class ParentB:
    def how_to_talk():
        print("Namaste")*
```

*Diamond 问题是当一个子类(`Toddler`)从多个父类(`ParentA`、`ParentB`)继承时出现的一个模糊性问题。由于子类没有重写(有自己的实现)方法(`how_to_talk`)而产生了歧义，这在两个父类中都有不同的实现。实际上，我们的`Toddler`并不确定`how_to_talk`，也就是说`Hello`(按照`ParentA`)或者`Namaste`(按照`ParentB`)！。*

*Python 通过使用方法解决顺序(MRO)来解决菱形问题。顾名思义，MRO 定义了父类中方法查找的顺序。在 Python 中，简单来说就是从下到上，从左到右。因此，在我们的例子中，MRO 将从左到右查看父类，并将首先遇到`ParentA`。所以，我们的`Toddler`会用`ParentA`的`how_to_talk`说`Hello`。*

*准确地说，Python 的 MRO 使用 [C3 线性化](https://en.wikipedia.org/wiki/C3_linearization)算法来解析顺序。该算法保证对于任何复杂的类关系，它将总是给出相同的解决顺序，否则如果不能确定这样的顺序，它将给出失败。*

*值得注意的一点是，Python 在 2.2 版之前有一个不同的 MRO，[从 2.3 版使用 C3 线性化将](https://www.python.org/download/releases/2.3/mro/)移到了新版本。*

# *2.`keyword 'type'`的元类和双重用途*

*注意:我们将只讨论 Python3 的类和元类概念。*

*你一定在 OOPs concepts 中学习过，一个`Class`可以被认为是一个模板，描述了从它们创建的所有`Object`的属性和行为。在 Python 中，如果你想知道一个对象的类，你可以使用关键字`type`如下:*

```
***//Method 1**
class Car:
  wheel_count = 4my_car = Car()>>> type(my_car)
**<class '__main__.Car'>***
```

*这里，我们定义了一个类`Car`并创建了它的对象`my_car`。然后我们使用关键字`type`来获得我们对象的类。因为 Python 是一种动态类型语言，所以`type`关键字有助于在运行时获取任何变量的类型。*

*Python 与大多数其他语言的区别在于，在 Python 中一个`Class`本身就是一个`Object`！。所以在上面的代码片段中，当我做`class Car:`时，一个对象被创建。这个对象属于一个名为`Metaclass`的类，一个用于创建类的`class`。*

*当 Python 中没有提供元类时，默认的元类是`type`，我们同样的好的旧关键字`type`，它也让你知道任何变量的类型。我们也可以在类定义中提供元类，如下所示:*

```
***//Method 2**
class Car(object, metaclass=type):
    wheel_count = 4*
```

*上面对类`Car`(方法 2)的定义与之前定义的(方法 1)相同，因为`object`是默认的超类，正如前面提到的，`type`是所有类的默认元类。我们也可以使用关键字`type`动态创建类，如下所示:*

```
***//Method 3**
Car = type(‘Car’, (object), {'wheel_count':4})*
```

*其中第一个参数是类名，第二个是超类元组，第三个是类名称空间属性字典。虽然方法 3 与前两种方法不完全相同，因为`class`关键字不仅仅是语法上的糖，它还做了一些额外的事情，比如设置适当的`__qualname__`和`__doc__`属性或者调用 `__prepare__`。*

*自定义元类可以通过子类化默认元类`type`来定义。然后它可以用来创建一个类，就像我们之前看到的那样。*

```
*class MyCustomMetaClass(type):
    will_fly = Trueclass FlyingCar(metaclass=MyCustomMetaClass):
    pass*
```

*元类是什么，它们能做什么，什么时候应该使用，还有更多内容。但是它本身保证了一个单独的帖子，并且上面的内容足以让一个有经验的 Python 开发人员了解元类。*

# *3.Python 程序执行和 PVM*

*我们知道 Python 是一种解释型语言，而 Java 是一种编译型语言。不幸的是，这是对实际执行过程的过于简单化的看法。如果你解构 Python 解释器，你会发现一个 Python 虚拟机和一个编译器(喘息！).所以让我们从头开始。*

*当我们通过执行`> python helloworld.py`来执行我们的 python 文件时，代码首先被*编译*为一个更简单的版本，称为字节码，并被存储在类似`helloworld.pyc`的`.pyc`或`.pyo`扩展中。*

*字节码代表 Python 开发人员创建的固定指令集，代表所有类型的操作。每个字节代码指令的大小是 1 字节(或 8 位)，因此这些被称为“字节”代码指令。这个字节码是一组低级指令，然后由 Python 虚拟机解释。*

*将 python 代码编译成字节码是一个多步骤的过程，处理编译的不同方面。例如，第一步将 Python 代码解析成解析树，解析树识别并标记代码中的所有语法错误。*

*当 Python 程序被编译时，解释器检查`.py`文件的最后一次编辑时间戳，并将其与`.pyc`文件中字节码的时间戳进行比较。如果字节码比 Python 代码旧，解释器会重新编译并生成新的字节码。否则，它将跳过编译并重用现有的字节码。*

*解释器将所有的字节码`.pyc`文件保存在`__pycache__`文件夹中。因此，不提交您的`__pycache__`文件夹或`.pyc`文件是一个好的开发实践。为了防止解释器保存字节码文件，还可以将`PYTHONDONTWRITEBYTECODE`标志设置为任何非零值。在创建基于 Python 的[docker 文件](https://faun.pub/the-near-perfect-dockerfile-for-django-applications-8bc352a1e871)时，这个标志非常方便，因为我们不想在 docker 映像中保存字节码文件。*

*与将高级语言编译成 CPU 可理解的机器代码的其他编译器不同，Python 编译器将 Python 代码转换成另一种更简单的代码(字节码)，这种代码不是机器代码，因此不能由 CPU 执行。*

*相反，Python 虚拟机或 PVM 将这个字节码解释成由 CPU 执行的机器码。这就是为什么 Python 被认为是一种解释语言，尽管编译是它的执行阶段之一。*

*PVM 允许 Python 独立于平台。给定相同的字节码和 PVM 版本，Python 字节码可以在任何平台上执行。下一步是学习[如何打包](https://packaging.python.org/en/latest/overview/) python 应用程序和发行版。*

# *4.Python 的 GIL 和多线程面临的挑战*

*众所周知，Python 在多线程方面表现不佳。这种情况的根本原因是 Python 如何使用引用计数方法执行垃圾收集。*

*在引用计数垃圾收集中，Python 记录有多少个变量指向一个特定的变量，这个变量一直在变化。如果引用一个对象的变量计数达到零，则该变量有资格进行垃圾收集。*

```
*>>> import sys
>>> my_dict = dict()
>>> another_variable = my_dict
>>> sys.getrefcount(my_dict)
**3***
```

*在上面的例子中，我们初始化了一个字典对象，两个变量`my_dict`和`another_variable`指向它。我们使用`sys.getrefcount()`方法检查引用我们对象的变量，在检查的同时，我们也给我们的对象分配了一个变量，函数参数。因此，我们得到的预期结果是 3。如前所述，当这个引用计数达到零时，该对象就有资格进行垃圾收集。*

*现在假设两个线程正在更新同一个对象的引用计数。它们可能会同时写入，从而导致竞态条件。[这个](https://www.techtarget.com/searchstorage/definition/race-condition)简洁地描述了竞态条件:*

> *竞态条件是指程序的行为取决于多个线程或进程的相对时间的条件。一个或多个可能的结果可能是不期望的，从而导致 bug。*

*可以通过为每个对象引入锁来避免竞争情况，但是它们会带来死锁问题。*

*Python 在解释器本身上有一个超级锁，称为全局解释器锁(GIL ),以减轻这些复杂性。然而，如果两个线程正在运行，它们轮流获取 GIL，然后另一个线程不能访问解释器本身。这解决了竞争条件和死锁的问题，但禁止两个线程同时执行，即使代码是多线程的，Python 也能有效地实现单线程。*

*人们一直在努力废除 GIL 教，但是还没有一个成功。最近，开发人员 Sam Gross[提议](https://mail.python.org/archives/list/python-dev@python.org/thread/ABR2L6BENNA6UPSPKV474HCS4LWT26GY/)在 GIL 进行一项重大变革，以提升多线程性能。所以，让我们祈祷吧。*

# *5.__str__ 和 __repr__ 的魔术方法和实际使用*

*大多数 Python 开发者都熟悉 magic 或 dunder 方法，如`__str__`、`__repr__`、`__add__`等。在 Python 中，魔术方法或 dunder 方法是以 Dunder(双下划线)开始和结束的预定义方法。它们主要用于运算符重载和为类提供附加功能。让我们看一个用来重载操作符`+`的神奇方法`__add__`的例子。*

```
*class Handbag:
  def __init__(self, item_count):
    self.item_count = item_counthandbag1 = Handbag(item_count=5)
handbag2 = Handbag(item_count=3)>> handbag1 + handbag2
>> **TypeError: unsupported operand type(s) for +: 'Handbag' and 'Handbag'***
```

*在上面的代码中，我们定义了一个类`Handbag`，这个类中存储了物品的数量。现在，我们希望我们的`Handbag`类有一个属性，当我们添加两个`Handbags`时，我们将获得两个`Handbags`中的总项目。*

*但是，正如我们在上面的例子中看到的，我们得到一个错误，即`Handbag`类不支持`+`。为了实现我们的目标，我们必须做到以下几点:*

```
*class Handbag:
  def __init__(self, item_count):
    self.item_count = item_count **def __add__(self, second_handbag):
    return self.item_count + second_handbag.item_count**handbag1 = Handbag(item_count=5)
handbag2 = Handbag(item_count=3)>> handbag1 + handbag2
**>> 8***
```

*在上面的例子中，我们添加了神奇的方法`__add__`，这将允许我们对`Handbag`对象使用`+`操作符。`__add__`接受两个对象输入，一个是 self，即第一个操作数，另一个是第二个操作数，并返回两个包中项目的总和。*

## *__str__ 和 __repr__ 魔术方法*

*`__str__`和`__repr__`是两种经常被混淆的流行魔术方法。然而，对它们的充分理解对调试和文档记录大有帮助。*

*`__repr__`的默认目标是拥有一个对象的字符串表示，可以使用 Python 的`eval`从该字符串表示中再次形成对象，如下所示:*

```
***object = eval(repr(object))***
```

*这对于像`list`这样的简单对象是可能的，但对于复杂对象是不可能的。让我们看一个简单对象的例子，一个`list`。*

```
*>>> mylist = [1, 2, 3]
>>> repr(mylist)
'[1, 2, 3]'>>> eval(repr(mylist))
[1, 2, 3]>>> mylist == eval(repr(mylist))
**True***
```

*另一个神奇的方法`__str__`用于获取一个对象的字符串表示，以用于文档和日志记录。如果没有定义任何类的`__str__`，但是定义了`__repr__`，则该类的对象将使用`__repr__`代替`__str__`，反之则无效。*

*写代码的时候，`__repr__`的目标应该是不含糊，`__str__`的目标应该是可读性。`__repr__`应该提供能够唯一识别对象的表示。正如我们在上面的`list`例子中看到的，它可以是分配给每个对象或对象组成部分的任何标识符。*

*另一方面，`__str__`的目标应该是可读性，如果一个对象被记录和打印，用户可以容易地阅读和理解它的描述，即使这是以唯一性为代价的。*

*魔术方法是 Python 工具包中非常有用的工具，能够轻松地添加和修改许多基本特性。*

*上面提到的主题绝不是详尽的，而是根据我与其他开发人员的交流挑选出来的。我觉得更多的话题应该被包括在这里，但由于博客的长度而没有包括在内。以后我会把剩下的这类题目整理出来，写一篇补充文章。*

*本博客到此为止，后续文章请关注，谢谢！如果你喜欢你读到的东西，也可以看看这个故事:*

*[](https://bettersoftwarewithsid.com/the-near-perfect-dockerfile-for-django-applications)*