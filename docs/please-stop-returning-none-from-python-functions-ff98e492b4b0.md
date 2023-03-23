# 停止从 Python 函数返回 None

> 原文：<https://betterprogramming.pub/please-stop-returning-none-from-python-functions-ff98e492b4b0>

## 为什么？因为它容易出错，而且会让呼叫者感到困惑

![](img/32a4ba8023a571a87bec631985510bf1.png)

照片由[亚历山大·波波夫](https://unsplash.com/@5tep5?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/traffic-light?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

通常，在阅读 Python 代码时，您会遇到返回`None`的实用函数。虽然在某些情况下返回`None`看起来很自然，但是应该避免，因为它很容易出错。

# 让我们看看为什么返回 None 容易出错

假设你想写一个简单的库函数，将两个数相除。

当分母为 0 时，该函数返回`None`。这是有意义的，因为结果是未定义的，所以发送`None`听起来很自然。但是，该功能的用户可能会错误地使用它，如下所示。

当分子为 0 时，函数将返回 0，这是意料之中的，但是看看在`if`语句中发生了什么。

它在寻找一个`False` 的等价物，并且会错误地执行，告诉用户你的输入是无效的，这是*而不是*真的！像这样在`if`条件下评估响应容易出错。

# 应该返回什么？

正确的方法是在库函数中抛出一个异常，如下所示。

这样，函数的调用者负责通过[捕捉异常](https://docs.python.org/3/tutorial/errors.html)并采取适当的行动来处理无效用例。呼叫者没有任何猜测或假设，结果更加清晰。

# **资源**

*   [GitHub](https://gist.github.com/i4ali/49a2b3ba56e4debe17cc094c8cb05e18)