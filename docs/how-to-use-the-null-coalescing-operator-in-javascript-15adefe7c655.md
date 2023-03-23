# 如何在 JavaScript 中使用空合并操作符

> 原文：<https://betterprogramming.pub/how-to-use-the-null-coalescing-operator-in-javascript-15adefe7c655>

## ECMAScript 2020 有一个新的运算符用于管理未定义的值和空值:？？

![](img/cf86411342a8025dc80823878e7e0768.png)

照片由[派恩瓦特](https://unsplash.com/@pinewatt?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/question?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

ECMAScript 2020 规范有一个新的操作符，用于管理未定义的值或空值。空合并操作符将获取一个初始变量/表达式/语句，并尝试使用它。

如果未定义或为空，出现在`??`之后的第二个操作数将用作后备。

# 工作中的例子

下面是基本数据类型的概要，以及它们如何使用`??`操作符进行评估。

```
let a;
let b = null;
let c = "";
let d = 0;
let e = false;const t1 = a ?? "Default";  // "Default"
const t2 = b ?? "Default";  // "Default"
const t3 = c ?? "Default";  // ""
const t4 = d ?? "Default";  // 0
const t5 = e ?? "Default";  // false
```

# 零合并运算符与逻辑“或”运算符

逻辑“或”短路操作符`||`，其行为与 [Elvis 操作符](https://medium.com/better-programming/did-you-know-theres-an-elvis-operator-1406cb364929)相同，用于协调两个值，类似于零合并操作符。区别在于确定是否使用第一个操作数的规则。

逻辑“或”将强制第一个操作数为布尔值，并且仅在评估`true`时使用。这将排除数字零以及空字符串、对象和数组。

null 合并运算符专门在第一个操作数中寻找未定义的值或 null 值，作为遵从第二个操作数的要求。

```
let a = 0;
let b = 7;// Logical Or Behavior
console.log(a || b);  // 7// Null Coalescing Behavior
console.log(a ?? b);  // 0
```

我们可以把它们写成类似的三元运算符语句，以便更清楚地看出区别。

```
let a = 0;
let b = 7;let logical_or = (a) ? a : b;
console.log(logical_or); // 7let nc = (typeof a == "undefined" || a == null) ? b : a;
console.log(nc);    // 0
```