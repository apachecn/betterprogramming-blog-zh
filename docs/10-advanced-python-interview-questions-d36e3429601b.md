# 10 个高级 Python 面试问题

> 原文：<https://betterprogramming.pub/10-advanced-python-interview-questions-d36e3429601b>

## 搞定你的下一次面试

![](img/55b510cad7d82dc14c7e966d4da6f24b.png)

图片由作者提供。

随着 Python 最近变得越来越流行，你们中的许多人可能正在接受与 Python 相关的技术面试。在本帖中，我将列出十个高级 Python 面试问答。

这些可能会令人困惑，针对的是中级开发人员，他们需要很好地理解 Python 作为一种语言以及它是如何工作的。

# Nolocal 和 Global 关键字有什么用途？

这两个关键字用于改变先前声明的变量的范围。`nolocal`经常在需要访问嵌套函数中的变量时使用:

```
def func1(): 
    x = 5
    def func2(): 
        nolocal x 
        print(x) 
    func2()
```

`global`是更直接的指令。它使先前声明的变量成为全局变量。例如，考虑以下代码:

```
x = 5 
def func1(): 
    print(x) 
func1() 
> 5
```

由于`x`是在函数调用之前声明的，`func1`可以访问它。但是，如果你试图改变它:

```
x = 5 
def func2(): 
    x += 3 
func2() 
> UnboundLocalError: local variable 'c' referenced before assignment
```

为了让它工作，我们需要指出，我们所说的`x`是指全局变量`x`:

```
x = 5 
def func2(): 
    global x 
    x += 3 
func2()
```

# Classmethod 和 Staticmethod 有什么区别？

它们都定义了一个无需实例化类的对象就可以调用的类方法。唯一的区别在于他们的签名:

```
class A: 
    @staticmethod 
    def func1(): 
        pass 
    @classmethod 
    def func2(cls): 
        pass
```

如您所见，`classmethod`接受一个隐式参数`cls`，该参数将被设置为类`A`本身。`classmethod`的一个常见用例是创建可选的可继承构造函数。

# 什么是 GIL，有哪些方法可以避开它？

GIL 代表全局解释器锁，它是 Python 用于并发的一种机制。它深深地嵌入到 Python 系统中，目前不可能摆脱它。GIL 的主要缺点是它使线程不能真正并发。它会锁定解释器，即使看起来您正在使用线程，它们也不会同时执行，从而导致性能损失。以下是一些解决方法:

*   `multiprocessing`模块。它允许您生成新的 Python 进程，并像管理线程一样管理它们。
*   `asyncio`模块。它有效地启用了异步编程，并添加了`async/await`语法。虽然它没有解决 GIL 问题，但它会使代码更易读、更清晰。
*   [无栈 Python](https://en.wikipedia.org/wiki/Stackless_Python) 。这是一条没有 GIL 的蟒蛇叉。它最显著的用途是作为 EVE 在线游戏的后端。

# 什么是元类，什么时候使用？

元类是类的类。元类可以指定某些行为，这些行为对于许多类来说是常见的，以防继承过于混乱。一个常见的元类是`ABCMeta`，用于创建抽象类。

Python 中的元类和元编程是一个巨大的话题。如果你对它感兴趣，一定要多读一些。

# 什么是类型注释？什么是泛型类型注释？

虽然 Python 是一种动态类型语言，但为了清晰起见，有一种方法可以注释类型。这些是内置类型:

*   `int`
*   `float`
*   `bool`
*   `str`
*   `bytes`

复杂类型可从`typing`模块获得:

*   `List`
*   `Set`
*   `Dict`
*   `Tuple`
*   `Optional`
*   等等。

下面是使用类型注释定义函数的方法:

```
def func1(x: int, y: str) -> bool: 
    return False
```

泛型类型批注是将另一种类型作为参数的批注，允许您指定复杂的逻辑:

*   `List[int]`
*   `Optional[List[int]]`
*   `Tuple[bool]`
*   等等。

请注意，这些仅用于警告和静态类型检查。在运行时不能保证这些类型。

# 什么是发电机功能？编写您自己的 Range 版本

生成器函数是可以在返回值后暂停执行的函数，以便在稍后的某个时间恢复执行并返回另一个值。这是通过关键字`yield`实现的，您可以用它来代替 return。您使用过的最常见的生成器函数是`range`。下面是实现它的一种方法(只适用于积极的步骤，我将把它作为一个练习，让它支持消极的步骤):

```
def range(start, end, step): 
    cur = start 
    while cur > end: 
        yield cur 
        cur += step
```

# Python 中的 Decorators 是什么？

Python 中的装饰器用于修改函数的行为。例如，如果您想记录对一组特定函数的所有调用，缓存它的参数和返回值，执行基准测试等。

装饰符以符号`@`为前缀，放在函数声明之前:

```
@my_decorator 
def func1(): 
    pass
```

# Python 中的酸洗和拆洗是什么？

Pickling 只是序列化的 Python 说法。Pickling 允许您将一个对象序列化为一个字符串(或您选择的任何其他内容)，以便保存在存储上或通过网络发送。拆包是从已拆包的字符串中恢复原始对象的过程。腌制不安全。仅从可信来源中取消选取对象。

下面是你如何处理一个基本的数据结构:

```
import pickle 
cars = {"Subaru": "best car", "Toyota": "no i am the best car"} cars_serialized = pickle.dumps(cars) 
# cars_serialized is a byte string new_cars = pickle.loads(cars_serialized)
```

# Python 函数中的`*args`和`**kwargs`是什么？

这些与拆包密切相关。如果将`*args`放在函数的参数列表中，所有未命名的参数都将存储在`args`数组中。`**kwargs`工作方式相同，但对于命名参数:

```
def func1(*args, **kwargs): 
    print(args) 
    print(kwargs) 
func1(1, 'abc', lol='lmao') 
> [1, 'abc'] 
> {"lol": "lmao"}
```

# `.pyc`文件是用来做什么的？

`.pyc`文件包含 Python 字节码，与 Java 中的`.class`文件一样。Python 仍然被认为是一种解释语言，因为这个编译阶段发生在你运行程序的时候，而在 Java 中，这两个阶段是明显分开的。

# 在 Python 中如何定义抽象类？

您通过从`abc`模块继承`ABC`类来定义抽象类:

```
from abc import ABC 
class AbstractCar(ABC): 
    @abstractmethod 
    def drive(self): 
        pass
```

要实现该类，只需继承它:

```
class ToyotaSupra(AbstractCar): 
    def drive(self): 
        print('brrrr sutututu')
```

# 结束语

感谢您的阅读，祝您下次面试一切顺利。

## 资源

*   [深度 Python 上下文管理器](https://levelup.gitconnected.com/python-context-managers-in-depth-52dd2dd2624b)