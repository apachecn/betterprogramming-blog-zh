# JavaScript 循环之间的区别:For、ForEach()、For…of 和 For…in

> 原文：<https://betterprogramming.pub/difference-between-javascript-loops-for-foreach-for-of-and-for-in-91cd8fe884fd>

## JavaScript 开发人员的备忘单

![](img/f3e65726037528c041c891bab305c4db.png)

j .凯利·布里托在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我将解释 JavaScript 中四个`for`循环的区别。

# **TL；博士**

这些循环的最佳用法如下:

*   对于数组，使用`for...of`(或`forEach`)。
*   对于对象，使用`for...in`。
*   如果你使用数组并且需要访问索引，使用`for`或者`forEach`。

# `for...in`循环

这个循环是在 ES6 中引入的，作为一个工具与可枚举数一起使用，以迭代对象的属性:

现在，你可以用这个工具迭代一个数组，但这并不意味着你应该这样做。它之所以有效，是因为在 JS 中，数组继承自`Object`。因此，如果您在数组上调用这个循环工具，括号符号(`[]`)将产生类似的效果，就像您在对象上调用它一样。然而，有两个警告:当你处理一个`NodeList`或者只是一个数组中不一致的数据时，数组会影响速度和情况。

至于速度，情况很清楚:当迭代数组时，`for...in`比特定于数组、字符串和`NodeLists`的`for...of`循环慢得多。

为了理解数据不一致的问题，您需要记住`Array`是一个`Object`，因此，您可以为它分配属性(这不应该发生，但是可能发生):

```
let students = ["SpongeBob", "Patrick"]
students.name = "Mr. Krabs"students
// ["SpongeBob", "Patrick", name: "Mr. Krabs"]
```

这是一个不一致数据的例子，我们希望我们的循环可以忽略它。然而，`for...in`是为了迭代属性而创建的，所以它会这样做:

这一点尤其重要，例如，看起来像一个数组，但实际上还包含一堆其他元素(例如，方法)，最好把它们一起跳过。

# `for...of`循环

在 ES6 中也引入了，`for...of`是特定于 iterables 的:数组、字符串、`NodeList` s、集合和映射。它具有一系列强大的功能:

它检查一个元素是否是可迭代的(使用 [GetIterator](http://www.ecma-international.org/ecma-262/6.0/#sec-getiterator) 操作),如果不是，抛出一个适当的`TypeError`:

将其与`for...in`(不应在数组上使用)进行比较:

支持循环体中的各种控制流，如`continue`、`break`、`yield`、`await`。

比`forEach`还快。

# `forEach`

这里有一个语法糖`forEach`，它需要三个参数:

```
arr.forEach(callback(currentElement, index, array) {
  // do something <- this is a callback
});
```

这些是:

*   `currentElement` —被操作的元素(这是唯一的强制元素)
*   `index` —元素的当前索引
*   `array` —正在迭代的数组

`forEach`的一个好处是你可以访问索引，而`for...of`不能。(好吧，有一个漫长的方法可以获得，但这是多余的。)此外，根据定义，这是一个有限循环，因此它将在某个点结束，即使您没有考虑到边缘情况的`break`语句。

还有另一个更难理解的好处:临时变量(上例中的`currentElement`)是局部作用域的，而在`for...of`循环中却不是这样。看这里:

在上面的例子中，我们可以看到`forEach`没有覆盖在其作用域之外声明的`num`变量的值，而`for...of`循环则不是这种情况。然而，如果你不能以一种更具描述性的方式命名你的变量，还有一个解决方法:在`for...of`数组中使用`let`。

# `for`循环

这个循环是 JS 中最古老和最受支持的`for`循环。你可以永远依靠它。它还允许您更有意识地确定哪个是开始/结束元素，以及迭代应该朝哪个方向进行。

然而，开发体验并不完美，因为它的语法并不简单。在适当的地方，可以随意使用`for...in`(处理对象时)或`for...of`(处理数组、字符串、集合、`NodeList`和地图时)。