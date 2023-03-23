# 在 JavaScript 中使用数组的完整指南

> 原文：<https://betterprogramming.pub/the-complete-guide-to-using-arrays-in-javascript-c77f1abab50e>

## 我们每天都在使用数组，但是你知道兔子洞有多深吗？

![](img/3d6f56824ba0e7dd20e8d9b10fb39844.png)

安东尼奥·加西亚在 [Unsplash](https://unsplash.com/s/photos/array?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

数组是可以用各种方式操作的对象列表。每个条目都可以通过它们自己的索引来访问。数组可以以各种方式组合，也可以相互嵌套，让我们创建多维数组。我们可以用任何对象的集合组成数组。它们也可以被析构为变量，这样每个条目都可以被单独访问和操作。JavaScript 数组是零索引的，所以每个数组的起始索引总是零。这意味着索引 0 具有数组的第一个元素。

数组可以包含任何类型的对象。它们不必是相同类型的对象。同样，如果具有给定索引的数组条目还没有被赋值，它就有一个`undefined`值。

可以用数组存储的内容包括:

*   待办事项
*   食谱列表
*   通讯录联系人
*   购物单
*   杂货清单
*   你的约会
*   任何可以列入清单的东西。

没有数组，我们所能做的就是为每个条目单独声明变量，这是一件非常痛苦且不切实际的事情。

# 声明数组

为了声明数组，我们使用`let`或`const`关键字——就像这样:

```
let arr = [1,2,3];
const arr2 = [1,2,3];
```

我们用`let`表示变量，用`const`表示不变的数组。

此外，我们可以先声明数组，然后再插入值，如下所示:

```
let arr = []
arr[0] = 1;
arr[1] = 2;
arr[2] = 3;
```

这与`let arr = [1,2,3]`相同，因为我们在两个`arr`数组中有相同的条目和相同的顺序。

我们可以在同一个数组中声明具有不同类型值的数组，如下所示:

```
let arr = [1, 'chicken', {foo: 'bar'}];
```

如您所见，我们在数组中放入什么样的数据并不重要。然而，当我们通过检查对象的类型和内容，以及任何条目是`null`还是`undefined`来遍历或操作数组时，我们必须小心避免数据类型错误。

创建数组的另一种方法是使用`new`关键字，就像这样:

```
let names = new Array('Bob','Jane', 'John');
```

# 访问数组数据

我们通过索引来访问数组项。例如，要获得数组的第二个元素，我们写:

```
arr[1]
```

如果`arr`被分配给`[1, ‘chicken’, {foo: ‘bar’}];`，那么`arr[1]`就是`'chicken'`。

# 获取数组大小

数组是一个具有`length`属性的对象，我们可以得到数组的大小，所以上面的`names`数组的`length`为 3。我们编写`names.length`来访问数组的长度。

# 多维数组

在 JavaScript 中，多维数组只是嵌套在另一个数组中的一个数组。因此，要声明一个多维数组，我们可以使用与上面相同的声明方法，除了我们用数组替换里面的实体。例如，我们可以写:

```
let multiArray = [[1,2,3], [4,5,6]];
```

或者:

```
let multiArray = new Array([1,2,3], [4,5,6]);
```

我们也可以写:

```
let multiArray = [];
multiArray[0] = [];
multiArray[0][0] = 1;
multiArray[0][1] = 2;
multiArray[0][2] = 3;
multiArray[1] = [];
multiArray[1][0] = 4;
multiArray[1][1] = 5;
multiArray[1][2] = 6;
```

这三段代码是等价的。

我们通过添加另一个带有内部数组索引的方括号来访问多维数组条目。例如，如果我们想获得`multiArray`中第一个数组的第二项，那么我们写:

```
multiArray[0][1]
```

![](img/7968c1f82138dfedee6107a6bad6c4a9.png)

[陈俊生](https://unsplash.com/@danielchenjs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/array?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 遍历数组

我们可以用循环遍历数组的值。循环是一段段重复的代码，直到满足结束条件。在 JavaScript 中，我们有`for`循环、`while`循环和`do...while`循环。

## For 循环

对于`for`循环，假设我们有了`name`数组，我们可以通过运行以下命令来遍历循环:

```
let names = new Array('Bob','Jane', 'John');
for (let i = 0; i < names.length; i++){
  console.log(names[i]);
}
```

`for`循环通常以起始条件作为第一条语句，然后是第二条语句的结束条件，第三条语句的索引改变条件。这些语句用分号分隔。因为在 JavaScript 中数组从零开始，所以当数组的索引比数组的长度小 1 时，我们终止循环。

## While 循环

`while`只要条件保持为真，循环就会循环。

例如，如果索引号`i`小于 3，将运行以下循环:

```
const array = [1,2,3];let i = 0;
while(i < array.length){
  console.log(i);
}
```

如果括号中的条件永远不为真，那么循环内容将永远不会运行。

## Do While 循环

`do...while`循环将总是执行第一次迭代。

```
const array = [1,2,3];let i = 0;
do{
  console.log(i);
}
while(i < array.length)
```

在上面的例子中，它至少会记录 0，但是在这种情况下它也会记录 1 和 2，因为这两个数字小于 3。

有了上面的循环，你可以在循环完全结束之前调用`break`来停止循环或者`return`。

```
//do while loop
const array = [1,2,3];let i = 0;do{
  console.log(i);  if (i == 1}{    break;  }}
while(i < array.length)//while loop
i = 0;while(i < array.length){  if (i == 1{    break;  }
  console.log(i);
}//for loop
for (let j = 0; j < array.length; j++){  if (j == 1){
    break;
  }
  console.log(j);
}
```

在上面的例子中，您不会看到两个日志。

从循环内部返回的示例:

```
const loop = ()=>{
  const array = [1,2,3];  
  for (let j = 0; j < array.length; j++){       
    if (j == 1){
      return j;
    }
    console.log(j);
  }
}
loop() //returns 1
```

你也可以用`continue`语句跳过迭代:

```
const array = [1,2,3];
for (let j = 0; j < array.length; j++){  if (j == 1){
    continue;
  }
  console.log(j) // 1 will be skipped;
}
```

# 数组属性

因为数组是 JavaScript 对象。是它自身的属性。像 JavaScript 中的任何对象一样，它有`prototype`属性，允许开发人员添加数组对象的方法和属性。`constructor`属性是对数组对象原型创建的函数的引用。`length`返回数组的大小。

# 数组方法

为了简化数组操作，JavaScript 的标准库包含了许多数组方法，使得数组操作变得简单。有一些方法可以查找和过滤项目，例如在数组中添加和删除项目。还有将多个数组合并成一个的函数。

这些是一些常见的数组方法，您可以用它们来对数组进行常见的操作。

## Array.forEach

将遍历数组的每个条目。您不能中断它或从它返回一个值。它采用一个回调函数，您可以在其中执行代码。

示例:

```
const array = [1,2,3];
  array.forEach(a =>{  console.log(a);
})
```

在上面的例子中，数组中的所有数字都将被记录。

## 数组.查找

`Array.find`将返回数组中给定条件的元素。例如，如果您想从数组中获取某些数字，您可以这样做:

```
const array = [1,2,3];
const num = array.find(a => a == 2); // returns 2
```

`find`返回单个结果。

## Array.findIndex

`Array.findIndex`将返回给定条件下数组中元素的索引。它采用一个返回给定条件的回调函数。例如，如果要从数组中获取某个数字的索引，可以这样做:

```
const array = [1,2,3];
const num = array.findIndex(a => a == 2); // returns 1
```

## 数组.过滤器

`Array.filter`将返回满足给定条件的项目数组。它采用一个返回给定条件的回调函数。返回一个新的数组。

例如，如果您想从数组中获取某个数字的索引，您需要:

```
const array = [1,2,3];
const numArray = array.filter(a => a == 2); // returns [2]
```

## 数组.包含

`Array.includes`检查数组中是否存在项目。它需要一个函数可以比较的数字或字符串。

```
const array = [1,2,3];
const includesTwo = array.includes(2); // returns true
```

## 数组. some

`Array.some`检查某些项目是否满足给定的条件。它接受一个回调函数，该函数为条件返回一个布尔值。

```
const array = [1,2,3];
const includesTwo = array.some(a => a == 2); // returns true
const includesFive = array.some(a => a == 5); // returns false
```

## Array.every

`Array.every`检查每个项目是否满足给定的条件。它接受一个回调函数，该函数为条件返回一个布尔值。

```
const array = [1,2,3];
const everyElementIsTwo = array.every(a => a == 2); // returns false
const everyElementIsNumber = array.every(a => typeof a == 'number'); // returns true since every item in the array is a number
```

## Array.isArray

`Array.isArray`检查给定的对象是否是数组。这是检查一个元素是否是一个数组的便捷方法。

```
const array = [1,2,3];const notArray = {};
let objIsArray = Array.isArray(array); // true
objIsArray = Array.isArray(notArray); // false
```

## Array.from(新集合(数组))

`Set`是一个不能有重复条目的对象。您可以从一个数组创建一个新的`Set`，然后将它转换回一个数组。

```
const array = [1,2,2,3];
const arrayWithDups = Array.from(new Set(array)); //returns new array without duplicates, [1,2,3]
```

## Array.slice(startIndex，endIndex)

从`startIndex`到`endIndex — 1`返回一个新数组。

示例:

```
const arr = [1,2,3,4,5];
const newArr = arr.slice(0,2);
console.log(newArr); // returns [1,2]
```

## Array.splice(index，numberOfItems)

用给定的`index`在适当的位置移除数组项，然后在其后移除`numberOfItems`。

例如:

```
const arr = [1,2,3,4,5];
arr.splice(0,2);
console.log(arr); // returns [3, 4, 5] since we specified that we remove item located at index 0 and 2 items after that.
```

## Array.sort(排序函数)

`Array.sort`根据`sortFunction`中返回的条件对数组进行排序。

`sortFunction`应该是这样的格式:

```
const sortFunction = (a, b) {
  if (a < b) {
    return -1;
  }
  if (a > b) {
    return 1;
  }
  // a must be equal to b
  return 0;
}
```

默认情况下，如果没有指定`sortFunction`，那么数组项将被转换为一个字符串，并根据字符串的 Unicode 值进行排序。

## Array.fill(newElement，startIndex，endIndex)

`Array.fill`将用从`startIndex`到`endIndex`指定的元素添加或替换元素。如果没有定义`startIndex`，那么它将从 0 开始。如果没有定义`endIndex`，那么它将改变值直到数组的末尾。

例如:

```
let array = [1, 2, 3, 4, 5];console.log(array.fill(0, 2, 4));
// array is now [1, 2, 0, 0, 0]console.log(array.fill(5, 1));
// array is now [1, 5, 5, 5, 5]console.log(array.fill(6));
// array is now [6, 6, 6, 6, 6]
```

## 递归展平数组

`Array.flat`函数不能很好地递归展平数组。深度是有限的，它不能展平所有类型的嵌套数组结构。更好的方法是编写一个递归函数来实现它。

```
let arr1 = [1, 2, [3, 4], 5];
let arr2 = [1, 2, [3, 4], [5, [6,[7,]]]];const flatten = (items) => {
  const flat = [];  items.forEach(item => {
    if (Array.isArray(item)) {
      flat.push(...flatten(item));
    } else {
      flat.push(item);
    }
  });  return flat;
}console.log(flatten(arr1));
console.log(flatten(arr2));
```

## Array.join(分隔符)

`Array.join`将返回一个字符串，方法是在将条目转换为字符串后，将它们串联起来，每个条目之间用`separator`隔开。最适用于字符串和数字数组。

示例:

```
const arr = [1,2,3];
console.log(arr.join(',')) // get '1,2,3'const arr = ['1',2,3];
console.log(arr.join(',')) // get '1,2,3'
```

## Array.indexOf(elementToFind)

`Array.indexOf`将返回数组中`elementToFind`的第一个索引。最适用于字符串和数字数组。如果要查找非字符串或数字对象，请使用`Array.findIndex`。如果找不到元素，则返回-1。

示例:

```
const arr = [1,2,3];
console.log(arr.indexOf(1)); // returns 0const arr2 = [1,1,2,3];
console.log(arr2.indexOf(1)) // still 0
```

## Array.lastIndexOf()

`Array.lastIndexOf`将返回数组中`elementToFind`的最后一个索引。最适用于字符串和数字数组。如果找不到元素，则返回-1。该函数还将开始向后搜索的起始索引作为第二个参数

示例:

```
const arr = [1,2,3];
console.log(arr.indexOf(1)); // returns 0const arr2 = [1,1,2,3];
console.log(arr2.indexOf(1)) // returns 1const arr3 = [3,1,2,3]
console.log(arr3.lastIndexOf(3, 2)) // returns 0, start searching backwards from index 2
```

## Array.push(newElement)

`Array.push`向数组中添加新元素。

示例:

```
let arr = [1,2,3];
arr.push(4);
console.log(arr) // [1,2,3,4]
```

## Array.pop()

`Array.pop`删除数组的最后一个元素。

示例:

```
let arr = [1,2,3,4];
arr.pop();
console.log(arr) // [1,2,3]
```

## Array.map(映射函数)

`Array.map`通过调用`mapFunction`返回一个新数组，该数组转换现有数组的元素。`mapFunction`取一个参数，就是数组元素。

示例:

```
let arr = [1,2,3,4];
const newArr = arr.map(a=>a*2)
console.log(newArr) // [2,4,6,8]
```

## Array.reduce(reduceFunction)

`Array.reduce`通过调用所有元素上的`reduceFunction`来组合数组元素以返回值。`reduceFunction`接受两个参数，即数组中的当前元素和下一个元素。

示例:

```
const arr = [1,2,3,4];
const sum = arr.reduce((a,b)=>a+b);
console.log(sum); // returns 10
```

# 数组. reverse()

`Array.reverse`返回一个新数组，现有数组的元素以相反的顺序排列。

示例:

```
const arr = [1,2,3,4];
console.log(arr.reverse()) // [4,3,2,1]
```

既然我们已经学习了数组，我们可以在 JavaScript 程序中做更多的事情。我们面前有一个广阔的世界！