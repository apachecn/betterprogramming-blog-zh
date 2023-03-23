# 用 JavaScript 解释数学的适马(σ)符号

> 原文：<https://betterprogramming.pub/maths-sigma-%CF%83-symbol-explained-with-javascript-a10f78c9786f>

## 当你把它看作代码时，这很容易

![](img/57922c278d687b376b72774adc6578bd.png)

照片由 [Antoine Dautry](https://unsplash.com/@antoine1003?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 数学中的适马(σ)符号

在数学中，sigma 符号σ用于表示在用规则对每个数字进行运算后，要将一系列数字相加。

例如，您可能希望在将数字 1 到 3 乘以 2 后，将它们相加。大概是这样的:

```
Operate on 1 through 3 by multiplying each by 2
1 x 2 = 2
2 x 2 = 4
3 x 2 = 6Add up the results
2 + 4 + 6 = 12
```

这可以用数学方法表示为:

```
Add up a sequence from 1 to 3 after multiplying each by 2:
3
**Σ**    2a
i=1"a" represents the current number in the sequence
```

概括起来就是:

```
Add up a sequence from 1 to 3 after multiplying each by 2:
n
**Σ**    2a
i=m"a" is the current number in the sequence
"n" is the upper bound number
"m" is the lower bound number
```

这可以用任何软件语言写成一个函数。这里我用 JavaScript 来说明。

# 地面工程

首先，让我们创建一个函数来模拟**σ**的全部功能。

```
function mathSumSymbol () {}
```

从这里开始，有很多方法可以实现这一点。我来演示一个。

# 以 1 为增量遍历下限和上限

让我们首先处理从下限到上限的迭代。这是我们一直用`for`循环做的事情，所以让我们使用它。

需要特别提到的是，上界是包含性的，所以指标必须小于等于( **< =** )上界，不能小于( **<** )。

```
function mathSumSymbol (lowerBound, upperBoundInclusive) {
  for(let i = lowerBound; i <= upperBoundInclusive; ++i) { }
}
```

同样重要的是要注意，我们的增量是 1 ( `++i`)。数学σ符号总是从下限加 1，直到达到或超过上限。

# σ的结果是累积

σ符号产生一个累加值。让我们添加一个变量来跟踪累积并从函数返回。

```
function mathSumSymbol (lowerBound, upperBoundInclusive) {
  let accumulator = 0;
  for(let i = lowerBound; i <= upperBoundInclusive; ++i) { } 
  return accumulator;
}
```

# 高阶函数

σ符号实际上是一个高阶函数。也就是说，它是一个接受另一个函数作为其参数之一的函数。

在软件中，对列表中的每一项进行操作的函数称为迭代器。让我们给函数签名添加一个参数`iterator`。

```
function mathSumSymbol (lowerBound, upperBoundInclusive, iterator) {
  let accumulator = 0;
  for(let i = lowerBound; i <= upperBoundInclusive; ++i) {
    accumulator += iterator(i);
  } 
  return accumulator;
}
```

此外，我们已经更新了`for`循环中的逻辑，使用传入的索引值执行迭代器。这个迭代器执行的结果被添加到累加器中(`+=`)。

就这样，我们结束了！

# 决赛成绩

请参见下面的代码片段。我已经改变了一些变量，但除此之外，都是一样的:

如果我们用我们的原始参数执行这个，我们得到:

```
function multiplyEachByTwo (a) {
  return 2 * a;
}mathSigmaSymbol(1, 3, multiplyEachByTwo)
// 12
```

呜哇，12！有用！

# 摘要

当转换成 JavaScript 中的函数时，数学和(σ)符号接受三个参数:下界、上界和迭代器。

该逻辑在从下限到上限(包括上限)的范围内迭代，并且在每次迭代时将索引传递给迭代器。每个迭代器的结果被添加到一个累加器中。累加器从函数返回。

# 资源

*   [https://en.wikipedia.org/wiki/Summation](https://en.wikipedia.org/wiki/Summation)