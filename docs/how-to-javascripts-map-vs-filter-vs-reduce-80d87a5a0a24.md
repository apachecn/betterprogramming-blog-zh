# 如何使用 JavaScript 的？地图，。过滤器，还有。减少

> 原文：<https://betterprogramming.pub/how-to-javascripts-map-vs-filter-vs-reduce-80d87a5a0a24>

## 如何使用这三个有用的 JavaScript 函数

![](img/431bb4977ec0022c165e4d5126f10fbd.png)

Nik Shuliahin 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果你正在读这篇文章，你可能已经听说过 JavaScript 函数`.map()`、`filter()`和。`reduce()`。不要担心——在了解它们是什么方面，你并没有落后。如果你正在阅读这篇文章，那么你已经走在了游戏的前面，所以让我们尽快让你跟上进度。

JavaScript 创建了一些基于列表对象或数组的迭代器。这些迭代器有助于提供有效的方法来做通常用 for 循环做的事情。

我所有的例子都是用 es6 语法写的，所以如果你不了解 es6，你应该看看这篇文章。

我将解释为什么你会使用这些功能，以及如何使用它们。我们去看看吧！

# 。地图()

您是否希望创建一个新数组，其中的元素数量与另一个数组相同？如果是，你要用`.map()`。这也是一种对列表进行简单迭代的有用方法。

让我们看看如何使用它:

```
const array1 = [ 1, 2, 3, 4, 5 ];// map accepts a function. Using es6 syntax...
const array2 = array1.map(num => num + 2)
console.log(array2)
// output
// [3, 4, 5, 6, 7]
```

如你所见，我可以用`.map()`改变一个数组。这将创建一个新数组，它不是旧数组的引用或浅表副本。

以下是`.map()`接受的参数:

```
const new_array = arr.map(function callback(currentValue[, index[, array]]) {
    // Return element for new_array
}[, thisArg])
```

`.map()`接受两个参数:一个回调函数和一个对`this`属性的引用。你很可能不需要担心第二个论点。

回调函数可以传递一到三个参数。这些参数是`currentValue`的名称。在这个例子中，那就是`num`。第二个参数是数组中元素位置的索引。第三个参数是用来调用`.map()`的数组。在大多数情况下，您只需要使用第一个参数。

# 。过滤器()

过滤就是它听起来的样子。你想把一个数组过滤成一个新的数组，里面只有特定的元素。`.filter()`也会返回一个新的数组。

让我们看看如何使用这个函数:

```
const flowers = [ 'tulip', 'rose', 'lilac', 'Indian Paintbrush', 'petunia' ];const longFlowers = flowers.filter(word => word.length > 6);console.log(longFlowers);
// output
// ["Indian Paintbrush", "petunia"]
```

下图显示了该函数可以接受的参数:

```
const newArray = arr.filter(callback(element[, index[, array]])[, thisArg])
```

您可能知道，这个函数接受与`.map()`完全相同的参数。

# 。减少()

`.reduce()`非常方便，因为你可以用它对一个数组求和并返回一个结果。这可以用于任何你想积累的东西。

这里有一个例子:

```
const totalNums = [ 1, 2, 3, 4 ]
const sum = totalNums.reduce((totalNumSoFar, currentNum) => {               return totalNumSoFar + currentNum;
});console.log(sum)
// output 10
```

这个可能有点混乱，但基本上，这里的回调函数从上一次迭代中获取总和，并将其作为参数传递。如果您希望累加器的初始值是某个值，您可以传入代表初始值的第二个参数。

让我们看看它初始值:

```
const totalNums = [ 1, 2, 3, 4 ]
const sum = totalNums.reduce((totalNumSoFar, currentNum) => {               return totalNumSoFar + currentNum;
}, 5);console.log(sum)
// output 15
```

以下是可以传递的所有参数:

```
arr.reduce(callback(accumulator, currentValue[, index[, array]])[, initialValue])
```

如你所见，参数都差不多。你也可以得到索引和初始数组。

# 结论

我希望这篇文章能帮助您了解这些非常有用的 JavaScript 函数。编码快乐！