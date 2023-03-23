# ES2020 的 6 大 JavaScript 特性

> 原文：<https://betterprogramming.pub/the-top-6-javascript-features-from-es2020-9beba927a9ae>

## BigInt、动态导入、globalThis 等等

![](img/545f1114a815b488b7558852c0591187.png)

[McDobbie Hu](https://unsplash.com/@hjx518756?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

ECMAScript 2020 候选在 4 月初获得批准。现在 ECMA 大会必须在 6 月份批准它。

让我们来发现这个新版本中最重要的特性！

# 1.动态导入()

JavaScript 中的动态导入允许您仅在需要时导入 JS 文件。这就是众所周知的代码分割。

以前，在您的项目中使用 Webpack 是可能的。但在 ES2020 的新版本中，该功能将被原生处理。

动态导入是异步的。因此，你必须使用`async` / `await`或`promise`:

# 2.可选链接

这个特性将使我们的源代码更具可读性！

可选的链接语法允许您访问嵌套的对象属性，而不必每次都验证父属性是否存在。

查看对比:

不用声明一个`let`变量并包含一些影响其值的条件，我可以在一行代码中使用一个`const`，不带任何条件。

如果没有找到该属性，则返回`undefined`。超级有帮助！

# 3.无效合并(？？)

Nullish 合并允许您用 nullish 值而不是 falsey 值进行`OR`操作。

如果变量是`undefined`或`null`，我们称之为 nullish。另一方面，falsey 变量可以有许多其他值:`undefined`和`null`当然，还有`''`(空字符串)、`NaN`、`0`和`false`。

查看`||`和`??`的区别:

`||`操作符返回真值，而`??`操作符返回非空值。

# 4.BigInt

`BigInt`是一种新型变量，允许您存储非常大的数字。

目前 JavaScript 中可以存储的最大数是`2^53 — 1`，为 9007199254740991。似乎有些人需要更多。`BigInt`允许开发者无限制存储数字！

要声明一个数字为`BigInt`，你可以使用构造函数`BigInt()`或者只是在你的数字后面加一个`n`:

```
const myBigInt = BigInt(9007199254740992);
const myBigInt2 = 9007199254740992n;console.log(myBigInt); -> 9007199254740992n
console.log(myBigInt2); -> 9007199254740992n
```

您可以比较`BigInt`和`Number`之间的值，但不能严格相等:

```
console.log(0n == 0); -> true
console.log(0n === 0); -> falseconsole.log(1n < 2); -> true
```

但是，您不能在`BigInt`和`Number`之间进行操作:

```
1n + 2 -> TypeError: can't convert BigInt to number
```

# 5.承诺。都解决了

`Promise.allSettled`方法接受一个承诺数组，并在该数组中的每个承诺完成时进行解析，不管它们是被解析还是被拒绝。

当你不在乎一些承诺是否被拒绝时，它会很有用——你只是想知道他们什么时候完成:

# 6.globalThis

今天，JavaScript 在不同的环境中使用:节点、浏览器、网络工作者等。

当您必须访问全局变量`this`时，根据您的环境，您可以使用`window`(浏览器)、`global`(节点)或`self` (web-workers)。

如果你想要一个跨平台的代码，你必须自己处理它，比如:

```
const getThis = () => {
  if (typeof self !== 'undefined') return self;
  if (typeof window !== 'undefined') return window;
  if (typeof global !== 'undefined') return global;
}
```

`globalThis`已被创建以引用全局对象，而不管环境如何。很酷吧。

# 结论

在这篇文章中，我列出了我认为的 ES2020 的六大特性。不过，你也可以很容易地找到其他的。

感谢阅读！