# 如何用 Python 写一个有用的实例计数器

> 原文：<https://betterprogramming.pub/how-to-write-a-useful-instance-counter-in-python-9eee1761c3c7>

## 一个简单的 mixin，可以添加到任何类中，允许实例计数

![](img/2a8bd79ed787f78dac8b7f7e5048489c.png)

Crissy Jarvis 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

在本教程中，我将解释用 Python 编写实例计数器的显而易见的方法，然后展示如何改进它。

# 为什么要计算实例？

首先，我们为什么要计算一个对象的实例数？以下是一些使用案例:

*   我们希望在运行时给对象唯一的 id。
*   出于调试目的，我们希望跟踪或统计实例创建。
*   运行时实例计数对我们的程序有一定的意义(例如，我们需要按照创建顺序对对象进行排序)。

# 基本方法

实例计数的简单方法是将它直接添加到您的类中:

```
class A:
    numInstances = 0
    def __init__(self):
        A.numInstances += 1
        self.count = A.numInstances
```

这将创建一个名为`numInstances`的[类变量](https://docs.python.org/3/tutorial/classes.html#class-and-instance-variables)，用于跟踪创建的*实例总数*，以及一个名为`count`的实例变量，用于保存给定实例的实例号。

所以:

```
a = A()
b = A()
a.count            # returns 1
b.count            # returns 2
a.numInstances     # returns 2
b.numInstances     # returns 2
```

# 使其可重复使用

当然，作为优秀的程序员，我们希望这种计数功能可以重用。然后通过继承一些预先做好的`Countable`类，可以独立统计多个类。

我们的第一反应是这样做:

```
class B : A
    pass # implement specialist functionality in B
```

问题是`B`和`A`现在共享由`A`提供的同一个`numInstances`变量，给我们:

```
b = B()
b.count    # returns 3, not 1 as expected
```

然而，有一种方法可以解决这个问题，那就是使用 [Python 元类](https://www.datacamp.com/community/tutorials/python-metaclasses)。这使我们能够为每个子类创建单独的实例计数:

`Countable`类现在可以用作[混音](https://en.wikipedia.org/wiki/Mixin):

```
class A(Countable):
    passclass B (Countable):
    passa = A()
b = B()
a.count    # prints 1
b.count    # also prints 1\. numInstances is no longer shared
```

感谢阅读。如果您觉得这很有用，请查看我在 Medium 上的其他文章:

 [## 我的热门故事(每周更新)

### 本月读者最多:iOS 版比特币基地深度评论

jamiebullock.medium.com](https://jamiebullock.medium.com/my-top-stories-updated-weekly-ca7e9b57599e)