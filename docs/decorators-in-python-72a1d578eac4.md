# Python 中的装饰者

> 原文：<https://betterprogramming.pub/decorators-in-python-72a1d578eac4>

## 用一个简单的例子简要描述 Python 中的 decorators

![](img/4a04172da422312dd311e6ae88b03996.png)

照片由[沙哈达特·拉赫曼](https://unsplash.com/@hishahadat?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/python-coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 功能

在 Python 中，函数是一级对象，这意味着:

*   函数是对象——它们可以被引用，传递给变量，也可以从其他函数返回。
*   函数可以在另一个函数中定义，也可以作为参数传递给另一个函数。

```
def say_name():
    print("Guido van Rossum")

def say_nationality():
    print("Netherlands")

def say(func):
    return func

say(say_name)()
say(say_nationality)()
```

输出:

```
Guido van Rossum
Netherlands
```

这里我们向`say`函数发送两个不同的函数引用(不是括号),作为再次返回引用的参数。

这就是**内部函数**的工作方式:

```
def say():

    def say_name():
        print("Guido van Rossum")

    def say_nationality():
        print("Netherlands")

    say_name()
    say_nationality()

say()
```

输出:

```
Guido van Rossum
Netherlands
```

# 装修工

您已经看到了函数就像 Python 中的任何其他对象一样，现在让我们举一个例子来看看 Python 装饰器的魔力。

```
def say(func):

    def employer():
        print("Say something about you.")

    def say_name():
        print("My name is Guido van Rossum.")

    def say_nationality():
        print("I am from Netherlands.")

    def wrapper():
        employer()
        say_name()
        say_nationality()
        func()

    return wrapper

@say
def start_interview():
    print("Real interview Started...")

start_interview()
```

输出:

```
Say something about you.
My name is Guido van Rossum.
I am from Netherlands.
Real interview Started...
```

这里我们称之为`start_interview`方法。它转到装饰器函数`say`并定义`employer`、`say_name`、`say_nationality`和`wrapper`函数，最后它返回`wrapper`函数引用，并在调用调用者函数`start_interview`的地方调用它。

# 用参数修饰函数

我们还可以修饰一个接受参数的函数。我们可以在包装函数中使用`*args`和`**kwargs`来接受这些参数。

```
def say(func):

    def wrapper(*args, **kwargs):
        func(*args, **kwargs)
    return wrapper

@say
def greet(name):
    print("Hello {}".format(name))

greet("Goutom")
```

输出:

```
Hello Goutom
```

# 从修饰函数返回

当你的修饰函数返回一些东西时会发生什么？只需从包装器返回调用函数。让我们看一个例子来理解这一点。

```
def my_decorator(func):

    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@my_decorator
def doubled(number):
    return number*2

print(doubled(10))
```

输出:

```
20
```

# 反省

在 Python [中，自省](https://docs.python.org/3/library/inspect.html)是一个对象在运行时知道自己属性的能力。例如，一个函数知道自己的名字和文档。让我们打印出`doubled`函数名。

```
print(doubled.__name__)
```

输出:

```
wrapper
```

但是它应该是`doubled.`在被修饰之后，我们的`doubled`函数已经对它的身份感到困惑，因为它是从`wrapper`函数中被调用的。为了解决这个问题，decorator 应该在`wrapper`函数中使用`[@functools.wraps](https://docs.python.org/library/functools.html#functools.wraps)` decorator，它将保存关于原始函数的信息。添加这个装饰器后，它将返回它原来的名字`doubled`。

# 现实世界的例子

让我们编写一个装饰器，它将以秒为单位计算函数执行时间，并在控制台中打印出来。

```
import functools
import time

def timer(func):

    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start_time = time.perf_counter()
        value = func(*args, **kwargs)
        end_time = time.perf_counter()
        run_time = end_time - start_time
        print("Finished {} in {} secs".format(repr(func.__name__), round(run_time, 3)))
        return value

    return wrapper
```

现在让我们使用这个装饰器:

```
@timer
def doubled_and_add(num):
    res = sum([i*2 for i in range(num)])
    print("Result : {}".format(res))

doubled_and_add(100000)
doubled_and_add(1000000)
```

输出:

```
Result : 9999900000
Finished ‘doubled_and_add’ in 0.0119 secs
Result : 999999000000
Finished ‘doubled_and_add’ in 0.0897 secs
```

# 注册插件

装饰者不必包装他们正在装饰的功能。他们也可以简单地注册一个函数的存在，并将其解包返回。例如，这可以用来创建一个轻量级插件架构:

```
PLUGINS = dict()

def register(func):
    PLUGINS[func.__name__] = func
    return func

@register
def add(a, b):
    return a+b

@register
def multiply(a, b):
    return a*b

def operation(func_name, a, b):
    func = PLUGINS[func_name]
    return func(a, b)

print(PLUGINS)
print(operation('add', 2, 3))
print(operation('multiply', 2, 3))
```

输出:

```
{‘add’: <function add at 0x7fb27f7a8620>, ‘multiply’: <function multiply at 0x7fb27f7a88c8>}
5
6
```

`@register`装饰器只是在全局`PLUGINS`字典中存储一个对被装饰函数的引用。注意，在本例中，您不必编写内部函数或使用`@functools.wraps`，因为您返回的是未修改的原始函数。这种简单的插件架构的主要好处是，您不需要维护存在哪些插件的列表。该列表是在插件注册时创建的。这使得添加新插件变得很简单:只需定义函数并用`@register`修饰它。

# 装饰课

有两种不同的方式在类中使用装饰器。第一种是装饰一个类的方法或者装饰整个类。

## 一些内置的类装饰器

Python 内置的一些常用装饰器有`@classmethod`、`@staticmethod`和`@property`。`@classmethod`和`@staticmethod`装饰器用于定义一个类名称空间内的方法，该方法不与该类的特定实例相关联。`@property`装饰器用于为类属性定制 getters 和 setters。展开下面的方框，看一个使用这些装饰器的例子。你可以在这里了解更多关于内置装饰器[。](https://docs.python.org/3/library/functions.html)

让我们看一个例子。

```
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def radius(self):
        """Get value of radius"""
        return self._radius

    @radius.setter
    def radius(self, value):
        """Set radius, raise error if negative"""
        if value >= 0:
            self._radius = value
        else:
            raise ValueError("Radius must be positive")

    @property
    def area(self):
        """Calculate area inside circle"""
        return self.pi() * self.radius**2

    def cylinder_volume(self, height):
        """Calculate volume of cylinder with circle as base"""
        return self.area * height

    @classmethod
    def unit_circle(cls):
        """Factory method creating a circle with radius 1"""
        return cls(1)

    @staticmethod
    def pi():
        """Value of π, could use math.pi instead though"""
        return 3.1415926535
```

在本课程中:

*   `.cylinder_volume()`是常规方法。
*   `.radius`是一个可变属性:它可以被设置为不同的值。然而，通过定义 setter 方法，我们可以进行一些错误测试，以确保它没有被设置为无意义的负数。属性作为不带括号的属性来访问。
*   `.area`是一个不可变的属性:没有`.setter()`方法的属性不能被改变。即使它被定义为一个方法，它也可以作为一个没有括号的属性被检索。
*   `.unit_circle()`是类方法。它并不局限于`Circle`的一个特定实例。类方法通常用作工厂方法，可以创建类的特定实例。
*   `.pi()`是静态方法。它并不真正依赖于`Circle`类，除了它是其名称空间的一部分。静态方法可以在实例或类上调用。

在控制台中测试:

```
>>> c = Circle(5)
>>> c.radius
5

>>> c.area
78.5398163375

>>> c.radius = 2
>>> c.area
12.566370614

>>> c.area = 100
AttributeError: can't set attribute

>>> c.cylinder_volume(height=4)
50.265482456

>>> c.radius = -1
ValueError: Radius must be positive

>>> c = Circle.unit_circle()
>>> c.radius
1

>>> c.pi()
3.1415926535

>>> Circle.pi()
3.1415926535
```

## 装饰类方法

这里我们使用一个先前创建的`timer`装饰器。

```
class Calculator:

    def __init__(self, num):
        self.num = num

    @timer
    def doubled_and_add(self):
        res = sum([i * 2 for i in range(self.num)])
        print("Result : {}".format(res))

c = Calculator(10000)
c.doubled_and_add()
```

输出:

```
Result : 99990000
Finished 'doubled_and_add' in 0.001 secs
```

## 装饰一个班级

```
@timer
class Calculator:

    def __init__(self, num):
        self.num = num
        import time
        time.sleep(2)

    def doubled_and_add(self):
        res = sum([i * 2 for i in range(self.num)])
        print("Result : {}".format(res))

c = Calculator(100)
```

输出:

```
Finished 'Calculator' in 2.001 secs
```

装饰一个类并不装饰它的方法。这里，`@timer`只度量实例化类所花费的时间。

# 嵌套装饰器

```
def hello(func):
    def wrapper():
        print("Hello")
        func()
    return wrapper

def welcome(func):

    def wrapper():
        print("Welcome")
        func()
    return wrapper

@hello
@welcome
def say():
    print("Greeting Dome")

say()
```

输出:

```
Hello
Welcome
Greeting Dome
```

可以把这想象成装饰者按照它们被列出的顺序被执行。换句话说，`@hello`调用`@welcome`，后者调用`say()`。

# 有争论的装饰者

这里`repeat`处理装饰器的参数。

```
def repeat(*args_, **kwargs_):

    def inner_function(func):

        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            for _ in range(args_[0]):
                func(*args, **kwargs) return wrapper

    return inner_function

@repeat(4)
def say(name):
    print(f"Hello {name}")

say("World")
```

输出:

```
Hello World
Hello World
Hello World
Hello World
```

# 有状态装饰器

我们可以使用装饰器来跟踪状态。作为一个简单的例子，我们将创建一个装饰器来计算一个函数被调用的次数。

```
def count_calls(func):

    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        wrapper.num_calls += 1
        print(f"Call {wrapper.num_calls} of {func.__name__!r}")
        return func(*args, **kwargs)

    wrapper.num_calls = 0
    return wrapper

@count_calls
def say():
    print("Hello!")

say()
say()
say()
say()
print(say.num_calls)
```

输出:

```
Call 1 of 'say'
Hello!
Call 2 of 'say'
Hello!
Call 3 of 'say'
Hello!
Call 4 of 'say'
Hello!
4
```

对函数的调用次数存储在`wrapper`函数的函数属性`num_calls`中。

# 作为装饰者的类

维护状态的最好方法是使用类。如果我们想使用 class 作为装饰器，它需要将`func`作为其`.__init__()`方法中的一个参数。此外，这个类需要是[可调用的](https://docs.python.org/reference/datamodel.html#emulating-callable-objects)，这样它就可以代替修饰函数。对于一个可调用的类，你实现特殊的`.__call__()`方法。

```
class CountCalls:
    def __init__(self, func):
        functools.update_wrapper(self, func)
        self.func = func
        self.num_calls = 0

    def __call__(self, *args, **kwargs):
        self.num_calls += 1
        print(f"Call {self.num_calls} of {self.func.__name__!r}")
        return self.func(*args, **kwargs)

@CountCalls
def say():
    print("Hello!")

say()
say()
say()
say()
print(say.num_calls)
```

输出:

```
Call 1 of 'say'
Hello!
Call 2 of 'say'
Hello!
Call 3 of 'say'
Hello!
Call 4 of 'say'
Hello!
4
```

# 带有参数的基于类的装饰器

```
class ClassDecorator(object):

    def __init__(self, arg1, arg2):
        print("Arguements of decorator %s, %s" % (arg1, arg2))
        self.arg1 = arg1
        self.arg2 = arg2

    def __call__(self, func):
        functools.update_wrapper(self, func)

        def wrapper(*args, **kwargs):
            return func(*args, **kwargs)
        return wrapper

@ClassDecorator("arg1", "arg2")
def print_args(*args):
    for arg in args:
        print(arg)

print_args(1, 2, 3)
```

输出:

```
Arguements of decorator arg1, arg2
1
2
3
```