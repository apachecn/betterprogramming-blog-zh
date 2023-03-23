# “Yield”关键字有什么作用？

> 原文：<https://betterprogramming.pub/what-does-the-yield-keyword-do-6b9304149462>

## Python 中的可迭代对象、函数和生成器

![](img/80b729672eb512aad43f97c5dd517196.png)

照片由 [C 在](https://unsplash.com/photos/H9-EYONVLaY) [Unsplash](https://unsplash.com/) 上烘干

要了解关键字`yield`，您必须首先熟悉各种其他术语和概念。在我们深入研究所有这些之前，让我们牢记最终目的，并通过*讨论为什么* `yield`是有益的。

使用`yield`将提高内存效率——从而提高速度/性能——当循环遍历一个大的可迭代对象时。对于小型数据集，这些好处不太明显；然而，随着数据的增长，使用`yield`的好处也在增加。

现在，我们来解释一下。简而言之，`yield`是对 *return* 关键字的替换，该关键字从一个函数产生一个*生成器*，该生成器用作一次性可迭代函数。如果你和我一样，当我第一次学习这个的时候，你会迷失方向。因此，要解开这个概念，让我们从基础开始，一步步向上。

# 什么是可迭代的？

有点开玩笑，但 iterable 是一种可以迭代的数据类型。澄清一下，它是一种数据类型，其中各个元素是可理解的数据——字符串的各个字符、字典中的术语、列表或元组中的项目等。从功能上讲，如果数据在以下代码片段中有效，则它是可迭代的:

```
for x in my_iterable:
   pass
```

需要注意的是，虽然一次只能访问一个元素，但整个 iterable 仍然存储在内存中。这意味着如果你在一个从 0 到 5，000，000，000 的范围内迭代，那么每一个值都会占用资源。即使作为程序员，您在循环中高效地编写了一组重复的命令，应用程序仍然需要存储正在迭代的内容。

# 什么是函数？

函数是构成定义的一组命令，除非被调用，否则不会被执行。这意味着，即使你的函数的进程占用了大量的内存和资源，那也只会发生在函数被调用的时候。函数的定义本身什么也不做。

把一个函数想象成一个黑盒是很有帮助的，它接受输入(参数)并产生预期的输出(返回值)。因此，一个函数很像一个公式或方程式，除了它超越了算术。

我将函数描述为黑盒，因为函数内部发生的事情对用户来说真的不重要——您只需要知道向函数提供什么是必需的，以及期望输出什么。除了参数之外，该函数不能访问任何其他内容。类似地，除了返回值之外，函数内部的任何东西都不能在函数外部访问。

# 什么是发电机？

生成器是一次性使用的可迭代类型。设计意图是 iterable 中的元素一旦被消费就不再需要了。因此，生成器不是创建和存储整个 iterable，而是一次生成一个元素。这可以节省资源并提高性能。要了解这一点，让我们将列表与生成器进行比较:

```
my_list = [n for n in range(10)]
print(my_list)
# [0,1,2,3,4,5,6,7,8,9]my_generator = (n for n in range(10))
print(my_generator)
# <generator object <genexpr> at 0x10daaa480>
```

首先，语法上的区别是列表理解是用方括号`[]`完成的，而生成器理解是用括号`()`完成的。注意，当一个生成器指向一个生成器对象时，这个列表就被存储起来并可以随时打印。

它们看起来不同，但是在一个循环中工作时，它们的行为是相似的。

```
for element in my_list:
   print(element)
   """
   0
   1
   2
   3
   ... etc.
   """for element in my_generator:
   print(element)
   """
   0
   1
   2
   3
   ... etc.
   """
```

# “Yield”关键字有什么作用？

最后，解释我们最初的问题。关键字`yield`将代替函数定义中的`return`来创建一个生成器。然后，可以将它用作普通的可迭代对象，获得生成器和函数的好处。

```
def createGenerator():
   for i in range(100):
   yield imy_generator = createGenerator()
print(my_generator)
# <generator object createGenerator at 0x102dd2480>for i in my_generator:
   print(i) # prints 0-99
```

那么，这是如何工作的呢？当返回的生成器第一次被使用时——不是在赋值语句中，而是在`for`循环中——函数定义将一直执行到到达`yield`语句。在那里，它会暂停(看看为什么叫 yield)直到再次被使用。然后，它会从停止的地方继续。在生成器的最后一次迭代中，`yield`命令之后的任何代码都将执行。

让我们添加一些打印代码来查看事件的顺序:

```
def createGenerator():
   print("Beginning of generator")
   for i in range(3):
      yield i
   print("After yield")print("Before assignment")
my_generator = createGenerator()
print("After assignment")for i in my_generator:
   print(i) # prints 0-99"""
Before assignment
After assignment
Beginning of generator
0
1
2
After yield
"""
```

总之，`yield`关键字修改一个函数的行为来产生一个生成器，它在迭代过程中的每个`yield`命令处暂停。除了迭代之外，该函数不会被执行，这导致了改进的资源管理，并因此带来了更好的整体性能。使用生成器(和生成的函数)来创建用于一次性迭代的大型数据集。