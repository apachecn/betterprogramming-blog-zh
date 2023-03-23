# 您可能不知道的 JavaScript 本地方法

> 原文：<https://betterprogramming.pub/javascript-native-methods-you-may-not-know-ccc4b8aa5cfd>

## JavaScript 中强大但经常被忽视的本机方法

![](img/ad59d473390eac5d51233ab09230f353.png)

自从 ES6 发布以来，许多新的、舒适的、方便的本地方法已经被添加到 JavaScript 的新标准中。

然而，我从 GitHub 仓库中看到了许多旧代码。这并不一定意味着它们不好——但是我将向您介绍的这些特性将有助于您的代码更易读、更漂亮。

# 数字.伊斯南对伊斯南

`NaN`是一个数字类型。

```
typeof NaN === 'number'
```

所以你分不清`NaN`和数字。

甚至`Object.prototype.toString.call`对`NaN`和数字都返回`[object Number]`。你可能知道有一个`isNaN`方法来检查参数是否为`NaN`。但是从 ES6 开始，`number`构造函数开始包含`isNaN`作为它的方法。那有什么不同？

*   `isNaN` —检查传递的值是否不是数字或不能转换成数字。
*   `Number.isNaN` —检查传递的值是否不是数字。

下面是例子。而且这个话题已经被[栈溢出](https://stackoverflow.com/questions/33164725/confusion-between-isnan-and-number-isnan-in-javascript)的人处理过了。

```
Number.isNaN({});
// <- false, {} is not NaN
Number.isNaN('ponyfoo')
// <- false, 'ponyfoo' is not NaN
Number.isNaN(NaN)
// <- true, NaN is NaN
Number.isNaN('pony'/'foo')
// <- true, 'pony'/'foo' is NaN, NaN is NaN

isNaN({});
// <- true, {} is not a number
isNaN('ponyfoo')
// <- true, 'ponyfoo' is not a number
isNaN(NaN)
// <- true, NaN is not a number
isNaN('pony'/'foo')
// <- true, 'pony'/'foo' is NaN, NaN is not a number
```

# number . is 有限与 is 有限

在 JavaScript 中，像 1/0 这样的计算不会产生错误。而是给你`Infinit`，这是一个全局属性。

那么，如何检查一个值是否是无穷大的值呢？你不能。但是你可以用`isFinite`和`Number.isFinite`来检查一个值是否是有限值。

基本上它们的工作原理是一样的，但是它们彼此之间略有不同。

*   `isFinite` —检查传递的值是否有限。如果类型不是`Number`，则传递的值被转换为`Number`。
*   `Number.isFinite` —检查传递的值是否有限。传递的值不会转换成`Number`。

```
Number.isFinite(Infinity) // false
isFinite(Infinity) // falseNumber.isFinite(NaN) // false
isFinite(NaN) // falseNumber.isFinite(2e64) // true
isFinite(2e64) // trueNumber.isFinite(undefined) // false
isFinite(undefined) // falseNumber.isFinite(null) // false
isFinite(null) // trueNumber.isFinite('0') // false
isFinite('0') // true
```

# Math.floor vs. Math.trunc

在过去，当你需要取出一个数值的点右边的数字时，你可能会使用`Math.floor`。但是从现在开始，如果你真正想要的只是整数部分，尽量使用`Math.trunc`。

*   `Math.floor` —返回小于或等于给定数字的最大整数。
*   `Math.trunc` —截断点及其右侧的数字。

基本上，如果给定的数是正数，它们会给出完全相同的结果。但是如果给定的数字是负数，结果就不同了。

```
Math.floor(1.23) // 1
Math.trunc(1.23) // 1Math.floor(-5.3) // -6
Math.trunc(-5.3) // -5Math.floor(-0.1) // -1
Math.trunc(-0.1) // -0
```

# array . prototype . index of vs . array . prototype . includes

当你在一个给定的数组中寻找某个值时，你如何找到它？我见过很多开发者使用`Array.prototype.indexOf`，就像下面这个例子。

```
const arr = [1, 2, 3, 4];if (arr.indexOf(1) > -1) {
  ...
}
```

*   `Array.prototype.indexOf` —返回给定元素在数组中的第一个索引，如果不存在则返回`-1`
*   `Array.prototype.includes` —检查给定数组是否包含您要查找的特定值，并返回结果`true` / `false`

```
const students = ['Hong', 'James', 'Mark', 'James'];students.indexOf('Mark') // 1
students.includes('James') // truestudents.indexOf('Sam') // -1
students.includes('Sam') // false
```

小心点。由于 Unicode 的差异，传递的值区分大小写。

# String.prototype.repeat 与手动 for 循环

在添加这个特性之前，您制作字符串的方式，比如`abcabcabc`，是复制字符串，然后将它们连接成一个空字符串，连接次数不限。

```
var str = 'abc';
var res = '';var copyTimes = 3;for (var i = 0; i < copyTimes; i += 1) {
  for (var j = 0; j < str.length; j += 1) {
    res += str[j];
  }
}
```

但是这本书太长了，很乱，有时很难读懂。为此，可以使用`String.prototype.repeat`。你所需要做的就是传递一个数字，这个数字表示你想要复制字符串的次数。

```
'abc'.repeat(3) // "abcabcabc"
'hi '.repeat(2) // "hi hi "'empty'.repeat(0) // ""
'empty'.repeat(null) // ""
'empty'.repeat(undefined) // ""
'empty'.repeat(NaN) // ""'error'.repeat(-1) // RangeError
'error'.repeat(Infinity) // RangeError
```

传递的值不能是负数，必须小于无穷大，并且不能超过溢出的最大字符串大小。

# String.prototype.match 与 String.prototype.includes

要检查字符串中是否包含某些单词，有两种方法可以完成— `match`和`includes`。

*   `String.prototype.match` —采用 RegExp 类型的参数。RegExp 中支持的所有标志都可以使用。
*   `String.prototype.includes` —取两个参数，`searchString`为第一个参数，`position`为第二个参数。如果`position`没有通过，将使用默认值`0`。

区别在于`includes`区分大小写，而`match`不区分大小写。您可以将`i`标志放在 RegExp 中，使其不区分大小写。

```
const name = 'jane';
const nameReg = /jane/i;const str = 'Jane is a student';str.includes(name) // false
str.match(nameReg) 
// ["Jane", index: 0, input: "Jane is a student", groups: undefined]
```

# String.prototype.concat 与 String.prototype.padStart

`padStart`当你想在一些字符串的开头追加一些字符串的时候是一个强大的方法。

另外，`concat`也可以很好地完成这个任务。但是主要的区别是`padStart`重复将从结果字符串的第一个索引填充到当前字符串的第一个索引的字符串。

我会告诉你如何使用这个功能。

```
const rep = 'abc';
const str = 'xyz';
```

这里有两根弦。我想做的是把`rep`加在`xyz` 前面——但不只是一次。我想让它重复几次。

```
str.padStart(10, rep);
```

`padStart`有两个参数——新创建的结果字符串和将要重复的字符串的总长度。理解这个函数最简单的方法就是用空格写下字母。

```
// create empty 10 blanks
1) _ _ _ _ _ _ _ _ _ _ // fill out 'xyz' in str
2) _ _ _ _ _ _ _ x y z// repeat 'abc' in rep, 
// up until the first letter of 'xyz' appears
3) a b c a b c a x y z// the result will be
4) abcabcxyz
```

这个函数对这个特性非常有用，用`concat`肯定很难做到这一点，它也执行一个字符串追加。

`padEnd`从位置的末端开始。

# 结论

JavaScript 中有许多有趣且有用的方法，但并不常见。但这并不意味着它们毫无用处。如何在各种情况下使用它们完全取决于你。

## 资源

*   [JavaScript 中的 isNaN 和 Number.isNaN 混淆](https://stackoverflow.com/questions/33164725/confusion-between-isnan-and-number-isnan-in-javascript)
*   [Number.isFinite — MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/isFinite)
*   [isFinite — MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/isFinite)
*   [Math.trunc — MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/trunc)
*   [Math.floor — MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/floor)
*   [array . prototype . index of—MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf)
*   [array . prototype . includes—MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)
*   [string . prototype . repeat—MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/repeat)
*   [string . prototype . math—MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match)
*   [string . prototype . includes—MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/includes)
*   [string . prototype . pad start—MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/padStart)