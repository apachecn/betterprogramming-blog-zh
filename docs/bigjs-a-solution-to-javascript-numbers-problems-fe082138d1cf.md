# BigJS 是 JavaScript 数字问题的解决方案吗？

> 原文：<https://betterprogramming.pub/bigjs-a-solution-to-javascript-numbers-problems-fe082138d1cf>

## 我们能最终解决客户端的数学问题吗？

![](img/9409bbfb4163a00cc70603864ef3ce5b.png)

由[罗马法师](https://unsplash.com/@roman_lazygeek?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/blackboard?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

众所周知，JavaScript 不擅长处理数字，涉及小数精度的计算可能会让您大吃一惊。幸运的是，有一个轻量级的库为我们提供了一系列工具来处理这个问题: [BigJS](https://www.npmjs.com/package/big-js) 。

让我们看看我们能从中得到什么。

著名的`0.2 + 0.1 === 0.3`返回`false`的例子通常会让编码学徒有点头疼。事实上，这不仅是一个典型的 JavaScript 问题，而且在许多其他编程语言中也存在(在本文中有关于那个[的更多细节)。](https://medium.com/better-programming/why-is-0-1-0-2-not-equal-to-0-3-in-most-programming-languages-99432310d476)

幸运的是，有一些简单的方法可以处理这个问题，而不会浪费太多时间。其中一个是名为 BigJS 的库。顾名思义，它最初是通过使用 JavaScript 类来处理大数字的。

# 和大班一起做

BigJS 引入了一个名为`Big`的类，包含一组可以被调用来对数字进行计算的方法。第一步是创建一个将数字(或一个字符串或另一个`Big`)作为参数传递的`Big`实例:

```
const two = new Big(2)
```

`new`甚至是可选的。这个类有一些特性，比如默认为 20 的十进制精度`Big.DP`(这是一个很大的值),可以由用户定义为 100 万。)，这为所有疯狂的用法留下了很好的回旋余地。

有一套用于评估的方法，如`gt(), gte(), eq()`以及用于计算的方法(`plus(), minus(), times(), sqrt()`)。它们可以链接在一起形成一个相当优雅的语法:

```
const result = Big(2).times(5).minus(2) // 8
```

该库解决了小数字的十进制精度问题:

```
const result = Big(0.2).plus(0.1).eq(0.3) // true
```

以及“大”的:

```
const result = Big(2e-12).plus(1e-12).eq(3e-12) // true
```

官方[文档](https://mikemcl.github.io/big.js/)很好地概述了简短示例的可能性。这组方法相当有限，部分遵循 JS 的`Math`对象模式，显然不是为复杂的数学设计的，而是为了提高精度，同时保持库的简单和小巧。

目前只有好消息？嗯，让我想想…

# 一些缺点

局限性并不总是显而易见的，但还是有的。例如，虽然`pow()`方法允许计算指数，但它只接受整数，所以为了计算`2^3.5`，您必须回到传统的 JavaScript。

大方法的返回值是一个`Big`对象，所以通常情况下，使用`toString()`或`toFixed(...)`进行字符串转换是最简单的方法，然后整个过程必须包装在`Number()`中，这使得语法更加复杂。

不包括对空字符串等虚假值的处理，这可能会导致崩溃——这是使用严格类型的又一个原因。

一般来说，给`Big`输入不需要的类型会导致错误。因此，在调用库之前，必须严格检查和处理打字。

# 性能呢？

检查计算的一个重要方面是它们的运行速度。当然，没有人期望`BigJS`能像普通 JavaScript 一样快，但是让我们看看要付出的代价是什么。我们将比较下面包含基本运算的两个程序块:

![](img/263064b820042e69c0a48ebcd3cea773.png)

对 [JSBEN 进行测试的结果。CH](https://jsben.ch/) 。

结果很明显:使用这个库比用普通 JavaScript 运行计算要慢 300 倍。这可能看起来很可怕，但必须从需要处理的数据量的角度来看。后端中的单个计算或记录的前端处理将不会受到这种计算能力需求的影响。

另一方面，对于大型数据库操作，这可能值得考虑。

为了深入理解为什么会发生这种情况，您可能想要检查库的源代码。它有很好的注释，大部分逻辑都在一个文件中。然而，单个字母的变量名往往会使代码变得晦涩难懂，这可能需要时间来破译逻辑。

# 结论

在计算精度和小数安全性方面，BigJS 绝对是一个不错的选择。它使用起来非常简单，可以压缩到 3 kb 以下，并且在速度和安全性之间取得了很好的平衡。这就是为什么它是大数最常用的库之一。

但是请记住这些注意事项:

1.  问问自己性能是否至关重要。
2.  当心类型！