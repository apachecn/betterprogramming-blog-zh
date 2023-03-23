# Python 中的 Lambda、地图和过滤器

> 原文：<https://betterprogramming.pub/lambda-map-and-filter-in-python-4935f248593>

## 每个函数的语法和用法

![](img/4396202bc16e22e0ead636de26df3742.png)

由 [Taras Shypka](https://unsplash.com/@bugsster?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/function?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

今天的文章涵盖了在 Python 中使用 lambda、map 和 filter 函数。我们将介绍每种方法的基本语法，并通过一些例子来熟悉它们的使用。我们开始吧！

# ***λ***

一个`lambda` 运算符或`lambda` 函数用于在 Python 中创建小型的、一次性的、匿名的函数对象。

## **基本语法**

```
lambda arguments : expression
```

一个`lambda`操作符可以有任意数量的参数，但只能有一个表达式。它不能包含任何语句，并返回一个可以赋给任何变量的函数对象。

## **示例**

让我们来看看 Python 中的一个函数:

上面的函数名为`add` *，*，它需要两个参数`x`和`y` ，并返回它们的和。

让我们看看如何将上述函数转换成一个`lambda`函数:

在`lambda x, y: x + y;` `x`和`y`中，是函数的参数，而`x + y`是执行的表达式，其值作为输出返回。

`lambda x, y: x + y` 返回一个可以赋给任何变量的 function 对象，在这种情况下，function 对象被赋给`add`变量。

如果我们检查`add`的类型，它是一个`function`T53。

更重要的是，`lambda`函数作为参数传递给期望函数对象作为参数的函数，如`map`、`reduce`和`filter`函数。

# ***地图***

## **基本语法**

```
map(function_object, iterable1, iterable2,...)
```

`map` 函数期望一个函数对象和任意数量的`iterables`，如`list`、`dictionary`等。它对序列中的每个元素执行`function_object`,并返回由函数对象修改的元素的`list`。

## **示例**

在上面的例子中，`map`对列表中的每个元素`[1, 2, 3, 4]`执行`multiply2`函数，并返回`[2, 4, 6, 8]`。

让我们看看如何使用`map`和`lambda`编写上面的代码。

就一行代码！

## **使用 Map 和 Lambda 迭代字典**

在上面的例子中，`dict_a` 的每个`dict`都将作为参数传递给`lambda` 函数。每个`dict`的`lambda` 函数表达式的结果将作为输出给出。

## **映射函数的多重迭代**

我们可以将多个序列传递给`map`函数，如下所示:

这里，`list_a` 和`list_b` 的每个`i^th`元素将作为参数传递给`lambda` 函数。

在 Python3 中，`map` 函数返回一个`iterator` 或`map object`，它们被延迟求值，类似于`[zip](https://medium.com/@happymishra66/zip-in-python-48cb4f70d013)`函数的求值方式。懒惰评估在`[zip](https://medium.com/@happymishra66/zip-in-python-48cb4f70d013)`函数文章中有更详细的解释。

我们不能用 index 访问`map object`的元素，也不能用`len()`找到`map object`的长度。

然而，我们可以强制将`map`输出，即`map object`转换为`list`，如下所示:

# **过滤器**

## **基本语法**

```
filter(function_object, iterable)
```

`filter` 函数需要两个参数:`function_object`和一个 iterable。`function_object`返回一个布尔值，并为`iterable`的每个元素调用。`filter`只返回那些`function_object`返回`True`的元素。

与`map` 函数一样，`filter` 函数也返回元素列表。与`map` *不同，* `filter` 功能只能有一个`iterable`作为输入。

## 例子

偶数使用`filter`功能:

`dicts`的过滤器`list`:

与`map`类似，Python3 中的`filter` 函数返回一个`filter object`或被延迟求值的迭代器。我们不能用 index 访问`filter object`的元素，也不能用`len()`求 f `ilter object`的长度。

**如果你喜欢我的文章，觉得它们很有用，请随意给我买杯咖啡。谢谢！**

[![](img/637b13257747e3091732494f68bbc5b4.png)](https://www.buymeacoffee.com/rupeshmishra)

**要获得我的新故事的更新，请关注我的**[**medium**](https://medium.com/@happymishra66)**和** [**Twitter**](https://twitter.com/happyrupesh123)

**其他文章**

1.  [Python 中的 Zip](https://medium.com/@happymishra66/zip-in-python-48cb4f70d013)
2.  [Python 中的装饰者](https://medium.com/@happymishra66/decorators-in-python-8fd0dce93c08)
3.  [在 Python 中连接两个列表](https://medium.com/@happymishra66/concatenating-two-lists-in-python-3cf9051da17f)
4.  [用 Python 列出理解](https://medium.com/@happymishra66/list-comprehension-in-python-8895a785550b)