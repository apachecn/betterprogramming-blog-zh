# 使用 JavaScript Math 对象进行计算

> 原文：<https://betterprogramming.pub/using-the-javascript-math-object-for-calculations-496ff3d22059>

## 看看数学对象的属性

![](img/7d7780eb17e4b49efb26de6c8c2adff0.png)

照片由[JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 JavaScript 中，`Math`对象是一个内置对象，让我们可以进行各种数学运算。

它是一个非函数对象，有数学常数和函数来计算各种量。它只适用于数字类型的变量，不适用于`BigInt`数据。

`Math`对象不是一个构造函数。所有的方法都是静态的。我们可以参考像`Math.PI`这样带点符号的成员来得到圆周率的值，或者用`Math.cos(x)`调用余弦函数。

在 JavaScript 中，常量的定义是完全精确的，允许使用实数。

# 常数

`Math`对象具有以下常量:

## 数学。E

欧拉常数和自然对数的底，大约为 2.718281828459045。

## 数学。LN2

2 的自然对数，约为 0.6931471805599453。

## 数学。LN10

10 的自然对数，约为 2.302585092994046。

## 数学。LOG2E

E 的以 2 为底的对数，约为 1.4426950408889634。

## 数学。LOG10E

E 的以 10 为底的对数，约为 0.4342944819032518。

## 数学。产品改进(Product Improve)

圆的周长与直径之比，大约为 3.141592653589793。

## 数学。SQRT1_2

1/2 的平方根，约为 0.7071067811865476。

## 数学。SQRT2

2 的平方根，约为 1.4142135623730951。

# 方法

对象有许多方法，像三角函数、幂函数和对数函数。

像`sin()`、`cos()`、`tan()`、`asin()`、`acos()`、`atan()`、`atan2()`这样的三角函数期望以弧度为单位的角度作为自变量，并且返回以弧度为单位的角度。

要将弧度转换为角度，除以`(Math.PI / 180)`，然后乘以这个值，以另一种方式进行转换。

结果的精度取决于浏览器，因为每个浏览器引擎实现浮点计算的方式不同。这意味着不同的浏览器可能会为相同的功能返回不同的结果。

## Math.abs(x)

返回一个数字的绝对值。例如，我们可以如下使用它:

```
Math.abs(2) // returns 2
Math.abs(-2) // returns 2
```

## Math.acos(x)

返回一个数的反余弦值。例如，我们可以如下使用它:

```
Math.acos(1) // returns 0
Math.acos(-1) // returns Math.PI
```

## Math.acosh(x)

返回一个数字的反双曲余弦值。例如，我们可以如下使用它:

```
Math.acosh(1) // returns 0
```

## Math.asin(x)

返回一个数的反正弦值。例如，我们可以如下使用它:

```
Math.asin(1) // returns 1.5707963267948966
Math.asin(0) // returns 0
```

## Math.asinh(x)

返回一个数字的反双曲正弦值。例如，我们可以如下使用它:

```
Math.asin(1) // returns 0.881373587019543
Math.asin(0) // returns 0
```

## 数学.阿坦(x)

返回一个数字的反正切值。例如，我们可以如下使用它:

```
Math.atan(1) // returns 0.7853981633974483
Math.atan(0) // returns 0
```

## Math.atanh(x)

返回一个数字的反双曲正切值。例如，我们可以如下使用它:

```
Math.atanh(1) // returns Infinity
Math.atanh(0) // returns 0
```

## Math.atan2(y，x)

返回其参数商的反正切值。例如，我们可以如下使用它:

```
Math.atan2(1, 1) // returns 0.7853981633974483
Math.atan2(1, Math.SQRT2) // returns 0.9553166181245093
```

## Math.cbrt(x)

返回一个数的立方根。例如，我们可以如下使用它:

```
Math.cbrt(3) // returns 1.4422495703074083
```

## Math.ceil(x)

返回大于或等于一个数的最小整数。例如，我们可以如下使用它:

```
Math.ceil(1.5) // returns 2
```

## Math.clz32(x)

当`x`转换为二进制时，返回 32 位整数的前导零的数量。例如，如果我们写:

```
Math.clz32(1)
```

我们得到 31，因为 1 的数字是 1，而它之前的 31 个数字都是 0。如果我们写下:

```
Math.clz32(2)
```

我们得到 30 是因为 1 的数字是 0，2 的数字是 1，在它之前的其他 30 个数字都是 0。

## Math.cos(x)

返回数字的余弦值。例如，我们可以如下使用它:

```
Math.cos(0) // returns 1
```

## Math.cosh(x)

返回一个数字的双曲余弦值。例如，我们可以如下使用它:

```
Math.cosh(0) // returns 1
```

## 数学表达式(x)

将`e`返回到`x`的幂，其中`x`是自变量，`e`是欧拉常数，约为 2.718281828459045，是自然对数的底。我们可以在下面的代码中使用它:

```
Math.exp(10) // returns 22026.465794806718
```

## Math.expm1(x)

返回减去 1 的`Math.exp(x)`。例如，我们可以在下面的代码中使用它:

```
Math.expm1(1) // returns 1.718281828459045
```

## 数学.楼层(x)

返回小于或等于一个数字的最大整数。例如，我们可以在下面的代码中使用它:

```
Math.floor(1.1) // returns 1
```

## Math.fround(x)

返回数字的最接近的单精度浮点表示形式。

单精度数字有 32 位，第一位用于符号，接下来的 8 位用于指数，其余 23 位是对数的小数部分，也称为尾数。

例如，我们可以在下面的代码中使用它:

```
Math.fround(1.2) // returns 1.2000000476837158
```

## Math.hypot(a，b，c，…)

返回其参数平方和的平方根。它需要无数个参数。例如，我们可以如下使用它:

```
Math.hypot(1,2)  // returns 2.23606797749979
Math.hypot(1,2,3,4,5)  // returns 7.416198487095663
```

## Math.imul(x，y)

返回 32 位整数乘法的结果。它在执行乘法之前将小数四舍五入到最接近的整数。例如，我们可以如下使用它:

```
Math.imul(1,2) // returns 2
Math.imul(3,4.1) // returns 12
```

## Math.log(x)

返回一个数的自然对数(以`e`为底的对数，也称为 ln)。例如，我们可以像下面的代码一样使用它:

```
Math.log(Math.E) // returns 1
Math.log(1) // returns 0
```

## Math.log1p(x)

返回数字 x 的自然对数(以`1 + x`为底的对数`e`，也是 ln)。例如，我们可以像下面的代码一样使用它:

```
Math.log1p(Math.E - 1) // returns 1
Math.log1p(1) // returns 0.6931471805599453
```

## Math.log10(x)

返回一个数以 10 为底的对数。例如，我们可以像下面的代码一样使用它:

```
Math.log10(1) // returns 0
Math.log10(10) // returns 1
```

## Math.log2(x)

返回一个数的以 2 为底的对数。例如，我们可以像下面的代码一样使用它:

```
Math.log2(2) // returns 0
Math.log2(10) // returns 3.321928094887362
```

## Math.max(a，b，…)

返回零个或多个数字中的最大值。如果没有传入任何内容，则返回`-Infinity`。例如，我们可以像下面的代码一样使用它:

```
Math.max(1, 2) // returns 2
Math.max(1,2,3,4,5) // returns 5
Math.max() // returns -Infinity
```

## Math.min(a，b，…)

返回零个或多个数字中的最小值。如果没有传入任何内容，则返回`Infinity`。例如，我们可以像下面的代码一样使用它:

```
Math.min(1,2) // returns 1
Math.min(1,2,3,4,5) // returns 1
Math.min() // returns Infinity
```

## Math.pow(x，y)

返回指数幂的底数。例如，我们可以像下面的代码一样使用它:

```
Math.pow(1,2) // returns 1
Math.min(2,3) // returns 8
```

## Math.random()

返回一个介于 0 和 1 之间的伪随机数。例如，我们可以像下面的代码一样使用它:

```
Math.random() // returns 0.5275086314071882 or any other number between 0 and 1
```

## Math.round(x)

返回舍入到最接近的整数的数值。例如，我们可以像下面的代码一样使用它:

```
Math.round(1.2) // returns 1
```

## 数学符号(x)

返回 x 的符号，指示 x 是正数、负数还是零。如果`x`为正，则返回 1。如果`x`为负，则返回-1。如果`x`为 0，则返回 0。例如，我们可以在下面的代码中使用它:

```
Math.sign(1) // returns 1
Math.sign(3) // returns 1
Math.sign(-1) // returns -1
Math.sign(-3) // returns -1
Math.sign(0) // returns 0
```

## Math.sin(x)

返回一个数的正弦值。例如，我们可以在下面的代码中使用它:

```
Math.sin(1) // returns 0.8414709848078965
```

## Math.sinh(x)

返回一个数字的双曲正弦值。例如，我们可以在下面的代码中使用它:

```
Math.sinh(1) // returns 1.1752011936438014
```

## Math.sqrt(x)

返回一个数的正平方根。例如，我们可以在下面的代码中使用它:

```
Math.sqrt(1) // returns 1
Math.sqrt(2) // returns 1.4142135623730951
```

## 数学.谭(x)

返回一个数字的正切值。例如，我们可以在下面的代码中使用它:

```
Math.tan(1) // returns 1.5574077246549023
```

## Math.tanh(x)

返回一个数字的双曲正切值。例如，我们可以在下面的代码中使用它:

```
Math.tanh(2) // returns 0.9640275800758169
```

## Math.trunc(x)

返回数字 x 的整数部分，去掉所有小数部分。例如，我们可以在下面的代码中使用它:

```
Math.trunc(2.2223) // returns 2
```

# 扩展数学对象

我们可以通过添加自定义属性和方法来扩展`Math`对象，因为它不是一个构造函数。例如，我们可以向 Math 对象添加以下函数来计算一个数的第五个根:

```
Math.fifthRoot = (x) => Math.pow(x, 1/5);
```

在 JavaScript 中，`Math`对象便于进行计算。它有一些有用的常数和方法，用于计算常见的数学运算，如取对数和三角函数。

我们可以通过将方法和属性直接附加到对象上来轻松扩展`Math`对象。