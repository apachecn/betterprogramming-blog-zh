# 带有简单示例的 JavaScript ES2021 特性

> 原文：<https://betterprogramming.pub/javascript-es2021-features-with-simple-examples-ada723b55355>

## JavaScript 的最新进展

![](img/64a62e15c98b993d4dd871801fa36a97.png)

在 [Unsplash](https://unsplash.com/s/photos/2021?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Waldemar Brandt](https://unsplash.com/@waldemarbrandt67w?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

今天，我们将了解 2015 年的 ECMAScript 特性:

*   ES2015 又名 ES6
*   [ES2016 又名 ES7](https://medium.com/better-programming/javascript-es2016-features-with-examples-a41b7aead589)
*   [ES2017 又名 ES8](https://medium.com/better-programming/javascript-es2017-features-with-examples-877f8406e770)
*   [ES2018 又名 ES9](https://medium.com/better-programming/javascript-es2018-features-with-examples-30fda8ac50fa)
*   [ES2019 又名 ES10](https://medium.com/better-programming/twelve-es10-features-in-twelve-simple-examples-6e8cc109f3d3)
*   [ES2020 又名 ES11](https://medium.com/better-programming/javascript-es2020-features-with-simple-examples-d301dbef2c37)
*   ES2021 又名 ES12

# 介绍

ES2021 是对应于 2021 年的 ECMAScript 版本。这个版本没有 ES6 (2015)中出现的那么多新功能。但是，已经加入了一些有用的功能。

本文通过简单的代码示例介绍了 ES2021 提供的特性。这样不需要复杂的解释就能快速理解新特性。

当然，有必要对 JavaScript 有一个基本的了解，才能完全理解所介绍的最好的。

ES2021 中新增的 JavaScript 特性有:

➡️`String.prototype.replaceAll`
➡️`Promise.any`
➡️`WeakRef`
➡️逻辑赋值运算符
➡️数字分隔符

# String.protype.replaceAll

目前，如果不使用全局 regexp ( `/regexp/g`)，就无法替换字符串中子字符串的所有实例。

```
const fruits = '🍎+🍐+🍓+';
const fruitsWithBanana = fruits.replace(/\+/g, '🍌');
console.log(fruitsWithBanana); //🍎🍌🍐🍌🍓🍌
```

字符串原型中增加了一个新的`replaceAll`方法。

```
const fruits = '🍎+🍐+🍓+';
const fruitsWithBanana = fruits.replaceAll('+', '🍌');
console.log(fruitsWithBanana); //🍎🍌🍐🍌🍓🍌
```

# 承诺。任何

`Promise.any`一旦承诺兑现，就给你一个信号。这个和`Promise.race`差不多，只不过`Promise.any`不会在其中一个承诺拒绝的时候提前拒绝。

# WeakRef

`WeakRef`提案包含两个主要的新功能:

*   用`WeakRef`类创建对对象的弱引用
*   使用`FinalizationRegistry`类在对象被垃圾收集后运行用户定义的终结器

这些接口可以独立使用，也可以一起使用，这取决于使用案例。

一个`WeakRef`对象包含一个对象的弱引用，这个对象被称为它的*目标*或*引用对象*。对一个对象的*弱引用*是一个不阻止对象被垃圾收集器回收的引用。

弱引用的主要用途是实现保存大型对象的缓存或映射，其中希望大型对象不会仅仅因为出现在缓存或映射中而保持活动状态。

`FinalizationRegistry`提供了一种方法，当在注册表中注册的对象被*回收*(垃圾收集)时，请求在某个时间点调用*清理回调* ( *终结器*)。

您创建了传入回调的注册表:

```
const registry = new FinalizationRegistry(heldValue => {
  // ....
});
```

然后，通过调用`register`方法，传入对象及其保存的值，注册任何想要清理回调的对象:

```
registry.register(theObject, "some value");
```

# 逻辑赋值运算符

逻辑赋值运算符组合了逻辑运算符和赋值表达式。有两个新运算符:

*   或或等于
*   和等于

```
// Or Or Equals
|   a   |   b   | a ||= b | a (after operation) |
| true  | true  |   true  |        true         |
| true  | false |   true  |        true         |
| false | true  |   true  |        true         |
| false | false |   false |        false        |a ||= b
// Equivalent to:
a || (a = b);// And And Equals
|   a   |   b   | a &&= b | a (after operation) |
| true  | true  |   true  |        true         |
| true  | false |   false |        false        |
| false | true  |   false |        false        |
| false | false |   false |        false        |a &&= b
// Equivalent to:
a && (a = b);
```

# 数字分隔符

该特性允许使用数字组之间的可视分隔来提高数值的可读性。

使用下划线(_，U+005F)作为分隔符有助于提高数值的可读性:

```
1_000_000_000           // A billion
101_475_938.38          // Hundreds of millionsconst amount = 12345_00;  // 12,345 (1234500 cents, apparently)
const amount = 123_4500;  // 123.45 (4-fixed financial)
const amount = 1_234_500; // 1,234,5000.000_001 // 1 millionth
1e10_000  // 10^10000 -- granted, far less useful / in-range... const binary_literals = 0b1010_0001_1000_0101;
const hex_literals = 0xA0_B0_C0;
const bigInt_literals = 1_000_000_000_000n;
const octal_literal = 0o1234_5670;
```

# 结论

JavaScript 是一种活的语言，这对于 web 开发来说是非常有益的。自 2015 年 ES6 出现以来，我们一直在经历这种语言的蓬勃发展。在本文中，我们回顾了 ES2021 中出现的特性。

尽管这些特性中的许多对于 web 应用程序的开发来说可能并不重要，但它们提供了以前只需技巧或大量赘述就能实现的可能性。