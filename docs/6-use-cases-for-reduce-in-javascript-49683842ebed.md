# JavaScript 中 Reduce()的 6 个用例

> 原文：<https://betterprogramming.pub/6-use-cases-for-reduce-in-javascript-49683842ebed>

## 学习 reduce()的不同用例，丰富您的 JavaScript 知识

![](img/8654d108010b7913979c8bf8b1f3dfd2.png)

照片由[雷米·米勒](https://unsplash.com/@remimullercha?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

reduce 方法在数组的每个元素上执行用户提供的回调函数“reducer”。它传递前面元素的计算返回值。结果是单个值。这是在数组的所有元素上运行缩减器的结果。

# 它是如何工作的？

reducer 函数逐个元素地遍历数组。在每一步中，reducer 函数都将当前数组值与上一步的结果相加，直到不再有元素需要添加为止。

# 参数是什么？

这些参数是一个回调函数和一个可选的初始值。

## **回调函数**

*   `preVal` : 是前一次回调函数得到的前一个值
*   `currVal` : 是 reducer 函数遍历的数组的当前元素。
*   `currentIndex`:当前指数。但是，它是可选的。
*   `arrayToTraverse`:是要遍历的数组。它也是可选的。

## **初始值**

初始值参数是可选的。这是第一次调用回调时将前一个值初始化为的值。如果未指定初始值，则将前一个值初始化为初始值，并将当前值初始化为数组中的第二个值。

Reduce 方法在某些情况下是一种有价值的方法。在某些情况下，它可以节省您的时间，或者减少您编码的行数。在本文中，我将向您展示 JavaScript 中`reduce()`方法的六个用例。

# 1.对数组的元素求和

假设您有以下数组:

```
const prices = [5.99, 2.99, 3.99, 11.59]
```

您可能更喜欢使用 for 循环或 for-each 循环来对上面的数组元素求和，但是，如果使用`reduce()`，您可以编写更少的代码。

正如您在简介中所知道的，`reduce()`接受一个有四个参数的函数。`preVal, currVal, currentIndex, prices`在这种情况下。

reduce 需要传递的第二个参数是要开始的初始值。例如，如果您希望对一个数组的元素求和，可以将 0 作为初始值。

```
const sum = prices.reduce((preVal, currVal, currentIndex, prices) => {
  return preVal + currVal;
}, 0);
```

对数组中的每个元素执行该函数。`preVal`对于第一次执行的初始值你设置为第二个参数，在本例中它是" 0 "。否则将是未定义的。`currVal`是数组的第一个元素。对于第二次执行，对数组中的第二个元素执行以下函数:

```
(preVal, currVal, currentIndex, prices) => {
  return preVal + currVal;
}
```

现在，`preVal`不是 0。它等于上一次执行中返回的值。所以，`preVal = 5.99 + 0`。现在，`currVal`是第二次迭代的第二个元素。

这种情况下不需要`currentIndex`和`prices`。所以，你可以把这个函数写成一行程序。

```
const sum = prices.reduce((preVal, currVal) => preVal + currVal, 0);
```

# 2.展平数组

使用`reduce()`，可以将多维数组转换成一维集合。

```
var rows = [[2, 3, 5], [1, 2, 4], [8, 5, 5]]var matrixElements = rows.reduce( function (prev, current) {
  return prev.concat(current);
});
```

# 3.创建管道

此外，你可以减少超过函数以及数字和字符串。假设你有一系列数学函数。例如，您有以下功能:

```
function increment(input) { return input + 1;}
function decrement(input) { return input - 1; }
function double(input) { return input * 2; }
function halve(input) { return input / 2; }
```

此外，您还想在订单中使用增量、双倍和减量。所以，你可以创建一个管道。然后，可以配合`reduce()`使用。

作者简介

# 4.从数组中获取一个对象

例如，你有一个字符串数组，是彩色的苹果。使用`reduce()`你可以如下构造一个苹果对象。

要旨由[作者](https://medium.com/@onurinanc)

# 5.找出奇数次出现的整数

您可以使用`reduce()`来查找在给定数组中出现奇数次的整数。下面这个函数有一个不好的地方。如果你给出一个以上奇数次出现的整数，那就不行。

```
const findOdd = (a) => a.reduce((a, b) => a ^ b);console.log(findOdd([1, 2, 2, 1])); // Result: 0
console.log(findOdd([1, 2, 2, 1, 2, 3, 3])); // Result: 2
console.log(findOdd([1, 2, 3, 4])); // Result: 4 -> Not working
```

# 6.找出给定数组的最大子数组和

使用这个函数，您可以找到给定数组的最大子数组和。这是一个需要解决的大问题。不容易解决。尤其是如果你想用一行程序实现这个功能，就更有挑战性了。不过如果用`reduce()`会更容易。

```
const maxSequence = (a,sum=0) => a.reduce((max,v) => Math.max(sum = Math.max(sum + v, 0), max), 0);console.log(maxSequence([1,2,3,4])); // Result: 10
console.log(maxSequence([1,2,3,4,-8])); // Result: 10
console.log(maxSequence([1,-2,3,-2,5,-1,3,-9,1,6])); // Result: 8
```

# 加成:reduceRight()

也可以用`reduceRight()`。其工作方式与`reduce()`相同。然而，它的作用方向相反。正如您所了解的，`reduce()`从第一个元素开始，向最后一个元素行进。相反，`reduceRight()`从最后一个元素开始，到第一个元素。

要旨由[作者](https://medium.com/@onurinanc)

# 结论

了解 JavaScript 中一些内置函数的用例可以帮助你提高编码技能。学习用例可以让你获得一些洞察力。可以优雅的实现一些功能。

`reduce()`在 JavaScript 中也是一个有用的内置函数。如果你知道如何使用它，它是强大的。这有助于您编写更少的代码行，就像对价格求和的例子一样。创建管道也是`reduce()`的一个有趣的用例。

综上所述，本文中`reduce()`的用例如下:

*   对数组的元素求和
*   展平数组
*   创建管道
*   从数组中获取对象
*   寻找奇数次出现的整数
*   寻找给定数组的最大子数组和。