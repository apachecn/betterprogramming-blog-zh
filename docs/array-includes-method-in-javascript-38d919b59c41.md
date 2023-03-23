# 数组包含 JavaScript 中的()和 IndexOf()方法

> 原文：<https://betterprogramming.pub/array-includes-method-in-javascript-38d919b59c41>

## 这些方法的区别以及何时使用它们。

![](img/64487a37df6741b723edaafe8ffcf028.png)

奥斯卡·伊尔迪兹在 [Unsplash](https://unsplash.com/search/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

`Array.prototype.includes()` 方法是在 [ECMAScript](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 6 中引入的。此方法用于确定元素是否包含在数组中。它返回一个布尔值— `true`或`false`。

我们使用`Array.prototype.indexOf()`来找出元素是否出现在数组中。但是它不返回布尔值。它返回在数组中找到的元素的第一个索引，否则将返回`-1`(表示没有找到该元素)。

在本帖中，我们将详细介绍数组`includes()`和`indexOf()`方法，找出它们的区别，并确定何时使用哪种方法。

两种方法都接受相同数量的参数。

# `**Includes()**` **参数**

1.  `ValueToFind`
2.  `fromIndex`(可选——从哪个索引开始搜索。默认为`0`

您可能已经注意到，`_array.indexOf(NaN)` 方法为`NaN`返回`-1`，即使它出现在`_array` *中。*另一方面，`_array.includes(NaN)` 返回`true`。

`includes()`方法使用的值比较不同于`indexOf()`方法，后者使用===进行比较。

> 更新:`includes()`方法使用 SameValueZero 算法进行比较。
> 
> [相同值零](https://ecma-international.org/ecma-262/7.0/#sec-samevaluezero)链接

我建议使用`includes()`方法来检查元素是否出现在数组中。如果需要知道元素在数组中的位置，需要使用`indexOf()`方法。

# **浏览器支持**

除了 Internet Explorer 之外，所有现代浏览器都支持这种方法。你将需要使用聚脂填料。这里有一个关于[浏览器支持](https://caniuse.com/#search=includes())信息的链接。

# **资源**

*   [ECMA 规范](https://www.ecma-international.org/ecma-262/7.0/#sec-array.prototype.includes)
*   [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)

我希望这篇文章对你有用。非常感谢任何改进建议或反馈。

感谢阅读。