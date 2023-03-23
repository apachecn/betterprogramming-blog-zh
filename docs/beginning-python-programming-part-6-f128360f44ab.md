# Python 编程入门—第 6 部分

> 原文：<https://betterprogramming.pub/beginning-python-programming-part-6-f128360f44ab>

## 为您的代码创建可重用的蓝图

![](img/8cff78f15ea82c69fd483413eeeb90da.png)

照片由[布鲁克·拉克](https://unsplash.com/@brookelark?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

在上一篇文章中，我们讨论了功能和范围:

[](https://medium.com/better-programming/beginning-python-programming-part-5-3c7cfa3cd701) [## Python 编程入门—第 5 部分

### 理解代码层次结构，以及如何使代码可重用

medium.com](https://medium.com/better-programming/beginning-python-programming-part-5-3c7cfa3cd701) 

它让我们很好地理解了如何将可重复的代码分解成函数，以及变量的范围如何影响程序的输出。

今天，我们将学习一种新类型的物体，它也可以被认为是一种类型。这种新类型的对象可以被认为是一个容器或一个蓝图，它为类似的代码段定义了一个称为*名称空间*的特殊范围。

这个新对象被称为一个**类**，它在清理和组织代码方面创造了奇迹。在我们学习本教程的过程中，我希望你能想象出一个房子的蓝图，或者是制作上图中所有星星的切饼刀。它不是房子，也不是 cookie，但它确保蓝图的每个*实例*都是完全相同的——这正是一个类所做的。

# 班级

简单地说，类是包含变量和函数的对象，但实际上，它们不仅仅是充当容器。类提供了一种创建可重复使用的代码的方法。

我要回去引用指针(你知道，我们提到它是有原因的)。当你创建一个类时，你就创建了一个引用；也就是说，指向内存中将存储该类的块，并引用它来创建该类的新实例或副本，并将它们复制到具有新值的新内存块中。这听起来可能会令人困惑，所以一些例子可能会有所帮助:

我们通过使用语法`class <name>()`来定义一个类。使用 PascalCase 命名类，这意味着没有空格，每个单词的第一个字母大写。对于这个例子，我正在创建一个新的`House`类。括号是必需的，我将在下面解释为什么。和往常一样，我们需要在末尾使用冒号来包含类的主体。

你会在这里看到一些有趣的东西:`def __init__(self)`。至少，这是你的*初始化函数*应该的样子。经验丰富的程序员喜欢称之为*构造方法。*

*一个方法和函数用在一个句子中时大多是可以互换的。大部分程序员用错了不会纠正你，但是函数和方法的区别在于方法属于类对象，而函数不属于。*

`init`两边的双下划线或`__`被称为*下划线*，用于*重载*，或替换默认方法中的功能。默认情况下，类包含一个`__init__(self)`方法，所以如果你事先没有任何事情要做，你不必添加这个方法。

但是我没有在构造函数方法中仅仅使用`self`，我还有一些其他的参数:`number_of_windows`和`number_of_doors`。我认为很容易理解它们的用途，因为我们在命名变量方面做得越来越好，但是为什么我们需要添加它们呢？

在构造函数方法中，添加参数来允许我们为类提供初始值总是有用的。在这种情况下，我们增加了窗户和门的数量。但仅此还不够。如果你只是把参数传递给一个常规函数，而不对它们做任何事情，什么都不会发生，对吧！？我们需要把这些分配给类，但是范围有问题。

我在上一篇文章中告诉过你，Python 为你处理范围。这就是其中一个例子。我们需要将传递给构造函数方法的值赋给属于该类实例的变量。为此，我们使用关键字`self`。`self`表示这个对象，不是类，而是要从类中创建的对象。

`self.number_of_windows`是*属性*，我们将使用它来存储我们为`number_of_windows`参数传递的数字。点`.`在这里很重要。如果`self`指的是这个类的实例，而`self.number_of_windows`是这个实例中我们想要存储这个值的属性，那么这一定意味着`.`一定意味着这个属性是这个类的子对象。`.`是我们如何访问类的属性和方法。

属性很像方法，除了它们引用类中的变量。是的，它们有时互换使用，你很可能不会得到纠正。

当创建一个新类时，您会注意到我有下面几行代码:

```
my_house = House(number_of_windows=2, number_of_doors=1)
your_house = House(4, 2)
```

这两个都是正确的语法，除了一个使用了*命名参数*。如果你错过了我关于[函数](https://medium.com/better-programming/beginning-python-programming-part-5-3c7cfa3cd701)的文章，我会稍微介绍一下，但同样适用于这里。如果使用命名参数，每个人都很容易理解这些值的含义，而无需深入研究代码。您还可以获得以任何顺序使用它们的额外好处。另外，注意`self`是缺失的，我们在调用方法时不使用`self`。一分钟后会有更多关于`self`的报道。

如果我们像对待`your_house`一样选择省略命名参数，我们必须对参数使用与它们在构造函数方法中出现时相同的顺序。这意味着窗户的数量是`4`，门的数量是`2`。

最后，我们打印每栋房子的窗户和门的数量。`my_house`只有一扇门，所以它会打印号码`1`。`your_house`比我的好，所以可以打印`4`窗口。我可以打印出`your_house.number_of_doors`，它会显示`2`。原因是`my_house`和`your_house`是`House`的两个不同实例。尽管它们来自同一个类，但它们是两个独立的实体。

那么为什么`self`如此重要呢？我们还没有讨论静态方法，但是我们可以快速浏览一下。静态方法是不依赖于类属性或方法运行的方法。让我们看一个例子。

这里我们有一个`Dog`类。跳过构造函数，我们有了第一个方法。这是一个类方法，所以我们包含了`self`。我们这样做是因为它引用了这个实例的`name`和`age`属性。然而，所有的狗生来就有说话的能力。当我们提到狗时，我们知道它们应该汪汪叫！既然我们知道这是事实，我们可以通过在这个名为`staticmethod`的方法上面添加一个*装饰器*来使它成为一个静态方法。`@`表示这应该修饰下一个函数，是的，这是必需的。

通过使用`@staticmethod`装饰器，我们不必使用`self`。那么`self`到底有什么重要的？如果我们使用`self`，我们必须有一个类的实例。如果我们没有一个类的实例，我们就不能使用属于这个类的属性或方法。通过使用静态方法，我们能够在不创建类的实例的情况下调用这个方法。

这意味着我们可以使用`Dog.speak()`并将`Woof!`打印到我们的终端上。`Datetime`是使用静态方法的一个很好的例子。

```
from datetime import datetimedatetime.now()
```

现在不要太担心第一行——我们将在以后讨论它。这里你可以看到`datetime`类有一个名为`now()`的静态方法，它从你的系统返回当前的日期时间。我能够调用`now`方法，而不需要实际创建`datetime`类。

如果您的方法未标记为静态，则需要创建该类的新实例，然后才能使用它。

关于`Dog`类中的`get_description(self)`方法需要注意的一些事情:如果你从类内部调用这个方法，你必须使用`self.get_description()`。仅仅因为它是这个类的一个方法，并不意味着它可以免于`self`。当从类中的另一个方法调用静态方法`speak()`时，必须用`self`调用它。

![](img/e372be4d05c34ced0dd67ee9ad1a4afc.png)

照片由 [Breno Assis](https://unsplash.com/@brenoassis?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 性能

不，不是那些种类的属性，但是我确实选择了那个图像作为一个千篇一律的蓝图的例子。

向类添加属性时，范围很重要。举例来说:

有什么区别？在`Person1`中，我们添加了一个名为 name 的属性，然后我们从`Person1`创建了`fred`和`wilma`。我们有两个实例，我们将每个实例设置为各自的名称。

当我们显示`fred.name`时，它错误地打印了“威尔玛”，但是当我们显示`wilma.name`时，它正确地打印了“威尔玛”。怎么回事？！

当你不使用`self.`创建一个顶级属性时，就像我们在构造函数方法中所做的那样，它变成了一个类属性。

类属性和类属性之间的区别在于，类属性在类的所有实例中共享，而类属性仅在该实例中可用。

范围让我们来到这里，但是如果我们将`name`改为`self.name`，我们的代码将是正确的；然而，在创建实例时，我们没有构造函数方法来为`self.name`提供初始值。

在我们的第二个例子中，我们没有使用`self.name`作为类中的属性，但是，我们将构造函数中的参数`name`赋给了`self.name`。在幕后，当您使用`self.something`时，Python 自动将范围提升到类的顶级，并将对象标记为属性而不是特性。

```
class Dog(object):
    self.name def __init__(self, name):
        self.name = name# is the same asclass Dog():
    def __init__(self, name):
        self.name = name
```

因此，在创建类的时候要小心，并且尽量使用构造函数。

# 摘要

今天我们讨论了类、方法和属性。我也顺便提一下*初始化*。我将在下一个主题中更多地介绍初始化，但我真的想介绍更多与类相关的内容，包括更多的覆盖、子类、去初始化和引用计数。

## 推荐阅读

Python 教程的 9 - 9.4 章。

 [## 9.类- Python 3.7.3 文档

### 与其他编程语言相比，Python 的类机制用最少的新语法和…

docs.python.org](https://docs.python.org/3/tutorial/classes.html) 

这是让我觉得上课有趣的东西，我希望你在实践你所学的东西。

[](https://medium.com/@broebling/beginning-python-programming-part-7-2f7df98b7c68) [## Python 编程入门—第 7 部分

### 在上一篇文章中，我们讨论了类、方法和属性。

medium.com](https://medium.com/@broebling/beginning-python-programming-part-7-2f7df98b7c68)