# 如何在 JavaScript 中创建 Python range()

> 原文：<https://betterprogramming.pub/using-python-range-in-javascript-1e58c7bd339c>

## 通过迭代器在 JavaScript 中使用广受欢迎的 Python range()操作符

![](img/29f1d95cda782888e9c650823b1a8761.png)

照片由[玉笛·西尔弗克劳德](https://unsplash.com/@silvercloud?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 什么*是*Python range()类型？

如果您不熟悉 Python，`range()`指的是使用`range`类型创建不可变的数字序列。

> “`range`类型代表一个不可变的数字序列，通常用于在`for`循环中循环特定的次数。”—[docs.python.org](https://docs.python.org/3/library/stdtypes.html#range)

`range()`构造函数有两种形式的定义:

```
range(stop)
range(start, stop[, step])
```

参数、返回值等的简明解释。可在[程序](https://www.programiz.com/python-programming/methods/built-in/range)中找到。

几个例子:

# 在 JavaScript 中构建 range()函数

为了简单起见，我们将忽略可选的`step`参数。

通过使用`Array`构造函数、`fill`和`map`，您可以在快速的一行程序中找到一个简单的解决方案:

```
new Array(stop - start).fill(start).map((el, i) => el + i)
```

然后可能会提供一个更完整的解决方案，涵盖只使用一个参数调用`range`的情况:

但是现在还不是时候。你能看出为什么这个解决方案是错误的吗？

记住，调用 Python `range`会返回一个**不可变的数字序列**。注意为了得到熟悉的列表数据结构，上面的 Python 例子是如何用`list()`包装`range`的返回值的。

JavaScript 中一个类似的例子应该是这样的:

```
> Array.from(range(1, 11))
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

那么如何才能让 JavaScript 中的函数返回一个“不可变的数字序列”呢？有什么方法可以在 JavaScript 中实现`range()`返回值的相同结构？

# 迭代器来拯救！

迭代器在不同的编程语言中被广泛使用，以允许我们迭代不同的数据结构。

> “在 JavaScript 中，**迭代器**是一个对象，它定义了一个序列，并可能在序列终止时返回一个值。”——[developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators)

具体来说，在 JavaScript 中，迭代器是通过使用一个返回具有两个属性的对象的`next()`方法来实现[迭代器协议](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterator_protocol)的任何对象:

```
next() {
  ...
  return {
    value: // current value to be passed
    done: // did we finish iterating over the data structure?
  }
}
```

使用迭代器，您可以提供自己的迭代逻辑。例如，下面是一个简单的迭代器，它将跳过每隔一项:

更重要的是，如果我们创建一个定义了返回迭代器的`[Symbol.iterator]`的对象，我们就可以得到我们想要的行为:

试验这些例子，看看你能创建什么样有趣和有用的迭代器。

到现在为止，你大概可以想象我们如何用 JavaScript 创建 Python `range()`。我是这样实现的:

正如我提到的，为了简单起见，这个实现省略了原始 Python `range()`中的`step`参数，但这只是一个额外的逻辑问题，您可以自己实现。请随意分享您的解决方案。

感谢阅读。