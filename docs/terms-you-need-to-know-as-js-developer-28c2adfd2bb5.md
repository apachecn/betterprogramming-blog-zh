# 作为 JS 开发人员，您需要知道的术语

> 原文：<https://betterprogramming.pub/terms-you-need-to-know-as-js-developer-28c2adfd2bb5>

## 作为开发人员开始使用 JavaScript 术语

![](img/27cd58112138e792e430b689e45e2f5e.png)

图片来自 [Tutorialrepublic](https://www.tutorialrepublic.com/)

在本文中，我们将特别关注作为一名 JS 开发人员，您需要了解的 JavaScript 知识。

# 值和类型

JavaScript 是动态类型的。这意味着变量可以保存任何类型的值，而无需任何类型强制。以下是可用的内置类型:

1.  `string`
2.  `number`
3.  `boolean`
4.  `null`和`undefined`
5.  `object`
6.  `symbol`(ES6 的新功能)

JavaScript 有*类型值*，没有类型变量——JavaScript 中的变量可以包含任何数据。一个变量可以是一个字符串，也可以是一个数字。

```
var a;
typeof a;           // "undefined"a=5;
typeof a;           // "number"a="js"
typeof a;           // "string"
```

`typeof null`是一个有趣的例子，因为当您期望它返回`null`时，它却错误地返回了`object`。

# 目标

对象用于存储各种数据和更复杂实体的键控集合，您可以在其中设置保存其自身值的属性。
可以使用点符号(即 obj.a)或括号符号(即 obj["a"])访问属性。点符号更短，通常更容易阅读。因此，如果可能的话，这是优选的。

```
var obj = {
 a: "js",
 b: 2
}obj.a;             // "js"
obj["b"];          // 2
```

# 排列

数组是保存值(任何类型)的对象。不是特别在命名属性/键中，而是在数字索引位置中。例如:

```
var arr = ["js", 2, true];arr[0];            // "js" 
arr[1];            //  2
arr[2];            //  true
```

# 内置类型方法

JavaScript 提供不同的数据类型来保存不同类型的值。JavaScript 中有两种数据类型:

1.  原始值
2.  非原始值(对象引用)

我们可以拥有非原始值的属性和方法。但是我们如何使用长度作为属性，使用`toUpperCase()`作为原始值的方法呢？

```
const a = "hello world";a.length;                   // 11
a.toUpperCase();            // "HELLO WORLD"
typeof a;                   // "string"
```

当您使用像`a`这样的原始值作为对象时，通过引用属性或方法(例如，前面代码片段中的`a.toUpperCase()`), JS 会自动将值“装箱”到它的对象包装器对应物(隐藏在幕后)。

字符串值可以由 string 对象包装，数字可以由 number 对象包装，布尔值可以由 boolean 对象包装。

**装箱**是将一个对象包装在一个原始值周围。例如，
`new Number(42)`为图元数 42 创建一个数字对象。

# 真实与虚假

JavaScript 中 falsy 值的具体列表如下:

`“ ”`(空字符串)
`0``-0``NaN`(无效数字)
`null``undefined`
`false`

任何不在这个虚假列表上的值都是真实的。比如:`[ ]`、`[1,2,3]`、
、`{}`、`hello`、`2`、`{a: 7}`。

# 平等

有四个等式运算符:`==`、`===`(等式)，以及`!=`、`!==`(不等式)。

`==`和`===`的区别通常表现为:`==`检查值相等，`===`检查值和类型相等。然而，这是不准确的。描述它们的正确方式是，`==`在允许强制的情况下检查值相等，而`===`在不允许强制的情况下检查值相等。

`===`通常被称为*严格相等，*和`==`则是*宽松相等*正因如此。

# 强迫

强制是将值从一种类型转换为另一种类型的过程(比如字符串转换为数字，对象转换为布尔值等等)。它有两种形式——显性和隐性。

显性胁迫的例子:

```
var a = “42”; 
var b = Number( a );a;          // “42” 
b;         // 42 — the number!
```

隐式强制的示例:

```
var a = “42”; 
var b = a * 1;     // “42” implicitly coerced to 42 herea;  // “42” 
b; // 42 — the number!
```

# 不平等

`<`、`>`、`<=`和`>=`运算符用于不等式，在规范中称为*关系比较*。

它们将与可比较的值一起使用，如数字。但是 JavaScript 字符串值也可以进行不相等的比较。

上面不等式表达式的答案是什么？真的还是假的？

在找到解决方法之前，我们先来看看两个字符串，或者数字和字符串是如何比较的。

如果`<`比较中的两个值都是字符串，就像`b > d`一样，那么比较是按字典顺序进行的(也就是像字典一样按字母顺序)。

与相等运算符类似，强制也适用于不等运算符。如果其中一个或两个都不是字符串，就像`a < b`一样，那么两个值都被强制为数字，并且出现典型的数字比较。

```
a < c       // true       convert "53" to 53,  42 < 53
a < b;      // false	  convert "foo to NaN, 42 < Nan
a > b;      // false      convert "foo to NaN, 42 >Nan
a == b;     // false      interpreted as 42 == NaN or "42" == "foo"
b > d;      // true       f come after b in alphabetic order
```

当把`foo`转换成一个数字时，我们得到`invalid number value`(`NaN`),`NaN`既不大于也不小于任何其他值。

# 功能范围

使用`var`关键字声明一个属于当前函数作用域的变量，或者声明一个全局作用域，如果它在任何函数之外的顶层。

我们还有两个变量声明的关键字；`let`和`const`。它们是块范围。

# **吊装**

当 JavaScript 编译所有代码时，所有使用`var`的变量声明都被提升到它们的函数/局部作用域的顶部(如果在函数内声明)，或者提升到它们的全局作用域的顶部(如果在函数外声明)——不管实际声明是在哪里进行的。这就是我们所说的*吊装*的意思。

函数声明也会被提升，但是它们会在最上面，所以会位于所有变量声明之上。

```
console.log(myName);    
var myName = ‘Sunil’;*What will be the answer - 
1\. Uncaught ReferenceError: myName is not defined* *2\. Sunil* *3\.* undefined
```

在上面的例子中，答案将是，“`undefined`”。

为什么？正如我们前面提到的，当 JavaScript 在运行时编译时，变量被移动到它们作用域的顶部。因此，当 JavaScript 被编译时，`var myName`将被移到其作用域的顶部。

唯一移到顶部的是变量声明，而不是赋予变量的实际值。

这就是为什么我们得到了 undefined，而不是一个`ReferenceError`，因为变量是在作用域的顶部声明的。

# 关闭

闭包是 JavaScript 中最重要的概念之一，也是最不容易理解的概念。闭包是一个函数，定义在另一个函数(称为父函数)中，并且可以访问在父函数作用域中声明和定义的变量。

闭包可以在三个范围内访问变量:

*   变量在自己的作用域中声明
*   父函数作用域中声明的变量
*   在全局命名空间中声明的变量

当我们调用`makeAdder(10)`时，我们得到一个对其内部`add(..)`的引用，该引用将 x 记为 10。我们称这个函数引用为`plusTen(..)` —它把它的内部 y( 3)加到被 x( 10)记住的。

# 该标识符

JavaScript 中另一个经常被误解的概念是 *this* 关键字。

如果一个函数内部有一个`this`引用，那么`this`引用通常指向一个对象。但是它指向哪个对象取决于函数是如何被调用的。

重要的是要认识到`this`不是指函数本身，这是最常见的误解。

为了理解这意味着什么，你必须检查一下这个函数是如何被调用的。这将是上面显示的四种方式之一，然后将回答这是什么。

# 原型

当您引用一个对象的属性时，如果该属性不存在，JavaScript 将自动使用该对象的内部原型引用来查找另一个对象以寻找该属性。如果属性丢失，您可以将此视为一种备用方法。

从一个对象到其后备的内部原型引用链接发生在创建对象的时候。说明这一点的最简单方法是使用一个名为`Object.create(..)`的内置实用程序。

我们讨论的所有术语对于成为一名优秀的 JS 开发人员都是必要的，您需要熟悉这些术语。

您也可以开始阅读官方的 JS 文档来学习 dept 中的所有这些概念。

希望这篇文章对你有用。感谢阅读，继续学习！