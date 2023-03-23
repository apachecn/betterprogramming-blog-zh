# 在 JavaScript 中生成随机数

> 原文：<https://betterprogramming.pub/generating-random-numbers-in-javascript-4b2a1e9d1806>

## 不止是 Math.random()

![](img/58e7aea6b679b5460a76a4a29b420db1.png)

图片由 [NeONBRAND](https://unsplash.com/@neonbrand?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 提供

`Math.random()`会生成一个介于`0`(含)和 1(不含)之间的伪随机浮点数(带小数的数)。

在这里，随机数并不意味着你总是得到一个唯一的数。它只在一段时间间隔后生成相同的数字。这里的间隔很长，所以你很可能不会两次得到同一个数字。

要生成随机数，我们可以使用:

```
Math.random(); //0.6140915758927235 may be  you get different value
```

定义我们自己的随机数函数:

```
function rand(maxLimit = 100) { return **Math.random() * maxLimit**;} rand(10); // 5.500949568251157
```

要获得整数，我们可以使用:

*   Math.floor() →向下舍入
*   Math.ceil() →向上取整
*   Math.round() →到最近的数字

```
// using Math.floorfunction rand(maxLimit = 100) { let rand = Math.random() * maxLimit; **return Math.floor(rand);**}// the above code generates any numbers between **0 to maxLimit-1**
```

得到两个数之间的随机数(`min`包含，`max`排除):

```
function rand(min, max) { let randomNum = Math.random() * (max - min) + min; return Math.floor(randomNum);}rand(10, 20);
```

在上面的代码中，`(max-min) + min`只是用来处理 max 小于 min 的情况。在这种情况下，最大值小于最小值:

```
max = 5, min = 10**(max - min) + min  => (5 - 10) + 5  => -5  + 5 => 0**
```

为了得到包含最小和最大元素的随机数，我们可以将`Math.floor`改为`Math.round`:

```
function rand(min, max) { let randomNum = Math.random() * (max - min) + min; **return Math.round(randomNum);**}rand(10, 20);
```

# 生成随机的十六进制颜色

我们知道十六进制颜色有`1–9`和`a-f`。

要生成随机颜色:

*   在数组中存储十六进制值
*   使用 math.rand 获取索引
*   创建长度为六或三的颜色字符串。

感谢阅读！