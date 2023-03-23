# 使用 JavaScript 集

> 原文：<https://betterprogramming.pub/using-javascript-sets-12b526edf729>

## 一种有用的数据结构，用于模拟数学集合的属性

![](img/5beda802d6284ee0c9a75ea9eed776f1.png)

萨姆·麦克格在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 JavaScript 中，`Set`对象是保存值集合的对象。它们可以按照插入的顺序进行迭代。`Set`中的每个值只能出现一次。这意味着 JavaScript 中的`Set`与数学中的集合是一致的。

因为`Set`中的每个值必须是唯一的，所以将检查它们的值是否相等。这是通过使用三等运算符进行比较来实现的；意思是-0 和+0 是不同的值。

然而，`NaN`被认为是等同于自身的，尽管在 JavaScript 的其他部分并不是这样。`undefined`也可以存储在`Set` s 中。

每个`Set`都可以用一个构造函数来创建，并有`size`属性来获取每个`Set`中的项目数。`Set`可以通过多种方法操作。

其中包括:

*   `add`:向`Set`添加新元素，并返回插入新元素的`Set`。
*   `clear`:从`Set`中删除所有元素。
*   `delete(value)`:从`Set`中删除给定的`value`，并返回`has`函数本应返回的`value`。
*   `entries()`:返回一个新的迭代器，包含`Set`对象中每一项的`value`，按照插入顺序排序。每个条目都有一个带有`key`和`value`的`array`。`key`将被设置为`Set`条目的`value`。
*   `forEach(callbackFn, thisArg)`:允许遍历`Set`的条目，并处理传入的回调函数中的数据。通过为参数`thisArg`传入一个变量，回调中`this`的值。
*   `keys()`:获取一个新的迭代器对象中`Set`的`keys`，这个迭代器对象拥有每个元素的`value`。
*   `values()`:获取每个`Set`的插入顺序的`values`。
*   `Set.prototype[Symbol.iterator]`:返回一个新的迭代器对象，该对象按照插入顺序拥有`Set`中每个元素的`value`。

# 定义和操作集合

为了定义一个`Set`对象，我们编写:

```
let set = new Set();
set.add(1); 
set.add(5);
set.add(5);
```

在对`add`函数的三次调用之后，当我们记录`Set`变量的值时，我们应该得到`[1,5]`，因为在一个`Set`对象中我们不能有重复的。

这只适用于基本类型。因此，如果我们插入两个具有相同内容的对象，两者都将在`Set.`中，只有在将项目插入到`Set`中时才检查引用，与三重等于检查一致。

例如，如果我们写:

```
let set = new Set();for (let i = 0; i < 5; i++){
  set.add({a: 1, b: 2});
}
```

然后，我们将在`Set`中得到`{a: 1, b: 2}`五次，因为每个对象在内存中没有相同的引用。

我们可以用`has`函数检查一个元素是否存在于`Set`中。例如，如果我们有:

```
let set = new Set();
set.add(1); 
set.add(2);
set.add(3);
set.add(4);
set.add(5);
set.add(6);
set.add(7);
set.add(8);
```

那么如果我们调用`has`函数:

```
set.has(1)
```

它会返回`true`。如果我们打电话:

```
set.has(10)
```

然后它将返回`false`，因为这个元素在`Set`中不存在。我们也可以将表达式放入一个`Set`函数中。如果我们像下面的代码那样调用`has`:

```
set.has(Math.sqrt(25));
```

我们仍然得到`true`，因为表达式是在传递给`has`函数的参数之前计算的，所以它与`set.has(5)`相同。

要获得`Set`的`size`，我们可以使用`size`属性，如下所示:

```
set.size
```

也就是`8`，因为`Set`中有八个元素。

要从`Set`中删除一个项目，我们可以使用`delete`函数，如下所示:

```
set.delete(8)
```

然后`8`会从集合中移除。如果我们调用`set.has(8)`，它将是`false`，因为`8`不再在`Set`中。

为了遍历一个`set`的值，我们可以使用`for...of`循环。在下面的示例中，我们将遍历条目:

```
let set = new Set();
set.add(1); 
set.add(2);
set.add(3);
set.add(4);
set.add(5);
set.add(6);
set.add(7);
set.add(8);for (let item of set){
  console.log(item)
}
```

在上面的循环中，我们按照插入`add`的顺序记录`1`到`8`。

为了遍历`Set`的`keys`，我们可以使用`keys()`方法从`Set`对象中获取`key` s。例如，我们可以编写以下代码:

