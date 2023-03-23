# 什么是数组中的孔？

> 原文：<https://betterprogramming.pub/what-are-holes-in-arrays-3ac5fcbcd1c>

## 你知道数组可以跳过值吗？

![](img/71f11dc44548ba1d98562e89335f1bcd.png)

Artem Maltsev 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 数组的一个特殊特性是，不是数组中的每个槽都必须填充值。这意味着我们可以跳过如下值:

```
let arr = [];
arr[1] = 1;
arr[10] = 10;
```

我们不必担心为数组的其他槽设置值。

还有一种替代语法来完成上述操作，编写如下:

```
[, 1, , 2];
```

我们上面的叫做数组的*孔，这里逗号之间没有任何东西。有孔的阵列称为*稀疏阵列*。*

在这篇文章中，我们将看看 JavaScript 如何处理数组中的漏洞。

# 检查数组中的值

我们可以用`in`操作符检查数组中的漏洞。为此，我们可以编写类似下面的代码:

```
const arr = [, 1, , 2];
1 in arr;
```

我们应该从最后一行返回`true`,因为 1 在数组中。

ES6 将数组中的洞视为`undefined`条目。因此，如果我们想检查数组中的漏洞，请检查`undefined`。

迭代器和生成器也将漏洞视为`undefined`。例如，如果我们有

```
const arr = [, 1, , 2];
const iter = arr[Symbol.iterator]()for (let a of iter) {
  console.log(a);
}
```

我们得到了

```
undefined
1
undefined
2
```

如果我们调用`next`来获取下一项，如下所示，

```
iter.next();
```

我们得到了

```
{value: undefined, done: false}
```

对于第一个条目。

同样，如果我们有给定的生成器，

```
function* generator () {
  const arr = [, 1, , 2];
  for (let a of arr) {
    yield a;
  }
}for (let a of generator()) {
  console.log(a);
}
```

我们得到同样的东西。

# 数组. from()

`Array.from()`像对待迭代器和生成器一样对待孔。

例如，如果我们有

```
const arr = [, 1, , 2];
const arrFrom = Array.from(arr);
```

那么我们得到`arrFrom`的值是

```
[undefined, 1, undefined, 2]
```

同样，如果我们从类似数组的对象中创建一个数组，其中我们将非负整数作为键，将一个非负数作为值的 length 属性，那么缺失的条目也会被视为`undefined`。

例如，如果我们跑

```
const arrFrom = Array.from({
  1: 'foo',
  length: 2
});
```

我们得到`arrFrom`的值为

```
[undefined, "foo"]
```

![](img/f6c09f9ad463915e8ebcb48db3c8a120.png)

科迪·韦斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# Array.prototype 方法如何处理漏洞

这些方法的行为随着 JavaScript 版本的不同而不同。在 ES5 中，它们是:

*   `forEach`、`filter`、`every`和`some`忽略孔。
*   `map`跳过但保留漏洞。
*   `join`和`toString`将孔视为`undefined`元素，但将`null`和`undefined`视为空字符串。

下面是一个完整的方法列表以及它们是如何处理漏洞的。每种方法都有不同的作用。

*   `concat` —保留孔
*   `copyWithin`-孔被复制
*   `entries`、`keys`、`values` —将孔视为`undefined`
*   `every`-忽略孔
*   `fill` —填充孔
*   `filter`-移除孔
*   `find`-将孔视为元素
*   `findIndex`-将孔视为元素
*   `forEach`-忽略孔
*   `indexOf`-忽略孔
*   `join`-将孔转换为空字符串
*   `lastIndexOf`-忽略孔
*   `map` —保留孔洞
*   `pop` —将孔视为元素
*   `push`-保留孔
*   `reduce`，`reduceRight`-忽略孔
*   `reverse`-保留孔
*   `shift` —将孔视为`undefined`
*   `slice`-保留孔
*   `sort`-保留孔
*   `toString`-保留孔
*   `unshift`-保留孔
*   `values`-将孔转换为`undefined`

当数组稀疏时，它们会有漏洞。它们作为`undefined`被迭代，但是每个数组原型方法对待漏洞的方式不同，所以我们在定义有漏洞的数组和处理它们时必须小心。

我们可以使用`in`操作符来检查一个给定的条目是否在一个数组中。

`Array.from()`在将数组或带孔的类数组对象转化为数组时，也将孔视为`undefined`并将孔转化为`undefined`。