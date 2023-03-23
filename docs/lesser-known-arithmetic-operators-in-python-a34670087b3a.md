# Python 中鲜为人知的算术运算符

> 原文：<https://betterprogramming.pub/lesser-known-arithmetic-operators-in-python-a34670087b3a>

## 超越加法、减法、乘法和除法

![](img/4e0dd9c0e59c248a5c5345d466a81b72.png)

照片由[hello queue](https://unsplash.com/@helloquence?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/math?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

作为一个自学编程的非数学专业的人，我发现我的一般知识中的差距与天资没有太大关系，更多的是与接触有关。

这里有三个方便的算术(数学)运算符，您可能没有意识到，但它们将对您的编程之旅有所帮助。

# 指数运算符

取幂运算符`**`取代了过去科学计算器中的传统插入符号`^`。

不用将一个变量相乘三次，或者使用外部库，比如 JavaScript 中的`[Math.pow()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/pow)`，只需使用双星号将左边的操作数提升到右边的幂。

```
x = 5
x_cubed = x ** 3
x_to_the_eighth = x ** 8
```

# 整数除法运算符

您是否曾经需要通过删除小数位数来截断一个值？JavaScript 有`Math.floor()`方法向下舍入；然而，这在 Python 中是不必要的。

Python 中的双正斜杠被称为整数除法运算符。本质上它会把左除以右，只保留整数部分。

```
x = 8
y = 3
z = x // yprint(z) # 2
```

# 模数算子

如果整数除法运算符给了我们整体，那么我们也需要找到部分的方法。

模数是数学课中被忽略的一种运算，但在编程和数据科学中却非常有帮助。当使用模数运算符`%`时，返回除法的余数。

```
x = 10
y = 4
z = x % yprint(z) # 2
```

如果您想不出使用模操作符的方法，我最常用它来检查奇数或偶数整数。

```
x = 11if x % 2:
   print("odd number")
else:
   print("even number")
```

通过返回 2 的除法的余数，余数将是 0(偶数)或 1(奇数)。