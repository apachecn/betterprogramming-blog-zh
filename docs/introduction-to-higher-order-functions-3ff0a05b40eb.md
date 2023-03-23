# 高阶函数介绍

> 原文：<https://betterprogramming.pub/introduction-to-higher-order-functions-3ff0a05b40eb>

## 通过示例理解高阶函数，如 map、filter 和 reduce

![](img/8fb1cc7723adb5bf1c7e4034c1465fe3.png)

由[萨法尔·萨法罗夫](https://unsplash.com/@codestorm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 高阶函数

*高阶函数*是接受另一个函数作为参数或者返回一个函数作为其返回类型的函数。理解这一点最简单的方法是认识到函数可以像任何其他数据一样被处理。

在支持高阶函数的语言中，就像使用`String`或`Integer`一样，您可以将函数作为参数传递、存储并返回。

# 匿名函数

在探索高阶函数之前，首先要了解匿名函数，它通常与高阶函数一起使用。

一个匿名函数，或者说 *lambda* ，仅仅是一个没有任何命名标识符来引用它的函数。它们仍然可以存储在变量中，因此仍然可以在代码中命名，但是函数本身是在没有附加名称的情况下声明的。

例如，在 Java 中，一个简单的匿名函数如下所示:

```
x -> x + 1
```

该功能的输入为`x`，输出为`x + 1`。

匿名函数的语法在不同的语言中略有不同，但是它通常以(输入)→(输出)的形式编写。

通常使用匿名函数，因为它们避免了与将它们正式声明为命名函数相关的样板代码。因此，对于可能不会在多个地方使用的简单函数，比如上面的函数，使用匿名函数可能更合适。

## 例子

现在，我们将进入一个使用匿名函数的高阶函数的非常基本的例子。这个例子使用 Python，其中匿名函数的语法是`lambda (inputs): (output)`。

```
def add_n(n):
    return lambda x: x + n
```

这个例子采用一个数字`n`并返回一个将`n`加到输入中的函数。

所以，如果我们想把 1 + 2 相加，我们可以通过调用`add_n(2)(1)`来实现。第一个调用`add_n(2)`返回一个将 2 加到其输入的函数，第二个调用`(1)`使用该函数将 2 加到 1。

# 常见高阶函数

有几个特定的高阶函数对于理解现代代码库是必不可少的。这些函数主要处理数据列表的迭代或求和。

它们提供了一种更简洁的方式来处理常见的列表操作，使您不必创建大量的助手代码来完成基本的列表操作。这将创建意图更加明确的代码。

对于这些例子，我将通篇使用 Python 式的代码。其他大多数现代语言，比如 Java 和 JavaScript，也有这些特性。

## 地图

`map`操作允许你对列表的每个元素应用一个函数，然后返回一个包含新值的新列表。

如果没有`map`，你的代码应该是这样的:

```
new_list = []
old_list = [...]for e in old_list:
    new_list.append(some_function(e))return new_list
```

在这里，我们的目标是将`some_function`应用到`old_list`的每个元素中。在这个循环的最后，`new_list`现在包含了这个结果。

有了`map`，我们可以用更干净的方式来做这件事。

`map`函数接受一个函数和一个列表，并返回一个新列表，该函数应用于列表的每个元素。它的用法如下:

```
old_list = [...]
return **map**(some_function, old_list)
```

所以，如果`old_list = [1, 2, 3]`和`some_function = lambda x: x + 1`，返回值将是`[2, 3, 4]`。

## 过滤器

`filter`操作允许您返回满足条件的列表元素。

如果没有筛选，完成此任务的代码将如下所示:

```
new_list = []
old_list = [...]for e in old_list:
    if some_function(e):
        new_list.append(e)return new_list
```

这里，我们想要返回`old_list`中`some_function`返回`True`的元素子集。

有了过滤器，我们可以用更干净的方式做到这一点。Filter 接受一个函数和一个列表，并返回一个新的列表，其中只包含函数返回的列表中的元素`True`。

我们按如下方式使用过滤器:

```
old_list = [...]
return **filter**(some_function, old_list)
```

所以，如果`old_list = [1, 2, 3]`和`some_function = lambda x: x == 2`，那么它的返回值就是`[2]`。

## 减少

`reduce`操作允许您基于用于组合列表中所有元素的函数来计算单个值。例如，这可能是将一个列表中的所有元素相加，得到列表的总和。

如果没有`reduce`，您的代码将如下所示:

```
accumulator = 0
old_list = [...]for e in old_list:
    accumulator += ereturn accumulator
```

累加器变量用一个值初始化(本例中为`0`，保存对累加器和每个列表元素应用函数的累积结果。

在这种情况下，功能是`lambda accumulator, e: accumulator + e`。

所以，对于给定的函数`f`，reduce 操作相当于调用`f(...(f(f(initial, list[0]), list[1]),...), list[n])`。

例如，假设`old_list = [1, 2, 3, 4]`，我们使用初始值为`0`的函数`lambda accumulator, e: accumulator + e`。

然后，我们的 reduce 操作的值将是`((((0 + 1) + 2) + 3) + 4)`，这与上面的代码片段的结果完全相同。

我们可以如下使用`reduce`:

```
old_list = [...]
initial = 0
return **reduce**(lambda acc, e: acc + e, old_list, initial)
```

(注意，Python 实际的`reduce`函数不接受初始值作为参数——它只是使用列表的第一个值作为初始值。上面的语法更通用于其他语言。)