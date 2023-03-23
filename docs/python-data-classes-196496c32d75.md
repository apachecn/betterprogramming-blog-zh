# Python 数据类——编写类的简写

> 原文：<https://betterprogramming.pub/python-data-classes-196496c32d75>

## 使用数据类来删除 __init__()、__repr__()、以及其他样板代码

![](img/2d749f0ee9d8758c940aeab1ff64c0f7.png)

[马丁·施瑞德](https://unsplash.com/@martinshreder?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Python 数据类是[类](https://www.codingem.com/what-is-a-python-class/)的特殊类型。它允许您停止编写与类相关的重复性样板代码。比如可以去掉`__init__()`方法。

要创建一个数据类，在类定义前面添加一个`@dataclass`装饰器，并为类属性提供类型信息:

现在，您可以通过以下方式创建对象:

```
banana = Fruit("Banana", "Yellow", 0.1)
```

接下来，让我们通过首先使用一个类，然后用一个数据类简化它，来看看数据类解决了什么问题。

# 初始化

## 班级

创建 Python 对象时，您希望使用用户定义的值对其进行初始化。

在 Python 中，有一个为[类](https://www.codingem.com/what-is-a-python-class/)预留的`__init__()`方法。它被称为构造函数方法，可以用来初始化一个对象。当您创建新对象时，会自动执行此方法。

例如，让我们创建一个带有初始化器的`Fruit`类:

现在，您可以像这样创建水果对象:

```
banana = Fruit("Banana", "Yellow", 0.2)
print(banana.color)
```

输出:

```
Yellow
```

这就是你如何在 Python 中创建了类。让我们看看数据类能为你做什么。

## 数据类别

Python 3.7 引入了数据类来简化类的创建。

例如，您可以使用数据类来摆脱您的`__init__()`方法。

为了演示，让我们使用一个数据类来实现上面的`Fruit`类:

让我们检查一下代码:

*   首先，从`dataclasses`库中导入`dataclass`。
*   然后[用一个`**@**dataclass`装饰器装饰](/understand-python-decorators-in-3-minutes-ec48fdc8e2cf)这个`Fruit`类。这就把类变成了数据类。
*   最后，不需要实现`__init__()`方法，只需用类型信息将属性添加到类中。

现在您可以测试这个数据类:

```
banana = Fruit("Banana", "Yellow", 0.1)
print(banana.color)
```

输出:

```
Yellow
```

它的工作原理和`Fruit`的班级版一模一样。

# 表示字符串

在 Python 中，`repr()`函数返回一个对象的字符串表示。它通过调用该类的`__repr__()`方法来实现这一点。您可以重写此方法，根据需要自定义字符串表示形式。

## 班级

让我们再次使用我们的`Fruit`类:

让我们创建一个`Fruit`对象，并在其上调用`repr()`。因为您还没有提供对`__repr__()`方法的实现，所以一个默认版本被称为:

```
banana = Fruit("Banana", "Yellow", 0.1)
print(repr(banana))
```

结果:

```
<__main__.Fruit object at 0x7f4b20f01d00>
```

然而，这不是人类可读的。

您可以通过覆盖`__repr__()`方法，用一个人类友好的版本替换字符串表示:

现在，您可以再次调用`Fruit`对象上的`repr()`,以获得更易读的字符串表示:

```
banana = Fruit("Banana", "Yellow", 0.1)
print(repr(banana))
```

输出:

```
Fruit(name='Banana', color='Yellow', weight=0.1)
```

这是你在课堂上的做法。现在让我们看看数据类如何处理字符串表示。

## 数据类别

一个数据类自动为您实现了`__repr__()`的人性化版本。

例如，让我们创建一个数据类，`Fruit`:

现在对一个`Fruit`对象调用`repr()`:

```
banana = Fruit("Banana", "Yellow", 0.1)
print(repr(banana))
```

输出:

```
Fruit(name='Banana', color='Yellow', weight=0.1)
```

默认情况下，结果是可读的。这为您节省了代码行，因为您不需要覆盖`__repr__()`方法。

如果您不喜欢这种表示，也可以在数据类中覆盖它。

# 两个物体相等吗？

## 班级

如果你想比较两个对象，你需要实现`__eq__()`方法。如果要检查两个对象的属性是否相同，这很有用。

例如，让我们给`Fruit`类添加一个`__eq__()`方法:

让我们通过创建两个具有相同属性的`Fruit`对象来对此进行测试:

```
fruit1 = Fruit("Banana", "Yellow", 0.1)
fruit2 = Fruit("Banana", "Yellow", 0.1)print(fruit1 == fruit2)
```

输出:

```
True
```

想要在你的类中实现平等是很常见的。这就是数据类可以帮助您节省时间的地方。

## 数据类别

数据类自动为您实现`__eq__()`方法。

让我们使用`Fruit`的数据类版本来比较两个相同的`Fruit`对象。

输出:

```
True
```

# 如何对对象进行排序和比较

## 班级

要比较两个对象，您需要实现比较方法。这些是:

*   `__lt__()` —小于
*   `__gt__()` —大于
*   `__eq__()` —等于
*   `__ne__()` —不相等
*   `__ge__()` —大于或等于
*   `__le__()` —小于或等于

例如，让我们可以通过重量来比较`Fruit`对象:

让我们来测试一下:

输出:

```
banana < apple:  True
banana > apple:  False
banana <= apple:  True
banana >= apple:  False
banana == apple:  False
banana != apple:  True
```

正如您所看到的，这需要大量的样板文件来完成，尽管这是一个非常基本的操作。

同样，数据类可以帮助你。

## 数据类别

比较数据类对象很方便——您不需要实现任何冗长的比较方法。

要启用数据分类比较，请执行以下操作:

*   将`**@**dataclass`装饰器的参数`order`设置为`True`
*   使用`field()`功能定义您希望在比较中考虑的属性。

为了演示这一点，让我们编写一个支持基于权重的比较`<>, ≥, ≤, ==, !=`的`Fruit`数据类:

使用数据类比较对象节省了 16 行代码

让我们仔细看看代码

*   在顶部，您从`dataclasses`模块导入`field`。
*   `**@**dataclass`装饰器接受参数。默认情况下，`order`被设置为`False`。要启用比较，您需要设置`order=True`。
*   在数据类中，您为每个属性分配一个`field`来突出显示您想要比较的内容。设置`compare=False`忽略比较中的属性。设置`compare=True`以在比较中考虑属性。

现在您可以比较`Fruit`对象:

输出:

```
banana < apple:  True
banana > apple:  False
banana <= apple:  True
banana >= apple:  False
banana == apple:  False
banana != apple:  True
```

# 如何散列对象

## 班级

哈希值是唯一的整数值，用于在查找过程中快速比较字典键。

为了散列你的对象，你可以覆盖`__hash__()`方法。此外，您需要实现`__eq__()`方法来使其工作。

例如，让我们使`Fruit`类可散列，这样两个相同的对象返回相同的散列。具有唯一属性的对象应该返回唯一的哈希。

让我们创建三个`Fruit`对象——两个相同，一个独特:

```
fruit1 = Fruit("Banana", "Yellow", 0.1)
fruit2 = Fruit("Banana", "Yellow", 0.1)
fruit3 = Fruit("Banana", "Brown", 0.2)print(hash(fruit1))
print(hash(fruit2))
print(hash(fruit3))
```

输出示例:

```
-6457846617344767246
-6457846617344767246-4509737671946749260
```

如果你想了解更多关于哈希的知识，请看这里的。

## 数据类别

可以通过在`**@**dataclass`装饰器中将`unsafe_hash`设置为`True`来散列数据类对象。

这使得`Fruit`是可散列的，因此两个相同的对象返回相同的散列。具有唯一属性的对象应该返回唯一的哈希。

例如:

现在，两个相同的对象具有唯一的哈希值:

```
fruit1 = Fruit("Banana", "Yellow", 0.1)
fruit2 = Fruit("Banana", "Yellow", 0.1)
fruit3 = Fruit("Banana", "Brown", 0.2)print(hash(fruit1))
print(hash(fruit2))
print(hash(fruit3))
```

输出示例:

```
2454484982753268175
24544849827532681755881766570306781446
```

# 如何为对象提供默认值

## 班级

您可以在初始化器中为类属性提供默认值。

例如，让我们创建一个`Fruit`类，其中`Fruit`对象默认为苹果:

让我们来测试一下:

```
apple = Fruit()
banana = Fruit("Banana", "Yellow", 0.1)print(apple.name)
print(banana.name)
```

输出:

```
Apple
Banana
```

## 数据类别

您可以向数据类添加默认属性。为此，请为 class 属性分配一个值。不要忘记添加类型信息。

让我们默认设置`Fruit`对象为苹果:

输出:

```
Apple
Banana
```

您现在已经了解了如何使用数据类来使代码更短、更简洁。现在让我们看看 Python 中其他一些有用的数据类独有的特性。

# 初始化后如何定义数据类属性

**Post initialization** 让您定义在初始化过程中不想添加的类的属性。

要使用 post 初始化，请在您的类中实现`__post_init__()`方法。初始化完成后，会立即自动调用此方法。

例如，让我们将`info`属性添加到用一句话描述对象的`Fruit`类中:

现在，您可以创建一个香蕉对象，并在其上调用信息:

```
banana = Fruit("Banana", "Yellow", 0.1)
print(banana.info)
```

结果:

```
This is a Yellow Banana that weighs 0.1 kilos
```

# 如何创建不可变的数据类

有时创建一个对象的值不能改变的类是很有用的。这可以通过数据类来完成。

要创建一个不可变的数据类，将一个`frozen=True`传递给`**@**dataclass`装饰器。

例如，让我们使`Fruit`类不可变:

如果试图改变`Fruit`对象的属性:

```
banana = Fruit("Banana", "Yellow", 0.1)
banana.name = "Apple"
```

会引发一个错误:

```
dataclasses.FrozenInstanceError: cannot assign to field 'name'
```

该错误表明您不能修改不可变的类。

请记住，冻结的类也不能修改自己。例如，您不能让一个方法改变它的属性。

# 数据类别转换

您可以将数据类对象转换为字典或元组。

为此，从数据类导入`astuple()`和`asdict()`方法。

*   使用`astuple()`转换成一个元组。
*   使用`asdict()`转换成字典。

例如:

输出:

```
('Banana', 'Yellow', 0.1)
{'name': 'Banana', 'color': 'Yellow', 'weight': 0.1}
```

# 结论

今天，您学习了如何通过使用 Python 中的数据类来减少样板代码。通过将您的类变成数据类，您可以去掉`__init__()`和其他常见的双下划线方法。

感谢阅读。编码快乐！

我很想加入你的 LinkedIn 网络。请随意连接[阿图里·贾利](https://www.linkedin.com/in/artturi-jalli-29619413a)。

# 你可能会发现见解深刻

[](/50-python-interview-questions-and-answers-5230fe2a0db6) [## 50 Python 面试问答

### Ace 您的下一次编码面试

better 编程. pub](/50-python-interview-questions-and-answers-5230fe2a0db6) [](/10-useful-python-snippets-to-code-like-a-pro-e3d9a34e6145) [## 10 个有用的 Python 片段，让你像专业人士一样编写代码

### 我每天使用的有用的提示和技巧

better 编程. pub](/10-useful-python-snippets-to-code-like-a-pro-e3d9a34e6145) 

# 参考

 [## 文件

### 欢迎光临！这是 Python 3.9.6 的文档。

docs.python.org](https://docs.python.org/3/)