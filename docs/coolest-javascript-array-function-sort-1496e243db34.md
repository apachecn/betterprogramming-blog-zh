# 最酷的 JavaScript 数组函数--。排序()

> 原文：<https://betterprogramming.pub/coolest-javascript-array-function-sort-1496e243db34>

![](img/9886bed94c6c01a37694b9410f1a01bc.png)

索菲·埃尔维斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

作为 JavaScript 开发人员，我们总是试图找到让编写代码更简单的方法。嗯，`.sort()`函数就是一个很好的例子。

让我们来看看你如何使用它。

# 证明文件

```
arr.sort([compareFunction])
```

该函数返回一个新数组，其结果已排序。这里真正神奇的是`compareFunction`。

下面是比较函数的样子:

```
compareFunction = (a, b) => {
 // return evaluation of a and b
}
```

# 什么是 a 和 b？

*   a —是数组中的前一个元素。
*   b —是数组中的下一个元素。

示例:

```
const newArray = [ 2, 4, 1, 7 ]newArray.sort((a, b) => {
 // first time through a = 2 b = 4
 // continues to compare the next values with the prev value
})
```

# 评价怎么写

评估可能有三种结果之一。

这是三个结果以及它们如何影响函数的结果:

```
compareFunction(a,b)
//result < 0 then a comes before b
//result > 0 then b comes before a
//result === 0 then order of a and b are unchanged
```

举个例子，假设我们想对一组数字进行排序。

```
const nums = [ 2, 4, 1, 7 ]
const newNums = nums.sort((a,b) => a - b)
// output
// [ 1, 2, 4, 7 ]
```

默认情况下，`sort`函数按升序对数字进行排序，因此，在这种情况下，您不必向它传递一个`compareFunction`。

`nums.sort()`返回相同的输出。

这里有一个更复杂的例子:

```
const arrayObjects = [{ price: 200 },{ price: 100 },{ price: 150 }]
const newObjects = arrayObjects.sort((a,b) => a.price - b.price)
console.log(newObjects)
// output
// [{price: 100},{price: 150},{price: 200}]
```

您还可以对字符串列表进行排序。

默认情况下，sort 函数将按字母升序对字符串列表进行排序。

这里有一个例子:

```
const fruits = ["Banana", "Orange", "Apple", "Mango"];
console.log(fruits.sort())
// output 
// ["Apple", "Banana", "Mango", "Orange"]
```

# 结论

利用`sort`功能简直牛逼。可能是最有用的 JavaScript 函数之一。检查一下，让我知道你用排序函数做什么！

编码快乐！