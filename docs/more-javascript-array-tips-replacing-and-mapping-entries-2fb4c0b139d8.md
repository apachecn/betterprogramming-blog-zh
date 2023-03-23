# 更多 JavaScript 数组技巧

> 原文：<https://betterprogramming.pub/more-javascript-array-tips-replacing-and-mapping-entries-2fb4c0b139d8>

## 替换和映射条目

![](img/dba23cc2315995a37e223d97ad2e9a44.png)

照片由 [Jen Theodore](https://unsplash.com/@jentheodore?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 和其他编程语言一样，有许多方便的技巧，让我们可以更容易地编写程序。在本文中，我们将研究如何做涉及数组的不同事情，比如替换数组中的特定值，以及将数组条目从一个值映射到另一个值。

# 替换数组中的特定值

有几种方法可以替换数组中的特定值。我们可以使用`indexOf`方法获得数组的第一个匹配项，然后使用索引为数组索引中的条目分配一个新值。例如，我们可以像下面的代码一样使用`indexOf`方法:

```
const arr = ['apple', 'orange', 'grape', 'banana'];
const index = arr.indexOf('orange');
arr[index] = 'chicken';
console.log(arr);
```

`indexOf`在`arr`上被调用，它接受任何我们想要获取数组中第一个索引的对象。它最适合原始值，因为它不对对象进行深度检查，所以它只通过检查对象的引用来工作。在第三行，我们重新分配了由`indexOf`方法分配的`index`的值，应该是 1。然后，我们赋予它新的值`'chicken'`。然后，我们应该从最后一行的`console.log`语句中获得以下输出:

```
["apple", "chicken", "grape", "banana"]
```

请注意，我们可以使用`const`来声明`arr`，因为我们没有给`arr`的任何属性分配任何新值，所以它不会出错。

我们还可以使用`splice`方法来替换数组中的一个或多个值。这种方法让我们可以删除、替换现有元素或在适当的位置添加新元素。`splice`方法的参数是开始改变数组的起始索引`start`。它可以是积极的，也可以是消极的。如果它是负的，那么它将从数组的末尾开始改变数组，并向数组的开头移动。结束索引是-1，第二个是-2，依此类推。`splice`方法的第二个参数是`deleteCount`，这是一个可选参数，让我们指定从第一个元素的`start`参数开始删除多少项。后续参数是我们想要插入到数组中的项目。我们想多久就多久。只有第一个参数是必需的。

我们可以使用`splice`方法，首先通过获取我们想要替换的项目的索引来删除我们想要替换的条目，然后我们可以使用`splice`方法在它的位置插入一个新条目，如下面的代码所示:

```
const arr = ['apple', 'orange', 'grape', 'banana'];
const index = arr.indexOf('orange');
arr.splice(index, 1);
arr.splice(index, 0, 'chicken');
console.log(arr);
```

如果我们运行上面的代码，我们应该得到与之前相同的输出:

```
["apple", "chicken", "grape", "banana"]
```

前两行与第一个示例相同。然后，我们第一次调用了`splice`方法来删除索引中的原始条目。第一个参数是我们想要移除的数组的`index`，第二个参数指定我们只移除一个条目，也就是由`index`指定的条目。然后我们再次调用`splice`来插入新的条目。我们在第二个拼接调用中再次传入`index`,因为我们想要在与原始元素相同的位置插入新元素。第二个参数是零，因为我们不想删除任何条目。然后，我们在第三个参数中传递`'chicken'`,这样我们就可以将`'chicken'`放在与`'orange'`相同的位置。

# 将数组条目从一个值映射到另一个值

如果我们想将数组的每个条目映射到一个新值，我们可以用几种方法来实现。我们可以使用`map`方法或`Array.from`方法来完成这项工作。`map`方法是一个数组实例方法，它接受一个最多有三个参数的回调函数。第一个参数是由`map`方法处理的数组的值。这是一个必需参数。第二个参数是可选参数，它是数组中正在处理的数组条目的`index`。第三个参数是调用`map`方法的数组。回调返回我们希望新值具有的值。

例如，如果我们想把每个数组条目的一个字段放入一个新的数组，我们可以编写下面的代码:

```
const arr = [{
    food: 'apple',
    color: 'red'
  },
  {
    food: 'orange',
    color: 'orange'
  },
  {
    food: 'grape',
    color: 'purple'
  },
  {
    food: 'banana',
    color: 'yellow'
  }
];
const foodColors = arr.map(({
  color
}) => color);
console.log(foodColors);
```

在上面的代码中，我们使用了`map`方法来获取`color`字段的值，并将其放入一个新的数组中。在`map`方法中，我们用第一个参数传入一个回调函数，将`arr`数组中的对象析构到`color`变量中，在参数中检索`color`变量，然后返回它。这将使我们获得新的`foodColors`数组中每个条目的`color`字段的值。

或者，我们可以使用`Array.from`方法来做同样的事情。`Array.from`方法从类似数组或其他可迭代对象创建一个新的浅复制数组实例。它接受的第一个参数是一个数组或其他类似数组或可迭代的对象，如 NodeList、`arguments`、strings、TypedArrays 如`Uinit8Array`、`Map`、other `Sets`以及任何其他具有`Symbol.iterator`方法的对象。第二个参数是可选的回调参数函数，我们可以用它将每个条目从一个值映射到另一个值。回调函数有两个参数，即由`from`方法处理的条目。`from`方法将遍历整个 iterable 对象或数组，将每个条目映射到一个新值。第二个参数是正在处理的数组或 iterable 的索引。它返回一个包含新条目的新数组

例如，我们可以用下面的代码将`map`方法替换为`Array.from`方法:

```
const arr = [{
    food: 'apple',
    color: 'red'
  },
  {
    food: 'orange',
    color: 'orange'
  },
  {
    food: 'grape',
    color: 'purple'
  },
  {
    food: 'banana',
    color: 'yellow'
  }
];
const foodColors = Array.from(arr, ({
  color
}) => color);
console.log(foodColors);
```

在上面的代码中，我们使用传入`Array.from`方法的回调函数来获取`color`字段的值，并将其放入一个新数组中。在`map`方法中，我们传递了一个带有第一个参数的回调函数，将`arr`数组中的对象析构到`color`变量中。在参数中检索`color`变量，然后我们返回它。这将为我们获取新的`foodColors`数组中每个条目的`color`字段的值。

有几种方法可以替换数组中的特定值。我们可以使用`indexOf`方法获取数组的第一个匹配项，然后使用索引为数组索引中的条目分配一个新值。我们还可以使用`splice`方法移除给定索引的现有条目，然后在给定相同索引的相同位置添加另一个元素。如果我们想将数组的每个条目映射到一个新值，我们可以用几种方法来实现。我们可以使用`map`方法或`Array.from`方法来完成此操作。