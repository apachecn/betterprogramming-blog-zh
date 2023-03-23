# 如何检查未定义/Null/空字符串

> 原文：<https://betterprogramming.pub/how-to-check-for-an-undefined-null-empty-string-2b67f627d888>

## 确定什么是有效的，并使用 duck-typing 来检查所有三个

![](img/d6b1b9a7e084b54af9f6b5a0545f22af.png)

照片由[亚伦·伯顿](https://unsplash.com/@aaronburden?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/three?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

验证数据时的一个常见任务是确保一个应该是字符串的变量具有有效值。在填写表单的上下文中，这通常意味着用户输入了一个值。

# 每种类型之间的差异

让我们首先澄清这样一个事实:未定义的字符串、空字符串和空字符串是相似的，但最终是不同的。

## **未定义**

解释这种状态最简单的方法是将变量视为不存在或未声明。

例如，如果您试图打印尚未声明的变量`a`，您将收到错误`ReferenceError: a is not defined`。类似地，初始化变量的数据类型是“未定义的”。

```
console.log(a); // ReferenceError: a is not definedlet b;
console.log(b); // undefinedconsole.log(typeof a); // "undefined"
console.log(typeof b); // "undefined"
```

## **空值**

与 undefined 不同，标识符确实存在；然而，它没有任何价值。

我知道，这很奇怪，因为这个值实际上是`null`。两者颇为相似，但说到底，做一个严格的比较，就能看出两者的不同。

```
let a;
console.log(a); // undefined
console.log(a == null); // true
console.log(a === null); // false
```

## **空字符串**

一个数据类型为`“string”`但内部没有任何字符数据的变量。

空字符串是有效的字符串；然而，从功能上来说，如果它是空的，那么它做不了什么，而且当需要字符数据时，它经常会引起问题。

```
let a = "";console.log(typeof a); // "string"// trying to capitalize the first letter
a = a[0].toUpperCase() + a.slice(1);
// "TypeError: Cannot read property 'toUpperCase' of undefined
```

在大多数情况下，当我们比较所有三个变量时，每个变量状态都是不同的。

```
let a;
let b = null;
let c = "";console.log(a == b);  // true
console.log(a == c);  // false
console.log(b == c);  // falseconsole.log(a === b); // false
console.log(a === c); // false
console.log(b === c); // false
```

既然我们已经阐明了每个状态，我们需要问这样一个问题:“空字符串是无效值吗？”根据答案有两种解决方案。

# 空字符串无效

在空字符串无效的情况下——意味着我们不想接受它，或者我们必须优雅地处理它——那么我们能够利用鸭式输入将这三种状态视为等价。

```
let a;
let b = null;
let c = "";if(a) { console.log("Valid"); } // does not print
if(b) { console.log("Valid"); } // does not print
if(c) { console.log("Valid"); } // does not print
```

如果你想知道什么是鸭式打字，这个术语暗指短语:

如果它像鸭子一样走路，像鸭子一样游泳，像鸭子一样嘎嘎叫，那么它很可能就是一只鸭子。

本质上，duck typing 优先考虑对象的属性和方法，而不是它的实际类型。

# 空字符串有效

当一个空字符串是一个有效条目时，我们需要添加到我们的 duck-type 表达式中，或者选择更显式的比较。

```
let a;// the following two expressions evaluate true and would print
if(a || typeof a == "string") {
   console.log("Valid");
}if(typeof a != "undefined" && a != null) {
   console.log("Valid");
}
```

对于第二个例子，表达式的顺序很关键。

因为在未定义的变量上检查 null 会导致错误，所以我们需要首先验证 undefined。

在`a`未定义的情况下，表达式将短路，跳过空值比较并避免错误。