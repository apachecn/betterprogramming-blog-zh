# 避免在 JavaScript 中编写另一个 For 循环

> 原文：<https://betterprogramming.pub/never-write-another-for-loop-in-javascript-9db11afa6445>

## 提高代码可读性的七个高阶函数

![](img/0eb35677cd05dbfde2e1adb0dfc8db6f.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

作为初级工程师，我们大多数人都有一种从零开始构建东西并对代码进行更多控制的内在愿望。在 JavaScript 中处理数组和对象时，我们中的一些人可能更喜欢使用 for 循环来遍历元素或键，同时执行某种形式的逻辑。嗯，这样做并不一定是错误的——控制回路是我们在计算机科学课上学到的最初几样东西之一。但重要的是要注意 Javascript 中内置的高阶函数，我们都可以利用它们来使我们的代码更加优雅、可读和简洁。

# 什么是高阶函数？

以防我们可能已经忘记，这里是定义:

> 高阶函数是一个函数，它接受一个函数作为参数，或者返回一个函数。

在这篇文章中，我将讨论我最常用的七个高阶 Javascript 函数。在每一节中，我将尝试解释*何时*和*如何*使用这些功能。

# 1.forEach()

`forEach()`方法接受一个函数，并对数组中的每个元素执行该函数。

## 何时使用它

`forEach()`是一个通用工具，可用于许多情况，包括其他高阶函数所涵盖的情况。然而，我们应该记住，如果有更具体的高阶函数可以使用，我们应该总是使用它们而不是`forEach().`我将更深入地研究这一部分，以便我们可以更好地理解本文后面的其他函数。

## 句法

`arr.forEach(callback(currentValue [, index [, array]])[, thisArg]);`

在最基本的层面上，传递给`forEach`的函数需要有被迭代的`currentValue`，在下面的例子中被恰当地命名为`elem`。其他可选参数用方括号标出。

## 例子

```
const array = ['a', 'b', 'c'];
array.forEach(function(elem) {
    // using template literals here
    console.log(`Printing ${elem}`); 
});// Printing a
// Printing b
// Printing c
```

为了说明我们如何使用这个可选参数，我们来看一个类似的例子:

```
const array = ['a', 'b', 'c'];
array.forEach(function(elem, index, array) {
    console.log(`${elem} is at position ${index} of [${array}]`);
});// a is at position 0 of the array [a, b, c]
```

此外，我们甚至可以传入`this`的值，以便在我们传入`forEach`的函数范围内使用:

```
const array = ['a', 'b', 'c'];
array.forEach(function(elem, index, array) {
    console.log(array == this); // checking for pointer equality
}, array); // pass in `this` as optional param// true
```

# 2.减少()

## 何时使用

`reduce()`是一个非常强大的方法，可以通过执行 **reducer** 函数从数组中导出**单值**。

这听起来可能有点复杂，但其思想是使用正在迭代的当前值，并以某种方式将其与在每次迭代中逐步处理的累加器集成。如果这个概念对你来说听起来仍然模糊不清，下面的例子应该会给你更多的启发。

我们有一个对象按部分存储考试分数，我们想得到总分。因为我们想要一个单一的值，`reduce()`是我们在这里应用的一个很好的方法。

## 句法

`arr.reduce(callback(accumulator, currentValue[, index[, array]])[, initialValue])`

## 例子

```
const scoresBySection = {
    math: 60,
    reading: 70,
    writing: 87,
};const sectionNames = Object.keys(scoresBySection); // get array
const marks = sectionNames.reduce(function(accumulator, sectionName) {
    // the return value will be the accumulator for next iteration
    return accumulator + scoresBySection[sectionName];
}, 0) // initial valueconsole.log(marks) // 217
```

在我的上一篇文章中，我还写了如何使用`reduce()`安全地访问对象中的值。

# 3.地图()

## 何时使用它

如果我们需要对数组进行修改，我们仍然可以使用`forEach()`以如下方式完成:

## 句法

`var new_array = arr.map(function callback(currentValue[, index[, array]]) {
// Return element for new_array
}[, thisArg])`

## 例子

```
const array = [1, 2, 3];
array.forEach(function(elem, index, array) {
    array[index] = elem * 2;
});console.log(array); // [2,4,6]
```

还记得我们应该总是尽可能使用另一个高阶函数而不是`forEach()`吗？如果我们想对现有数组进行一对一的转换，我们应该使用`map().`对每个当前值执行传入的函数，并返回一个**新数组**。

```
let array = [1,2,3];
array = array.map(function(elem) {
    return elem * 2;
});console.log(newArray); // [2,4,6]
```

这里需要注意的一件重要事情是，我们使用了声明符`let`而不是`const`，因为我们从`map(),`获得了一个新数组，因此我们需要用一个新值给变量`array`重新赋值。

# 4.过滤器()

## 何时使用它

可以想象，我们并不总是希望所有的值都在一个数组中。有时我们只想要那些符合特定资格条件的。我们可以用`filter`筛选出那些我们想要得到的原始数组的子集。与`map()`类似，`filter()`返回一个新的数组。

## 句法

`var newArray = arr.filter(callback(element[, index[, array]])[, thisArg])`

## 例子

```
const array = [1,2,3,4,5];
const evenNumbers = array.filter(function(elem) {
   // expressions that return 'true' are retained
   return elem % 2 == 0;
});
```

# 5.查找()

## 何时使用它

在数组中搜索一个元素并不一定是大海捞针。`find()`返回满足条件的**第一个元素，**如果有的话。一旦找到该元素，就会立即返回。

## 句法

`arr.find(callback(element[, index[, array]])[, thisArg])`

## 例子

```
const array = [1,4,4,5];
const result = array.find(function(elem, index) {
   console.log(`Index: ${index}`);
   return elem === 4;
});
console.log(`Result: ${result}`);// Index: 0
// Index: 1
// Result: 4
```

# 6.一些()

## 何时使用它

有时，我们可能想要找出在数组中是否有至少一个满足条件的*元素。`some()`返回一个布尔值，告诉我们这样的元素是否存在。*

## 句法

`arr.some(callback(element[, index[, array]])[, thisArg])`

## 例子

```
const array = [1,4,4,5];
const hasAtLeastAFour = array.some(function(elem) {
   return elem === 4;
});
console.log(hasAtLeastAFour);// true
```

# 7.每隔()

## 何时使用它

最后，我们可能想要找出数组中的每个 元素是否都满足某个条件。同样，`every()`如果所有元素都满足条件，则返回 true，否则返回 false。

## 句法

`arr.every(callback(element[, index[, array]])[, thisArg])`

## 例子

```
const array = [1,4,4,5];
const allFours = array.every(function(elem) {
   return elem === 4;
});
console.log(allFours);// false
```

# 最终注释

在上面的例子中，我使用了`function`关键字，但是在 ES6 中，我们可以使用[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)来获得更简洁的语法。除了简洁之外，arrow 函数没有绑定到`this,`，这可能根据您的情况有所帮助(例如，使用外部作用域的`this`)。

```
array.forEach((elem, index) => {
    ...
});
```

最后，我已经谈到了我经常使用的七个高阶函数，但是您可以在 [MDN web 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)中找到更多。你通常不需要花时间从头开始写代码，因为已经有本地函数为我们做了。在从头开始编写代码之前，我们应该总是尝试利用现有的资源。

我希望这篇文章能有所帮助。如果你注意到一些不正确的地方，或者可以解释得更好的地方，请随意留言。感谢阅读！