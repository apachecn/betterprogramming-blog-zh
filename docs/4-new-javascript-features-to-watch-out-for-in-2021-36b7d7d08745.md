# 2021 年值得关注的 4 个 JavaScript 新特性

> 原文：<https://betterprogramming.pub/4-new-javascript-features-to-watch-out-for-in-2021-36b7d7d08745>

## 数字分隔符、逻辑赋值运算符等等

![](img/f85cfbf31390a14d2e42ef7125a1cc10.png)

[Jr Korpa](https://unsplash.com/@korpa?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/data?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

自 2015 年以来，JavaScript 每年都有语言更新。2021 年也不例外:今年，几个新功能达到了 [TC39 进程](https://tc39.es/process-document/)的第 4 阶段，并且已经可以在 Node.js 和大多数主要的现代浏览器中使用。

在本文中，我们将研究四个最重要的新特性。我们将介绍一个新的 string 方法、一个新的 Promise 方法、对数字可读性的改进以及一个新的赋值简写。

# replaceAll()

首先，我们有一个新的字符串原型方法`replaceAll`，它替换子字符串的每个实例。虽然我们可以用现有的 replace 方法做到这一点，但我们不得不求助于正则表达式，如下所示:

```
"1990-01-01".replace(/-/g, "/");
```

`replaceAll`按理说更具可读性:

```
"1990-01-01".replaceAll("-", "/");
```

使用`replaceAll`，不需要正则表达式。但是，只要提供了`g`标志，它仍然可以使用。

```
"1990-01-01".replaceAll(/-/g, "/");
```

## 支持

chrome:85+
Firefox:77+
Safari:13.1+
Node:15 . 0 . 0+

# 承诺。任何

今年，使用`Promise.any`对异步代码的处理有了进一步的改进，它接受一组承诺并返回第一个解决的承诺。如果每个承诺都被拒绝，就会抛出一个错误。

让我们做四个承诺，看看会发生什么:

```
const p1 = new Promise((*resolve*, *reject*) => setTimeout(reject,  1000, "p1"));const p2 = new Promise((*resolve*, *reject*) => setTimeout(resolve, 2000, "p2"));const p3 = new Promise((*resolve*, *reject*) => setTimeout(reject,  3000, "p3"));const p4 = new Promise((*resolve*, *reject*) => setTimeout(resolve, 4000, "p4"));
```

`Promise.any`将返回先解决的问题。这种情况下，`p2`。我们可以使用下面的代码对此进行测试:

```
async function getFirstResolvedPromise() {
  const result = await Promise.any([p1, p2, p3, p4]);
  console.log(result);
};getFirstResolvedPromise(); // "p2"
```

现在有四种方法可以用来处理一系列承诺:

*   `Promise.all` —一旦所有承诺都已解决，返回一个已解决承诺的数组
*   `Promise.allSettled`(2020 年新增)—一旦所有承诺都被解决或拒绝，则返回一组承诺，无论它们是被解决还是被拒绝
*   `Promise.race` —返回第一个解决或拒绝的承诺
*   `Promise.any` —返回第一个已解决的承诺

## 支持

chrome:85+
Firefox:79+
Safari:14+
Node:15 . 0 . 0+

# 逻辑赋值运算符

自从 2020 年引入 nullish 合并运算符`??`以来，我们现在有三个逻辑运算符:

*   `&&` —逻辑与
*   `||` —逻辑或
*   `??` —无效合并运算符

这些简写允许我们根据一个值是真还是假来简洁地解析表达式——或者，在`??`的情况下——一个值是否为空(`null`或`undefined`)。

现在，我们有了使用这些运算符赋值的更简洁的简写方式:

*   `&&=` —如果变量为真，则分配变量
*   `||=` —如果变量为 falsy，则分配变量
*   `??=` —如果变量为空，则分配一个变量

在下面的代码中，每组三条语句是等效的:

```
// Logical AND assignment
a &&= b;
a = a && b;
a = a ? b : a;// Logical OR assignment
a ||= b;
a = a || b;
a = a ? a : b;// Logical nullish assignment
a ??= b;
a = a ?? b;
a = a === null || a === undefined ? b : a;
```

在下面的例子中，当每个表达式运行时，变量`a`将被重新分配一个新值。代码如下:

```
let a = true;
const b = false, c = null, d = 10;a &&= b;
console.log(a); // falsea ||= c;
console.log(a); // nulla ??= d;
console.log(a); // 10
```

这些新的赋值运算符加入了数学赋值运算符`+=`、`-=`、`*=`、`/=`、`**=`和`%=`，以及按位运算符赋值。

## 支持

chrome:85+
Firefox:79+
Safari:14+
Node:15 . 0 . 0+

# 数字分隔符

最后，今年有一个特性被广泛采用，它为那些处理大量数字或大量小数位的人带来了显著的可读性增强！

现在，我们可以用下划线来分隔数字。

```
const a = 100000000;const b = 100_000_000;console.log(a === b); // true
```

虽然`a`和`b`一模一样，但是`b`更容易识别为 1 亿。实际上，数字分隔符主要用作千位分隔符，尽管这种用法不是强制的。数字分隔符可用于浮点数和 BigInt 值(2020 年引入)。下面的代码展示了这些是如何工作的:

```
const float = 3.141_592_653_59;const bigInt = 9_007_199_254_740_992n;
```

它们也支持十六进制、八进制和二进制数，比如十六进制的`0xFF_FF_FF`或`0x_00_00_00`。

注意`_`不能用在数字的开头或结尾(或者在首字母`0`之后)。此外，它不能与小数点相邻，并且不能在一行中使用多个下划线。

## 支持

chrome:75+
Firefox:70+
Safari:13+
Node:12 . 5 . 0+