```
let set = new Set();
set.add(1); 
set.add(2);
set.add(3);
set.add(4);
set.add(5);
set.add(6);
set.add(7);
set.add(8);for (let key of set.keys()){
  console.log(key)
}
```

在`Set`对象的情况下，`key`将与值相同，因此当我们运行上面的代码时，我们仍然会在控制台中记录`1`到`8`。要循环遍历这些值，我们可以在`Set`对象上调用`values`方法，如以下代码所示:

```
let set = new Set();
set.add(1); 
set.add(2);
set.add(3);
set.add(4);
set.add(5);
set.add(6);
set.add(7);
set.add(8);for (let value of set.values()){
  console.log(value)
}
```

正如我们所见，我们从`keys`方法中获得了相同的值。这是因为对于`Set` s，`keys`和`values`总是相同的。为了同时获得`keys`和`values`，我们可以使用`entries`方法和析构赋值操作符将`key`和`value`分解到每个条目中它们自己的变量中，如下面的代码所示:

```
let set = new Set();
set.add(1); 
set.add(2);
set.add(3);
set.add(4);
set.add(5);
set.add(6);
set.add(7);
set.add(8);for (let [key, value] of set.entries()){
  console.log(key, value)
}
```

我们可以看到一个`Set`的每个条目的`key`和`value`是相同的，正如我们所料。

# 将集合转换为数组

要将一个`Set`转换成一个`array`，我们可以使用`Array.from`方法。我们将`Set`作为`from`方法的一个参数传入。例如，我们可以写:

```
let set = new Set();
set.add(1); 
set.add(2);
set.add(3);
set.add(4);
set.add(5);
set.add(6);
set.add(7);
set.add(8);const arr = Array.from(set);
console.log(arr)
```

通过运行上面的代码，我们看到`arr`是一个`array`，其元素的顺序与`Set`相同。这对于从`array`中删除重复元素很方便。为了从一个`array`中删除重复项，我们可以通过使用`Set`的构造函数将其转换为一个`Set`，然后用`Array.from`将其转换回一个`array`。我们可以这样写:

```
let arr = [1,1,1,1,1,2,2,2,3,4,5,5,6,7,8];
const set = new Set(arr);
arr = Array.from(set);
console.log(arr);
```

当我们在上面代码的最后一行使用`log` `arr`时，我们得到`[1, 2, 3, 4, 5, 6, 7, 8]`。这是因为我们将`arr`传递给了`Set`的构造函数，它创建了`Set`，在这里重复的值被自动删除。然后我们用`set`作为参数调用`Array.from`函数，所以我们把它转换回一个`array`。`Array.from`函数返回一个`array`，所以我们可以将它赋回`arr`并返回一个删除了重复项的`array`。

将`Set`转换成`array`的另一种方法是使用扩展运算符。例如，我们可以使用 spread 运算符重写上面的示例:

```
let arr = [1,1,1,1,1,2,2,2,3,4,5,5,6,7,8];
const set = new Set(arr);
arr = [...set];
console.log(arr);
```

我们得到完全相同的结果。这个函数稍微短一点，做的事情和`Array.from`函数一样，因为扩展操作符会复制它正在扩展的对象。

![](img/dd8a7069952c0313c863e158c7e567b3.png)

