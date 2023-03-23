# Python 中的函数式编程简介

> 原文：<https://betterprogramming.pub/introduction-to-functional-programming-in-python-3d26cd9cbfd7>

## 是时候卷起袖子开始了

![](img/44c4b17f73d56b1a2a0803e0c0a1f126.png)

由 [Katerina Limpitsouni](https://twitter.com/ninalimpi) 创作

最近，函数式编程的使用越来越多。因此，许多传统的命令式语言，如 Java 和 Python T21，已经开始支持函数式编程技术。

本文介绍了 Python 中的这些函数式编程技术。本文假设您具备函数式编程的基础知识。如果你不熟悉函数式编程，可以看看 FP 上的这篇文章[。](https://medium.com/@yannickdot/functional-programming-101-6bc132674ec5)

# **一级功能**

在 Python 中，函数是一级的。这意味着它们可以被视为另一种数据类型，比如`int`。

因此，我们可以将它们赋给变量，将它们作为参数传递给其他函数，将它们存储在其他数据结构中，如`dicts`，并将它们用作其他函数的返回值。

## *作为对象的功能*

由于其他数据类型如`string`、`list`和`int`都是对象，所以函数在 Python 中也是对象。让我们考虑一个示例函数`foo`，它只是将自己的名字打印到控制台:

```
def foo():
   print("foo")
```

由于函数是对象，我们可以将函数`foo`赋给任何变量，然后调用该变量来引用函数。例如，我们可以将它赋给变量`bar`，如下所示:

```
bar = foo
bar()
#will print "foo" to the console
```

语句`bar = foo`将`foo`(我们函数的名字)引用的对象分配给变量`bar`。

## *作为功能的对象*

当我们使*可调用，*如`object()` *时，对象可以表现为函数。*这是使用`__call__`方法完成的。

让我们看一个例子:

```
class Greeter:
   def __init__(self, greeting):
      self.greeting = greeting
   def __call__(self, name):
      return self.greeting + " " + name
```

每当我们初始化一个`Greeter`类的对象时，我们就创建一个新的对象，可以用一个新的`name`来调用它。例如:

```
morning = Greeter("good morning") #creates the callable object
morning("john") # calling the object
#prints "good morning john" to the console
```

我们可以调用`morning`对象的原因是我们在类定义中有`__call__`方法。为了检查一个对象是否可调用，我们使用内置函数`callable`:

```
callable(morning) #true
callable(145) #false. int is not callable. 
```

## *数据结构内部的函数*

像其他对象一样，函数可以存储在数据结构中。例如，我们可以创建一个`int`到`func`的字典。当`int`是要执行的程序的简写时，这很有用。

```
# store in dictionary
mapping = {
   0 : foo,
   1 : bar
}x = input() #get integer value from user
mapping[x]() #call the func returned by dictionary access
```

类似地，函数可以存储在许多其他数据结构中。

## *用作自变量和返回值*

函数也可以作为其他函数的参数和返回值。接受或返回函数的函数称为 [*高阶函数*](https://en.wikipedia.org/wiki/Higher-order_function) ，是函数式编程的重要组成部分。

高阶函数非常强大。正如雄辩的 JavaScript 上的精彩解释[:](https://eloquentjavascript.net/05_higher_order.html)

> "高阶函数允许我们抽象出*动作*，不仅仅是值."

考虑一个例子。

假设我们想要遍历一个条目列表并按顺序打印它们。我们可以很容易地构建一个`iterate`函数:

```
def iterate(list_of_items):
    for item in list_of_items:
        print(item)
```

这可能看起来很酷，但这只是抽象的一个层次。如果我们想在遍历列表时做一些不同的事情(而不是打印)呢？

这就是高阶函数的用武之地。我们可以创建一个函数`iterate_custom`,它接受一个要迭代的列表和一个要应用于每一项的函数:

```
def iterate_custom(list_of_items, custom_func):
   for item in list_of_items:
        custom_func(item)
```

虽然这看起来微不足道，但它非常强大。

我们已经提升了一个抽象层，使我们的代码更加可重用。现在，我们不仅可以在想要打印列表时调用该函数，还可以对列表做任何涉及顺序迭代的事情。

函数也可以被返回以使事情更简单。就像我们如何在`dict`中存储函数一样，我们也可以使用一个函数作为控制流来决定合适的函数。例如:

```
def add(x, y):
    return x + ydef sub(x, y):
    return x - ydef mult(x, y):
    return x * ydef calculator(opcode):
    if opcode == 1:
       return add
    elif opcode == 2:
       return sub
    else:
       return mult my_calc = calculator(2) #my calc is a subtractor
my_calc(5, 4) #returns 5 - 4 = 1 my_calc = calculator(9) #my calc is now a multiplier
my_calc(5, 4) #returns 5 x 4 = 20\. 
```

## *嵌套函数*

也可以在其他函数内部定义函数，这些函数被恰当地称为*内部函数。这些对于制作*辅助函数*特别有用——小的、可重用的函数，支持主函数作为子模块。*

当一个问题需要一个特定的函数定义(参数类型或顺序)时，Helper 函数是很方便的，但是不遵循约定更容易解决问题。一个很好的例子来自本讲座幻灯片。

假设你想定义一个斐波纳契函数，`fib(n)`，它有一个参数，`n`，我们必须返回`nth`斐波纳契数。

定义这种函数的一种可能方式是使用跟踪斐波纳契数列的前两项的辅助函数(因为斐波纳契数只是前两个斐波纳契数的和)。

```
def fib(n):
    def fib_helper(fk1, fk, k):
        if n == k:
           return fk
        else:
           return fib_helper(fk, fk1+fk, k+1) if n <= 1:
       return n
    else:
       return fib_helper(0, 1, 1)
```

将计算从函数体转移到函数参数是非常强大的，因为它减少了递归方法中可能出现的冗余计算。

# **单一表达式函数(λ表达式)**

如果我们想写一个没有名字的函数呢？如果我们想写一个简短的单行函数(就像上面例子中的函数`foo`或`mult`)该怎么办？

我们可以用 Python 中的`lambda`关键字来声明这样的函数。例如:

```
mult = lambda x, y: x * y
mult(1, 2) #returns 2
```

这个`mult`的行为与前面使用传统的`def`关键字定义的行为完全相同。

请注意，lambda 函数必须是一行程序，并且不能包含程序员编写的返回语句。

事实上，它们总是有一个隐式的返回语句(在上面的例子中，它会说`return x * y`，但是我们省略了 lambda 函数的显式返回语句)。

lambda 函数更加强大和简洁，因为我们还可以构造匿名函数——没有名字的函数:

`(lambda x, y: x * y)(9, 10) #returns 90`

这是一个方便的方法，只要我们只需要一次函数，以后就不用了。例如，在填写字典时:

```
import collections
pre_fill = collections.defaultdict(lambda: (0, 0))
#all dictionary keys and values are set to 0
```

我们现在可以看看地图，过滤，并减少欣赏λ更多。

# **贴图、过滤、缩小**

## 地图

`map`是一个函数，根据指定的过程(函数)将一组输入转换成另一组输入。这类似于前面讨论的`iterate_custom`函数。例如:

```
def multiply_by_four(x):
    return x * 4scores = [3, 6, 8, 3, 5, 7]
modified_scores = list(map(multiply_by_four, scores))
#modified scores is now [12, 24, 32, 12, 20, 28]
```

在 Python 3 中，`map`函数返回一个`map`对象，可以将其类型转换为`list`供我们使用。现在，我们可以定义一个`lambda`表达式，而不是显式定义`multiply_by_four`函数:

```
modified_scores = list(map(lambda x: 4 * x, scores))
```

当我们想对集合中的所有值做一些事情时,`map`函数很有用。

## 过滤器

`filter`顾名思义，就是帮助“过滤”掉不需要的项目的功能。例如，我们可能希望从`scores`中过滤掉所有奇数。我们可以使用`filter`来实现:

```
even_scores = list(filter(lambda x: True if (x % 2 == 0) else False, scores))
#even_scores = [6, 8]
```

由于提供给`filter`的函数根据具体情况决定是否接受某个项目，因此该函数必须返回一个`bool`值(如上面的 lambda 函数所示)，并且必须是一元的(接受一个输入参数)。

## 减少

`reduce`是对一组数据进行“汇总”或“概括”的功能。例如，如果我们想计算所有分数的总和，我们可以使用`reduce`:

```
sum_scores = reduce((lambda x, y: x + y), scores)
#sum_scores = 32
```

这比写循环简单多了。注意，提供给`reduce`的函数需要两个参数:一个代表当前被检查的项目，一个是应用操作的累积结果。

请注意，以上只是让您开始使用 Python 进行函数式编程，尽管是彻底地开始。还有更多信息需要了解，您可以使用以下资源:

[](https://kite.com/blog/python/functional-programming/) [## 在 Python - Kite 博客中使用函数式编程的最佳实践

### 函数式编程(FP)是一种编码范例，其中的构建块是不可变的值和“纯……

kite.com](https://kite.com/blog/python/functional-programming/) [](https://www.hackerearth.com/practice/python/functional-programming/functional-programming-1/tutorial/) [## 函数式编程教程和笔记| Python | HackerEarth

### 函数式编程是一种编码风格，专注于定义做什么，而不是执行一些动作…

www.hackerearth.com](https://www.hackerearth.com/practice/python/functional-programming/functional-programming-1/tutorial/)