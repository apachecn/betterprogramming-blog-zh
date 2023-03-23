# Python 中的生成器、产量、流是什么？

> 原文：<https://betterprogramming.pub/what-are-generators-yields-and-streams-in-python-a94072b3eb93>

## 中级 Python

![](img/6bf26928d13258eee4b2d7a8a95611ce.png)

由 [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/items?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

`generator`和`yield`在 Python 中使用频率很高。在本文中，让我们讨论一下`generator`的一些基础知识，使用`generator`的好处，以及我们如何使用`yield`来创建一个`generator`。

同时，我们将学习计算机科学中的另外两个概念:惰性求值和流。

# 可重复的

首先，在理解如何使用`generator` 之前，我们需要知道什么是*iterables*——因为`generator`本质上也是一个迭代器。

在 Python 中，iterable 是一个可以迭代的对象，比如在一个`for`循环中。

大多数集合数据结构都是可迭代的，比如列表、元组和集合。例如，下面我们将创建一个列表并逐个迭代它:

```
lst = [1, 2, 3]
for i in lst:
    print(i)# 1
# 2
# 3lst = [x+x for x in range(3)]
for x in lst:
    print(x)
# 0
# 2
# 4
```

我们还可以迭代字符串中的字符:

```
string = "cat"
for c in string:
    print(c)# c
# a
# t
```

# 可迭代的极限

iterables 的限制是，在开始迭代之前，我们需要将所有的值存储在内存中。在某些情况下，这将耗费太多的内存。一个典型的场景是从文件中读取行:

```
def file_reader(file_path):
    fp = open(file_path)
    return fp.read().split("\n")
```

想想如果我们读取一个大文件，比如一个大小为 6 GB 的文件，会发生什么？

当从文件中加载内容时，我们需要将所有的行保存在内存中。

实际上，在大多数情况下，我们只想逐行迭代来完成一些数据处理任务。我们可能会提前打破循环——没有必要将所有行都加载到内存中。

我们能有一个按需读取数据的策略吗？Python 引入了`generator`来解决这个问题。

# 发电机

一个`generator`也是一个迭代器，但是它的关键特性是[](https://en.wikipedia.org/wiki/Lazy_evaluation)**。*懒求值是计算机科学中的一个经典概念，已经被很多编程语言采用，比如 Haskell。懒惰评估的核心思想是*按需调用。*懒惰求值会导致内存占用的减少。*

*生成器是一种按需迭代风格的迭代器。我们不会立刻计算和存储这些值，而是在迭代时动态生成它们。*

*我们有两种创建`generator`的方法:通过生成器表达式或生成器函数。*

*生成器表达式类似于列表理解——除了我们使用`()`。由于`generator`是一个迭代器，我们使用`next`函数来获取下一项:*

```
*g1 = (x*x for x in range(10))
print(type(g1))
print(next(g1))
print(next(g1))# <type 'generator'>
# 0
# 1*
```

*这里的区别在于，我们在创建`generator`时并不计算所有的值。`x*x`是在我们迭代`generator`时计算的。*

*为了理解区别，让我们运行下面的代码片段:*

```
*>>> import timeit
>>> timeit.timeit('lst = [time.sleep(1) for x in range(5)]', number=2)
10.032547950744629>>> timeit.timeit('lst = (time.sleep(1) for x in range(5))', number=2)
1.0013580322265625e-05*
```

*从结果可以看出，当我们创建一个 iterable 时，大约需要 10 秒钟(因为我们执行了 10 次`time.sleep(1)`)。*

*但是在我们创建`generator`的时候`time.sleep(1)`实际上并没有运行。*

# *产量*

*创建`generator`的另一种方法是使用生成器函数。我们使用关键字`yield`在函数中返回一个`generator`。*

*让我们来看看`fib`的这个函数，它返回一个带有 *n* 个斐波那契数的`generator`:*

```
*def fib(cnt):
    n, a, b = 0, 0, 1
    while n < cnt:
        yield a
        a, b = b, a + b
        n = n + 1g = fib(10)
for i in range(10):
    print g.next(),# 0 1 1 2 3 5 8 13 21 34*
```

*让我们用`yield`重写上面的文件读取程序:*

```
*def file_reader(file_path):
    for row in open(file_path, "r"):
        yield rowfor row in file_reader('./demo.txt'):
    print(row),*
```

*通过这种方式，我们不会将所有内容加载到内存中，而是通过读取行来加载。*

# *溪流*

*有了生成器，我们可以构建一个包含无限项的数据结构。这种数据元素序列在计算机科学中被称为[T21 流](https://en.wikipedia.org/wiki/Stream_(computing)) 。这很奇妙，因为我们可以用数学来表达无限的概念。*

*假设我们想要一个包含所有斐波那契数列的序列。我们如何实现这一目标？*

*我们只需要从上面的函数中删除 count 参数。*

```
*def all_fib():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + ball_fib_numbers = all_fib()*
```

*是啊！我们得到了一个可以代表所有斐波那契数列的变量。让我们编写一个通用函数，从任何流中获取 n 个条目。*

```
*def take(n, seq):
    result = []
    try:
        for i in range(n):
            result.append(next(seq))
    except StopIteration:
        pass
    return result*
```

*因此`take(10, all_fib_numbers)`将返回前 10 个斐波那契数作为结果。*

# *结论*

*`generator`在 Python 中是一个延迟计算、节省时间和空间的强大工具。*

*懒求值的核心思想是计算值，直到你真正需要它。这也有助于我们表达无限序列的概念。*