照片由[克里斯·贾维斯](https://unsplash.com/@crissyjarvis?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 对 JavaScript 集合进行数学集合运算

因为我们可以在`arrays`和`Set` s 之间转换，所以我们可以对它们应用`array`操作。`Set` s 没有任何方法来做类似于与其他`Set` s 求交集、差集或并集的事情。然而，由于我们可以在`array` s 和`Set` s 之间转换，我们可以使用`array`函数来做同样的事情。例如，如果我们想得到两个`Set`之间的交集，我们可以写:

```
let set1 = new Set();
set1.add(1); 
set1.add(2);
set1.add(3);
set1.add(4);let set2 = new Set();
set2.add(1);
set2.add(2);
set2.add(7);
set2.add(8);const intersectionBetweenSet1and2 = new Set([...set1].filter(item => set2.has(item)))console.log(intersectionBetweenSet1and2);
```

当我们记录`intersectionBetweenSet1and2`时，我们在`Set`中得到`1`和`2`，这是我们所期望的，因为`1`和`2`是两个`Set`中仅有的元素。在`Set`构造函数中，我们用 spread 操作符将`set1`转换为`array`，然后在其上调用`filter`，以仅包含`set1`中的项目，这些项目在`set2`上调用`has`方法后位于`set2`中。

要得到两个`Set`的集合并集，我们只需在两个集合上调用 spread 运算符，如下面的代码所示:

```
let set1 = new Set();
set1.add(1); 
set1.add(2);
set1.add(3);
set1.add(4);let set2 = new Set();
set2.add(1);
set2.add(2);
set2.add(7);
set2.add(8);const unionBetweenSet1and2 = new Set([...set1,...set2])console.log(unionBetweenSet1and2);
```

运行上面的代码时，我们应该看到`unionBetweenSet1and2`的值是`[1,2,3,4,7,8]`。因为我们将两个`Set`都传递到了`array`中，并用 spread 操作符将它们展开，所以我们用`array`中的所有元素创建了一个`array`。然后，集合构造函数消除了重复的元素，剩下两个`set`中的元素，这是两个`set`的并集。

如果我们想得到两个`Set`之间的集合差，我们可以将一个`Set`转换成一个`array`，然后对其使用`filter`函数来排除另一个`Set`中的元素。例如，我们可以写:

```
let set1 = new Set();
set1.add(1); 
set1.add(2);
set1.add(3);
set1.add(4);let set2 = new Set();
set2.add(1);
set2.add(2);
set2.add(7);
set2.add(8);const differenceBetweenSet1and2 = new Set([...set1].filter(item => !set2.has(item)))console.log(differenceBetweenSet1and2);
```

当我们记录`differenceBetweenSet1and2`的值时，我们应该得到`[3,4]`,因为我们用 spread 操作符将`set1`转换成了`array`,并在上面调用了`filter`。在`filter`的回调函数中，我们返回了`!set2.has(item)`来排除`set2`中也在`set1`中的元素，所以我们只剩下了`set1`中的元素。

为了获得两个`Set`之间的对称差，即仅在一个`Set`或另一个`Set`中的元素集合，我们可以获得两个`Set`之间的交集，然后排除交集中的元素。例如，我们可以这样写:

```
let set1 = new Set();
set1.add(1); 
set1.add(2);
set1.add(3);
set1.add(4);let set2 = new Set();
set2.add(1);
set2.add(2);
set2.add(7);
set2.add(8);const intersectionBetweenSet1and2 = new Set([...set1].filter(item => set2.has(item)))const symDiffBetweenSet1and2 = new Set([...set1,...set2].filter(item => !intersectionBetweenSet1and2.has(item)))console.log(symDiffBetweenSet1and2);
```

在上面的代码中，我们获得了`set1`和`set2`之间的交集。一旦我们得到了`intersectionBetweenSet1and2`，我们通过使用 spread 操作符将`set1`和`set2`中的所有内容放在同一个`array`中，然后在那个数组上调用`filter`并在回调函数中返回`!intersectionBetweenSet1and2.has(item)`以排除交集中的元素。这就是我们想要从`array`中消除的，以获得对称差。然后，我们用`Set`构造函数将结果转换回`Set`。最后，当我们记录`symDiffBetweenSet1and2`时，我们应该得到`[3,4,7,8]`，它们是只存在于某个`Set`中的元素。

为了检查一个元素是否是另一个`set`的`Superset`，我们可以再次将它转换成一个`array`并使用它来检查。例如，如果我们有:

```
let superSet = new Set();
superSet.add(1); 
superSet.add(2);
superSet.add(3);
superSet.add(4);let set = new Set();
set.add(1);
set.add(2);const isSuperset = [...set].every(item => superSet.has(item));console.log(isSuperset);
```

在上面的代码中，我们将`set`转换为`array`，然后用`has`函数检查`set`的每个元素是否都包含在`Superset`中。`every`方法是一个数组方法，它检查`array`中的每个元素是否满足指定的条件。

我们也可以从字符串创建`Set`对象。我们可以将一个字符串直接传递给`Set`构造函数。结果将是一个`Set`,其中删除了重复条目的字符串的单个字符。例如，如果我们有以下代码:

```
const letterSet = new Set('element');
```

然后当我们运行`console.log(letterSet)`时，我们返回`{“e”, “l”, “m”, “n”, “t”}`。请注意，如果有重复，稍后插入的与任何现有字符相同的字符将被忽略，因此较早插入的字符将保留。

JavaScript `Sets`是一种有用的数据结构，它对数学集合的属性进行建模。`Set`可以转换成`array`的，所以我们可以把它们当作`array`来操作，然后再把它们变回`Set`的。这对于处理它们来说非常方便，因为`Set`没有`array`那么多的方法。