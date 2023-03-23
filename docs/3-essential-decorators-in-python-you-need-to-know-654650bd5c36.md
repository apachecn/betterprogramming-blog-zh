# 你需要知道的 Python 中的 3 个基本装饰器

> 原文：<https://betterprogramming.pub/3-essential-decorators-in-python-you-need-to-know-654650bd5c36>

## 更擅长装饰

![](img/d8280e6d7395df331d0c941c2e37d971.png)

[天一马](https://unsplash.com/@tma?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 什么是室内设计师？

在 Python 中，函数与常规对象没有什么不同，它们也是对象。这就是 Python 中的函数如此有用的原因，因为它们可以和 decorators 一起使用。

装饰器本质上是一个为传递给它的函数提供额外功能的函数。所以装饰器修改作为参数传递给自己的函数的行为。

变复杂了？不要担心！装饰者只是函数。它们只是由`'@’`符号指定，这只是语法上的糖。所以装修工都是由`'@’`来装修的。:)

装饰者不一定是一个函数；也可以是一个类。或者它可以接受一个类作为参数。为了简单起见，我在这里只讨论函数装饰器。

# 为什么要用装修工？

当然是为了增加代码的可读性！此外，为了减少代码重复，如果有一些函数有类似的行为。

但是首先，让我们记住如何使用装饰器。

# 装修工怎么用？

第 1 行— `funtion_to_decorate`被传递给装饰器函数。

第 2 行——我们将在`wrapper`函数中构建我们的逻辑。

第 3 行— `funtion_to_decorate()`被执行。

第 4 行— `wrapper`函数返回。

现在，我们准备看例子。

# 1.执行计时器装饰器

你写了一个函数，想知道它需要多长时间。这可以使用以下代码实现:

注意，我们将带有`*args`和`**kwargs`的函数参数传递给包装器。并且不要忘记返回结果。

第 4 行—获取开始时间。

第 5 行—调用我们想要测量其运行时间的函数，并将返回值赋给一个变量。离开装饰器时返回这个值。

第 6 行—用`f-string`打印出经过的时间。

第 7 行—返回结果值。

就是这样！

让我们测试我们的代码。

## **例 1**

## **结果**

```
Elapsed time is 1.9073486328125e-06 ms
```

## **例 2**

## **结果**

```
Elapsed time is 0.5003328323364258 ms
Elapsed time is 1.0017611980438232 ms
Elapsed time is 1.5045099258422852 ms
Elapsed time is 2.003535747528076 ms
```

最后值得一提的是:在装饰函数中使用`f-string`提高了代码的可读性。[我用 Python 写了一篇关于`f-string` s 的文章](https://medium.com/r?url=https%3A%2F%2Fpython.plainenglish.io%2Fget-better-at-python-f-strings-83b123bb4ce0)，所以你可能想看看。

# 2.伐木装饰工

logger decorator 显示了可以写入的函数名和相应的时间戳值。

## **例子**

## **结果**

```
_________________________
Run on: 2021-10-15 23:21:57
shutdown
System shutdown
_________________________
_________________________
Run on: 2021-10-15 23:21:57
restart
System restarts
_________________________
```

# 3.HTML 生成器装饰器

人们说 HTML 不是编程语言。但是 Python 是！迄今为止最强的语言之一。

我们可以用 decorators 创建一个 HTML 生成器。

让我们试着用一个装饰器写下面一行:

```
<h1>I love Python</h1>
```

它有两个部分:

*   **HTML 标签**:也就是`<h1>`
*   **内文**:即`I love Python`

请记住，我们刚刚创建的所有装饰器都为函数赋予了功能。这时，我们需要给出两个功能:一个是 HTML 标签，另一个是内部文本。所以这次新的装饰器将由两个包装函数组成。

## **示例**

## **结果**

```
<div>I love Python</div>
<i>I love Python</i>
```

可以使用多个装饰器。

## **结果**

```
<div><p>I love Python</p></div>
```

# 奖金装饰者

## 结果

```
#################################
Thank you for reading my article
#################################
```

如果你喜欢你所读到的，看看下面这个故事:

[](https://levelup.gitconnected.com/5-powerful-python-one-liners-you-should-know-469b9c4737c7) [## 您应该知道的 5 个强大的 Python 一行程序

### 如果没有 map()函数和理解，我真不知道该怎么办。

levelup.gitconnected.com](https://levelup.gitconnected.com/5-powerful-python-one-liners-you-should-know-469b9c4737c7)