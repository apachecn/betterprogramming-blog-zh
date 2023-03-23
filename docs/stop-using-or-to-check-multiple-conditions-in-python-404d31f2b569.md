# 在 Python 中停止使用“或”来检查多个条件

> 原文：<https://betterprogramming.pub/stop-using-or-to-check-multiple-conditions-in-python-404d31f2b569>

## 有一个更复杂的解决方案

![](img/61dd4b58f26be29897ca89864eb99d0d.png)

照片由[卡斯滕·怀恩吉尔特](https://unsplash.com/@karsten116?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我们都熟悉使用这两条线来检查一个变量是否满足一个或多个等式。

没有比这种编码方式更自然的了，也就是说，用`or`逻辑运算符分隔每个条件。但这并不是故事的全部。

正如我在文章开头所说，有一个复杂的解决方案。这也可能是一个更好的解决方案，或者不是。这取决于你的方面和你如何定义‘更好’。我将给出几种方法，并分析它们的优缺点。

# Python 中的“in”运算符

`in`关键字在 Python 中主要有两种用法:

1:遍历序列:

**例如:**

**结果:**

```
winter
spring
summer
autumn
```

2: 检查一个值是否存在于一个序列(字符串、元组、列表等)中。)还是没有。根据一个值的存在，返回`True`或`False`。

**例 1:**

**结果:**

```
True
False
```

**例二:**

现在我们来看看这一节的副标题:

**结果:**

```
True
```

让我们把目前为止学到的所有东西放在一起，看看优雅的解决方案。

# 用“in”代替“or”

这是我们的第一个例子:

我们将用`in`关键字来改进这个例子:

这意味着如果列表中存在`number`，则满足条件并执行`do_smt()`。换句话说，如果`number`等于**列表中的任意一个**值，`do_smt()`就会运行。请注意，最后一句中的**【任何】**与**或**具有完全相同的含义和功能。这就是为什么可以用`in`代替`or`——两者的目的相同。

我能听到你。你说，等一下！

# 为什么以及何时我们应该选择“in”而不是“or”

复杂的解决方案不是很容易理解吗？是的，它是！看那，我们能容易地跟随条件。在多条件的情况下，使用`in`关键字会大大增加可读性。

比如在[Django REST Framework(DRF)](https://www.django-rest-framework.org/)中，如果要测试一个请求是否是只读操作，只要检查请求的 HTTP 方法(`request.method`)是否存在于`SAFE_METHODS`中就可以了，T1 不过是一个包含`"GET"`、`"HEAD"`、`"OPTIONS"`的元组。

```
**if** request.method **in** permissions.SAFE_METHODS:
    # Whether it is a read-only request
```

让我们显式地编写`permissions.SAFE_METHODS`以便更好地理解:

```
**if** request.method **in** ("GET", "HEAD", "OPTIONS"):
    # Whether it is a read-only request
```

阅读和遵循条件的目的是如此简单。我们很容易看出有多少个条件，它们是什么。如果我问你这里检查哪些条件，你可以很容易地回答这个问题。

在使用`or`的情况下，我们需要这样写:

```
**if** request.method == "GET" **or** request.method == "HEAD" **or** request.method == "OPTIONS":
    # Whether it is a read-only request
```

就可读性而言，最后一个好像不太友好，因为有重复，而且是很长的一行！现在，我问你前一个问题，你再回答这个问题，但是要多花一点时间。对吗？

# 这个解决方案真的是最好的吗？

看你怎么定义**“最好”**。如果你在寻找可读性，很明显`in`是这里最好的**。如果您追求的是性能或内存呢？**

**此外，在使用`in`时，我们不应该只局限于一种数据类型。我们来看看`list`、`tuple`、`set`。**

## **比较执行时间**

**我将使用 [timeit](https://docs.python.org/3/library/timeit.html) 模块来测量执行时间。我们将比较 4 种不同的用法。所以这将是 **1** ( `or` ) **对 3** ( `in`代表`list`、`tuple`和`set`)。**

**本文其余部分的顺序也是一样的:**

```
in [] - list
in () - tuple
in {} - set
or
```

**我会执行每一个`100000`次。此外，我决定重命名请求的 HTTP 方法`method`，因为它更短。**

****结果:****

```
0.0018657920008990914
0.0018152919947169721
0.00212829202064313
0.0020830420253332704
```

**即使我执行了语句`100000`次，仍然很难决定哪个更好，但是我们可以说元组稍微好一点。**

****注意:`timeit.timeit`内的** `globals`参数用于访问全局命名空间中定义的变量。这样，我们就不会评估将`"GET”`值赋给`method`变量所需的时间。否则，我们需要修改它:**

```
print(timeit.timeit('method = "GET"; method in ["GET", "HEAD", "OPTIONS"]', globals=globals(), number=100000))
```

**如果我们想评估一个最坏的情况。这时会满足最后一个条件而不是第一个。**

****结果:****

```
0.0035216250689700246
0.0032744579948484898
0.002372542046941817
0.007088540936820209
```

**现在，`sets`处于优势，`or`表现最差。事实上，`sets`与之前的结果相比没有太大的变化，因为`sets`对于这个过程有`O(1)`的时间复杂度:**

```
x **in** list   ---> O(n)
x **in** tuple  ---> O(n)
x **in** set    ---> O(1)
```

**如您所见，`tuples`和`lists`都有`O(n)`时间复杂度来检查一个项目的存在。因此，如果我们真的关心时间，使用`set`作为数据收集是有益的。它在一天结束时具有`O(1)`时间复杂性。**

## **比较内存使用情况**

**我们可以使用`sys.getsizeof()`来计算对象的内存消耗。**

****结果:****

```
120
64
216
```

**就内存而言，元组比其他数据容器更好。然而，当我们使用`or`时，我们不创建任何序列，所以`or`是内存竞争的赢家！**

# **结论**

**使用`in`是检查多个条件的时髦方式。它使您的代码更具可读性和可维护性。我更喜欢将`in`与`sets`一起使用，因为在考虑性能时`in`关键字更有优势。这也向其他程序员表明，您有时间顾虑，所以您决定使用具有`O(1)`时间复杂性的数据类型。如果您的项目(如嵌入式系统)内存有限，在这种情况下使用`tuple`是有意义的。让我们从现在开始去掉`or`用`in`！**

**我希望你喜欢我的文章。您可能也会对这个感兴趣:**

**[](/3-essential-decorators-in-python-you-need-to-know-654650bd5c36) [## 你需要知道的 Python 中的 3 个基本装饰器

### 更擅长装饰

better 编程. pub](/3-essential-decorators-in-python-you-need-to-know-654650bd5c36)**