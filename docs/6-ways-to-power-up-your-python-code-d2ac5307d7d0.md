# 增强 Python 代码能力的 6 种方法

> 原文：<https://betterprogramming.pub/6-ways-to-power-up-your-python-code-d2ac5307d7d0>

## 打磨你的蛇佬腔的实用技巧

![](img/48d2e3f35c2bfdc48fe246461ecf2d35.png)

在 [Imgur](https://imgur.com/gallery/5at77) 上 [wrathletbirds](https://imgur.com/user/wrathletbirds) 拍摄的照片。

作为软件开发人员，我们努力编写能够很好地工作的代码。当然，这要求代码能够工作，但强调可维护性和清晰性的重要性。令人欣慰的是，开发人员并不是靠代码行来获得报酬的，这让我们能够专注于我们职业中真正重要的事情:为复杂的问题创造简单而优雅的解决方案。

Python 是一种语言，如果使用得当，这种语言很容易做到。我认为它在编程新手中的受欢迎程度证明了这一事实。可读代码对于那些希望学习基础知识的人来说是容易理解的。

另一方面，Python 不是一个特别严格的老师，很容易强化坏习惯。当我们还在学习的时候，这很好，功能比形式更重要——毕竟，我们必须先学会走，然后才能跑。然而，为了我们的开发伙伴和未来的自己，我们应该保持代码的整洁。能写一行为什么要写五行？[可读性计数](https://www.python.org/dev/peps/pep-0020/#the-zen-of-python)。

本文主要面向那些不熟悉 Python 最佳实践和速记语法的人。所描述的步骤很小也很简单，在阅读完本文后，它们可以——也应该——快速而轻松地应用于任何 Python 项目。

让我们开始吧！

# 1.使用列表理解

对列表的操作对于 Python 来说就像滑动对于蛇一样。也就是说，很普通！在下面的示例中，我们希望选择所有以 B 开头的名称，并在此过程中将它们转换为小写:

```
names = ["Alice", "Brian", "Betty", "Bob", "Christa"]
b_names = []
for name in names:
   if name.startswith("B")
      b_names.append(name.lower())
```

这段代码当然完成了工作，但是很冗长。迭代、过滤和转换数据集合是如此常规，事实上，Python 为我们提供了一些简洁的语法，可以在一条语句中实现这三项:

```
list = [**expression** for **item** in **iterable** if **condition**]
```

一个*列表理解*允许我们从一个现有的列表中构建一个新的列表，通过迭代它的条目，对那些条件为`True`的条目应用一个表达式。

```
names = ["Alice", "Brian", "Betty", "Bob", "Christa"]
b_names = [name.lower() for name in names if name.startswith("B")]
```

瞧啊。

# 2.使用序列解包提取相关数据

当操作复合类型时，我们将不可避免地需要进入内部来获取数据。客户的详细信息，例如:

```
person = ("Bob", 22, "4 Adder Avenue")
name = person[0]
age = person[1]
address = person[2]
```

*序列解包*允许我们通过一次将集合中的连续元素分配给多个变量，以一种更简洁的方式完成这项工作:

```
person = ("Bob", 22, "4 Adder Avenue")
name, age, address = person
```

当然，这个例子相对简单。更多的时候，我们将会处理任意结构的数据类型:一个熊猫[数据帧](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html)，一个枕头[图像](https://pillow.readthedocs.io/en/stable/reference/Image.html#the-image-class)，或者一个 PyQt [小部件](https://doc.qt.io/qtforpython/PySide6/QtWidgets/QWidget.html)。

在更复杂的场景中应用这种语法的一个简单方法是编写一个函数来封装特定于类型的获取逻辑，使用多个返回值将相关数据打包成一个元组。这允许我们在函数被调用时干净利落地解包回单个变量。

```
def get_user_info(user):
   ... 
   return name, age, addressuser = get_active_user()
name, age, address = get_user_info(user)
```

# 3.使用字典理解

记住前面的提示，我们可以将*字典理解*引入我们的代码:

```
other = {**key'**: **value'** for **key**, **value** in **dict.items()** if **condition**}
```

`items()`方法返回一组键值对，我们可以对其进行解包和处理以构建一个新的键值对。与列表理解一样，我们可以通过可选条件过滤字典条目。剩下的那些由两个表达式(`key’`和`value’`)转换，产生我们的最终项目。

该语法对于从嵌套字典中选择子属性非常有用:

# 4.更喜欢创建而不是删除

数据通常在项目过程中更新(例如，从学校记录中删除成绩不佳的学生)。当需求如此呈现时，下面的代码可能看起来是最自然的:

然而，在 Python 中，为我们需要的数据构建新的结构通常比删除我们不需要的数据更干净。请注意，去掉成绩不好的人实际上与保留成绩好的人是一样的，使用我们掌握的理解语法，用更少的行就可以做到这一点:

```
students = {"Alice": 90, "Bob": 81, "Charlie": 42}
achievers = {
   name: score for name, score in students.items() if score >= 60
}
```

这种方法还通过保证变量无论何时何地被使用都保持相同的值，使得测试和推理我们的代码变得更加容易。完全[不可改变的状态](https://medium.com/r?url=https%3A%2F%2Fopensource.com%2Farticle%2F18%2F10%2Ffunctional-programming-python-immutable-data-structures)是一个*整体*不过其他的蛇都可以。

# 5.尽可能使用 if-else 赋值

这个又短又甜。假设我们想根据一个布尔表达式的值给一个变量赋值。使用 Python 的*三元运算符，*我们可以执行`if-else`赋值，将一个冗长的五行:

```
mood = ""
if is_sunny(today):
   mood = "Happy"
else:
   mood = "Sad"
```

变成了一句俏皮话:

```
mood = "Happy" if is_sunny(today) else "Sad"
```

请注意，这只有在两种情况下都有值时才有效。只有当我们的条件是`True`时，我们才不能使用下面的代码来初始化变量:

```
mood = "Happy" if is_sunny(today) # Results in a SyntaxError
```

# 6.一起分配相关的上下文管理者

Python 的*上下文管理器*让我们能够控制临时资源的生命周期，比如文件流。通常，必须同时维护多个管理器(例如，当组合来自几个文件的数据时)。这可能导致过度嵌套的代码:

```
with open(file1) as in1:
   with open(file2) as in2:
      with open(file3) as out:
         out.write(in1.read() + in2.read())
```

一个鲜为人知的技巧是 Python 允许一次分配多个管理器，这使得我们的代码更加简洁:

```
with open(file1) as in1, open(file2) as in2, open(file3) as out:
   out.write(in1.read() + in2.read())
```

长队可能很难跟上。因此，明智的做法是少用这一招。更好的做法是，只要我们真正需要上下文管理器，就把它们保留下来:

```
with open(file1) as in1, open(file2) as in2:
   contents = in1.read() + in2.read()
with open(file3) as out:
   out.write(contents)
```

# 资源

*   [蟒蛇之禅](https://www.python.org/dev/peps/pep-0020/)
*   [数据结构](https://docs.python.org/3/tutorial/datastructures.html)
*   [熊猫数据帧类](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html)
*   [枕头图像模块](https://pillow.readthedocs.io/en/stable/reference/Image.html#the-image-class)
*   [PyQt QWidget 类](https://doc.qt.io/qtforpython/PySide6/QtWidgets/QWidget.html)
*   [功能编程指南](https://docs.python.org/3/howto/functional.html)
*   [Python 中的函数式编程:不可变数据结构](https://opensource.com/article/18/10/functional-programming-python-immutable-data-structures)
*   [三元运算符](https://book.pythontips.com/en/latest/ternary_operators.html)
*   [上下文管理器](https://book.pythontips.com/en/latest/context_managers.html)