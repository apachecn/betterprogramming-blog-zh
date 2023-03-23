# 了解数组的排序方法

> 原文：<https://betterprogramming.pub/understanding-the-sort-method-of-arrays-a9f2d5f83230>

## 如何使用 JavaScript 的排序

![](img/1eb396e9a2667fcfdcba226d80b412e6.png)

约瑟夫·霍查在 [Unsplash](https://unsplash.com/s/photos/shop?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

*   JavaScript array sort()用于对数组元素进行排序。
*   默认情况下，数组将按升序排序，但我们可以改变它。
*   这个方法将改变原来的数组。
*   我们还可以提供我们的比较功能来实现自定义排序。

```
var array = [1,2,3,1,2,3];array.sort(); [1,1,2,2,3,3]
```

让我们试试另一个例子:

```
var array = [1,2,11];array.sort(); // [1,11,2]
```

对于上面的代码，预期的结果是`[1,2,11]`，但是我们得到的是`[1,11,2]`。原因是`sort` 方法将元素转换成字符串，然后比较它们的 UTF-16 代码单元值序列。

于是`11`转换成了`“11”`。当我们比较两个字符串`“11”`和`“2”`时，比较的字符代码表示`char code of 1 is 49`，而`2`的字符代码是`50`。因为 49 < 50，我们得到`[1,11,2]`。

如果数组包含任何`undefined`，那么所有的`undefined`元素都被排序到数组的末尾。

我们可以使用默认的排序方法对字符串进行排序。

```
var names = ["John", "Abu", "Babu", "Balu", "Antony"]names.sort();console.log(names); // ["Abu", "Antony", "Babu", "Balu", "John"]
```

对于其他类型，我们需要将我们的`compareFunction`作为参数传递给 sort 函数。

compare 函数有两个参数。这两个参数取自索引`[0,1] , [1,2] … [arrayLength-2, arrayLength-1]`上的数组，用于比较这一对元素。

元素的排序基于每对比较的返回值`compareFunction`。

如果函数返回…

*   `>0`然后`a`和`b`的位置互换。
*   `<=0`然后位置没有变化

让我们写一个比较函数来排序数字。

```
function compare(a, b) { if (a < b ) {
    return -1;
  } if (a > b ) {
    return 1;
  } // otherwise a == b
  return 0;}// Now if we execute

var array = [2,1,11];
array.sort(compare);log(array); [1,2,11];
```

上述比较函数可以简化为:

```
function compareNumbers(a, b) { return a - b;}
```

因为，当我们做`a-b`，如果`a > b`那么它返回数字`> 0`。如果`a<b`，那么它返回`< 0`。当`a=b`时，则返回`0`。

要对`Descending Order`中的数字进行排序，只需将`a-b`改为`b-a`。

```
function Descending(a, b) { return b- a; }
```

# 排序对象数组

```
var users = [ {name: "John", age:20}, {name:"Antony" , age : 33}];function sortByAge(obj1, obj2) { return obj1.age - obj2.age;}function sortByName(obj1, obj2) {
   return obj1.name.localeCompare(obj2.name);
} **users.sort(sortByAge);**// output 
0: {name: "John", age: 20}
1: {name: "Antony", age: 33}**users.sort(sortByName);**//output0: {name: "Antony", age: 33}
1: {name: "John", age: 20}
```

在`sortByName` compareFunction 中使用的`**localeCompare()**`方法返回一个数字，指示一个引用字符串在排序顺序中是在给定字符串之前还是之后，或者是否与给定字符串相同。

如果 CompareFunction 返回`undefined`、`null`、`NaN`、`Infinity`，则数组不会对两个成员进行排序

```
var array = [3,2,1, 110, 102 , 30];function customCompare(a, b){
   if(a > 10 || b > 10) {
      return Infinity;
   }
   else {
      return a-b;
   }
}array.sort(customCompare);
```

在上面的例子中，我们已经定义了如果`a`和`b`小于`10` ，它将按升序排序。否则，该函数将返回`Infinity`，尽管是`Infinity > 0`。但是，它不会被交换。对于`undefined`、`null`、`NaN`来说也是如此。

[](https://medium.com/better-programming/you-dont-need-loops-in-javascript-1dc8139eab4b) [## JavaScript 中不需要循环

### 了解如何消除循环并使用高阶函数，如映射、减少和过滤

medium.com](https://medium.com/better-programming/you-dont-need-loops-in-javascript-1dc8139eab4b) [](https://medium.com/better-programming/debug-like-a-lion-in-chrome-dev-tool-babe1019df3d) [## 使用 Chrome 的 DevTools 像狮子一样调试

### 更有效地使用 Chrome 开发工具的技巧和诀窍

medium.com](https://medium.com/better-programming/debug-like-a-lion-in-chrome-dev-tool-babe1019df3d)