# 使用 Walrus 运算符在一行中进行赋值和打印

> 原文：<https://betterprogramming.pub/use-the-walrus-operator-to-assign-and-print-in-one-line-c5c968b119c8>

## Python 中我最喜欢使用的 walrus 操作符

![](img/bbad953560b3aedf019745c1e30f504c.png)

由 [Unsplash](https://unsplash.com/s/photos/print?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的[Cup 先生/杨奇煜·巴拉](https://unsplash.com/@iammrcup?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄

在 [Python](https://www.python.org/) 3.8 中，发布了赋值表达式运算符 [*海象运算符*](https://medium.com/better-programming/what-is-the-walrus-operator-in-python-5846eaeb9d95) 。人们对这一约定褒贬不一，因为有些人认为这种语法违背了 Python“越简单越好”的理念。

我以前发表过一套[三种技术](https://medium.com/better-programming/three-ways-to-use-the-walrus-operator-in-python-d5550f3a7dd)来使用算子；然而，我发现自己最常使用运算符来打印赋给变量的值。

# 我什么时候打印一个赋值？

当向返回响应的外部 API 发出请求时，这种情况最常见。

例如，假设我向一个 API 请求创建一个新对象，响应中返回该对象的 ID。我需要做两件事:

*   打印 ID，这样我就知道它成功了。
*   存储 ID，以便我可以在下游使用它。

# 代码以前的样子

我经常会遇到下面这样的陈述:

```
result = apiRequest()
print(result)resultTwo = apiRequestTwo(result)
```

请注意我是如何打印结果，然后在后续调用中使用它的。

当我需要在 API 请求之前打印一条消息时，代码开始变得复杂。我更喜欢这样写:

```
print("Creating record...", apiRequest())
```

但是，在这种情况下，我不能做这项任务。因此，我必须这样做:

```
print("Creating record...")
result = apiRequest()
print(result)
```

对我来说，将请求和任务夹在打印语句之间并不理想。

# 输入 Walrus 运算符

有了 walrus 操作符，我可以将原来的三行压缩成一行。

```
print("Creating record...", result := apiRequest())
```

这里发生了什么事？

walrus 操作符将把来自我的 API 请求的响应分配给`result`，然后使用该值作为传递给`print()`语句的值。

# 结论

到目前为止，这是我采用新实践的智力投资的最高回报。

如果您发现运营商的其他创造性用途，请分享。这是一个令人兴奋的时刻，我们可以尝试新事物，并学习如何利用它。