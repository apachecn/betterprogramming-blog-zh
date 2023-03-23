# 通过实现 Lodash 方法学习 JavaScript:唯一数组条目

> 原文：<https://betterprogramming.pub/learning-javascript-by-implementing-lodash-methods-unique-array-entries-dfd17d336e76>

## Lodash 的“uniq”系列方法可以毫无困难地用普通 JS 实现

![](img/e699139696c6c0a6d403d0d780645114.png)

照片由[上的](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)[表面](https://unsplash.com/@surface?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)和组成

Lodash 是一个非常有用的工具库，让我们可以轻松地处理对象和数组。

然而，现在 [JavaScript 标准库](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)正在赶上诸如 Lodash 之类的库，我们可以用简单的方法实现许多功能。

在本文中，我们将通过在普通 JavaScript 中实现一些 Lodash 方法，包括`uniq` `uniqBy`和`uniqWith`，来了解和学习更多关于 JavaScript 的知识。

# 唯一的

Lodash `uniq`方法创建一个没有重复值的数组。每个值只有第一次出现在返回的数组中。

因为 JavaScript 已经有了`set`数据结构，我们可以使用 spread 操作符从原始数组中删除所有重复的数据并返回它。

我们可以这样实现:

```
const uniq = arr => [...new Set(arr)]
```

在上面的代码中，我们只是将`arr`转换成一个集合来删除重复的值，然后用 spread 运算符将其转换回一个数组。

那么当我们这样称呼它的时候…

```
const result = uniq([1, 2, 2, 3]);
```

…我们得到`result`是`[1, 2 ,3]`。

# `'uniqBy'`

`uniqBy`与`uniq`相似，只是它采用了一个`iteratee`函数，该函数在比较值的唯一性之前运行。这意味着我们不能把它转换成一个集合。相反，我们必须运行`iteratee`，然后将它们与我们返回的数组中的现有条目进行比较。

为此，我们以下列方式实现了`uniqBy`:

```
const uniqBy = (arr, iteratee) => {
  let uniques = [];
  for (let a of arr) {
    const mappedUniques = uniques.map(iteratee);
    if (!mappedUniques.includes(iteratee(a))) {
      uniques.push(a);
    }
  }
  return uniques;
}
```

在上面的代码中，我们有一个`uniques`数组，它包含我们将返回的唯一条目的数组。

然后我们用`for...of`循环遍历`arr`。在循环内部，我们用`iteratee`映射`uniques`数组以获得映射的值。

接下来，我们检查每个数组条目是否已经在`uniques`中，方法是检查它是否在`mappedUniques`数组中，而不是在`uniques`中，因为我们想比较它被`iteratee`函数转换后的唯一性。

如果用`includes`方法检查时没有包含，那么我们将该值推入`uniques`。

一旦循环结束，我们返回`uniques`。那么当我们这样称呼它的时候:

```
const result = uniqBy([1, 2, 2.1, 3], Math.floor);
```

那么`result`就是`[1, 2, 3]`，因为`2`和`2.1`在对它们调用`Math.floor`之后是相同的。`iteratee`可以是任何接受一个参数并返回从其派生的内容的函数。

# `'uniqWith'`

`uniqWith`类似于`uniq`,除了它接受一个比较器，该比较器运行来比较具有唯一值的现有值和不具有唯一值的现有值。

如果原始数组中的条目不在具有唯一值的数组中，那么我们将它放入具有唯一值的数组中。

在检查给定数组中的所有值后，最终返回具有唯一值的数组。

我们可以如下实现它:

```
const uniqWith = (arr, comparator) => {
  let uniques = [];
  for (let a of arr) {
    if (uniques.findIndex(u => comparator(a, u)) === -1) {
      uniques.push(a);
    }
  }
  return uniques;
}
```

在上面的代码中，我们有:

```
uniques.findIndex(u => comparator(a, u)) === -1
```

这将检查`uniques`中的条目是否与我们正在循环的数组条目相同。如果不是(如返回值`-1`所示)，那么我们将它放入`uniques`。

当循环完成时，我们返回`uniques`。

然后我们如下运行它:

```
const result = uniqWith([1, 2, 2.1, 3], (a, b) => Math.floor(a) === Math.floor(b));
```

由此，我们得到`[1, 2, 3]`作为`result`的值，因为我们的比较器确定如果两个值的下限相同，那么它们就是相同的。`comparator`函数需要两个参数和两个值进行比较。

# 结论

Lodash 方法可以很容易地用普通的 JavaScript 集合和 spread 操作符实现

`uniqBy`实现起来有点困难，因为我们需要用传入的`iteratee`函数映射这些值，然后才能比较它们的唯一性。

`uniqWith`与`uniqBy`相似，我们必须运行`comparator`函数来比较这些值，以确定这些项目是否是唯一的。