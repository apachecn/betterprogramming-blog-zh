# 探索 JSON、JSON5 和循环引用

> 原文：<https://betterprogramming.pub/exploring-json-json5-and-circular-references-2b5b0c5de532>

## JavaScript 对象符号(JSON)的深度指南

![](img/33eaf9c8770e13b0ee4280627d6f7e62.png)

由 [Erlend Ekseth](https://unsplash.com/@er1end?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 反对字符串问题

在 JavaScript 和 TypeScript 中，对象是属性的集合。例如:

```
const value = {a: 1, b: 2};
```

可以记录该值:

```
console.log(value); // {a: 1, b: 2}
```

写封短信怎么样？

```
console.log(`My value is ${value}`); // My value is [object Object]
```

行动。

什么是`[object Object]`？它是对象的`toString()`值。

`console.log`可以通过提供对象作为独立参数来修复消息。

```
console.log('My value is', value); // My value is {a: 1, b: 2}
```

然而，还有其他使用`toString()`序列化对象的情况。例如，JSX 元素:

```
<div>{value}</div>
```

在许多情况下，`JSON.stringify()`是救命恩人。

我们将仔细研究 JavaScript 对象符号(JSON)。

# JSON 是什么？

JSON(JavaScript 对象符号)是一种轻量级的数据交换格式。人类很容易读写，机器也很容易解析生成。它基于 JavaScript 编程语言标准 ECMA-262 第三版(1999 年 12 月)的子集。

JSON 是由道格拉斯·克洛克福特在 2001 年 3 月首先规定的。它是序列化对象、数组、数字、字符串、布尔值和`null`的语法。它于 2013 年 10 月成为 ECMA 国际标准。

JSON 有两个主要功能:

*   它是在客户端和服务器之间传输的数据格式。
*   它用于定义配置。

JSON 语法简单，对数据类型的支持有限，包括`object`、`array`、`number`、`string`、`boolean`和`null`。但是，函数、`NaN`、`Infinity`、`undefined`和`Symbol`不是有效的 JSON 值。JSON 没有名称空间、注释或属性支持。它可能不支持复杂的配置。这些限制使 JSON 变得简单，因此它可以快速传输和解析。

JSON 有两个静态方法，`JSON.parse()`和`JSON.stringify()`。

## JSON.parse()

`JSON.parse(text)`解析一个 JSON 字符串来构造一个 JavaScript 值或对象。对于对象，JSON 的属性名必须是双引号字符串，并且禁止尾随逗号。对于原始类型，`JSON.parse()`返回原始值。对于数字，禁止前导零，小数点后必须至少有一位数字。任何违反 JSON 语法的行为都会抛出`SyntaxError`。

下面是关于`JSON.parse()`如何构造 JavaScript 值或对象的例子:

`JSON.parse(text[, reviver])`有一个可选的`reviver`，可以改变返回值。

`reviver`(第 2-12 行)被调用 3 次。

*   第一次是键`a`，在第 4 行，值(`1`)减少到`0`。
*   第二次是键，`b`，在第 8 行，值(`2`)增加到`3`。
*   第三次为 key，`""`，值为当前对象(`{"a":0,"b":3}`)。在第 11 行，返回值被转换成一个字符串，`key is "", and value is {"a":0,"b":3}`。

第 15 行日志`key is "", and value is {"a":0,"b":3}`。

## JSON.stringify()

`JSON.stringify(value)`返回指定`value`对应的 JSON 字符串。`boolean`、`number`和`string`被转换成相应的原始值。函数、`undefined`和`Symbol`不是有效的 JSON 值，在对象中被省略，或者在数组中被改为`null`。`NaN`、`Infinity`、`null`改为`null`。如果值有一个`toJSON()`方法，数据序列化调用这个方法。`Date`通过返回字符串`date.toISOString()`实现`toJSON()`功能。JSON 不能序列化`BigInt`值或不可枚举的属性。

下面是关于`JSON.stringify()`如何组成 JSON 字符串的例子:

`JSON.stringify(value[, replacer])`有一个可选的`replacer`，可以改变返回值。

我们写一个`replacer`，类似于`JSON.parse()`中的`reviver`。猜猜会记录什么？

第 2–12 行定义了`replacer`，它输出`"key is \"\", and value is {\"a\":1,\"b\":2}"`。

A `replacer`与 a `reviver`相对。第一次调用有键`""`和值`{"a":0,"b":3}`。在第 11 行，它返回一个字符串值，`"key is \"\", and value is {\"a\":1,\"b\":2}"`。因为它是一个没有下一次迭代属性的字符串，所以`replacer`退出。第 15 行日志`"key is \"\", and value is {\"a\":1,\"b\":2}"`。

如果我们将第 11 行改为`return {a: 5};`，那么下一次调用将是对属性`a`的调用。由于没有其他属性，`replacer`退出。第 15 行日志`{"a":4}`。

如果我们把 11 号线改成`return {c: 5};`呢？然后，下一次调用将在属性上进行，`c`。再次循环属性`c`，返回`{c: 5}`。变成一个无限循环，抛出一个错误:`Uncaught RangeError: Maximum call stack size exceeded`。

我们应该非常小心地写一个`replacer`。对空键的第一次调用应该只是返回原始值。下面是典型的`JSON.stringify`带`replacer`:

第 13 行日志`{"a":0,"b":3}`。

`JSON.stringify(value[, replacer[, space]])`有第二个可选参数`space`，这是一个`string`或`number`，它在输出的 JSON 字符串中插入空格以提高可读性。

`\n`是换行符，`\n`是制表符。`'{\n "a": 1\n}'`指以下结构:

```
{
  "a": 1
}
```

# JSON5 是什么？

JSON 很酷，JSON5 更酷！

[JSON 5 数据交换格式](https://json5.org/) (JSON5)是 JSON 的超集，旨在通过扩展其语法来包括来自 [ECMAScript 5.1](https://www.ecma-international.org/ecma-262/5.1/) 的一些产品，从而减轻 JSON 的一些限制。

## 目标

*   对象键可以是 ECMAScript 5.1 标识符名称。
*   对象可以有一个逗号结尾。

## 数组

*   数组可以有一个逗号结尾。

## 用线串

*   字符串可以用单引号括起来。
*   通过转义新的行字符，字符串可以跨越多行。
*   字符串可能包含字符转义。

## 数字

*   数字可以是十六进制的。
*   数字可能有一个前导或尾随小数点。
*   数字可以是`Infinity`、`-Infinity`、`NaN`。
*   数字可以以一个明确的加号开始。

## 评论

*   允许单行和多行注释。

## 空格

*   允许额外的空白字符。

JSON5 的静态 API`parse`和`stringify`与 JSON 的相同。

为了使用 JSON5，我们需要安装包`[json5](https://github.com/json5/json5)`，它有 5000 万的周下载量。

```
npm i json5
```

`json5`成为`package.json`中`[dependencies](/package-jsons-dependencies-in-depth-a1f0637a3129)`的一部分:

然后，我们可以从`json5`导入`parse`和`stringify`。我们有些失败的案例是用 JSON5 的。

# 如何转换圆形结构

JSON/JSON5 比简单的`toString()`更强大。`JSON.stringify()`解决了`[object Object]`的对象序列化问题。原来的问题解决了。

然而，我们又遇到了一个新问题。

你遇到过错误:`Uncaught TypeError: Converting circular structure to JSON`？

作为一名 JavaScript/TypeScript 开发人员，您可能已经多次遇到过这个错误。这里有一个例子:

创建循环引用是糟糕的编码。但是，如果糟糕的 JSON 结构来自后端或第三方包，我们可能别无选择。

我们如何处理循环引用？

有几种方法可以解决这个问题:

*   `JSON.stringify`的`replacer`
*   第三方软件包

## `JSON.stringify’s replacer`

循环引用的话，JSON 或者 JSON5 `stringify`的`replacer`可以做拯救者。这里是 MDN 网站上的示例代码。

第 3–14 行定义了一个函数`getCircularReplacer`。它返回一个创建`seen`闭包的函数，这个闭包是一个在集合中存储弱持有对象的`WeakSet`。对于每个键，它验证该值是否已经在`seen`中(第 7 行)。如果是，这是一个循环引用，则忽略键/值对(第 8 行)。否则，将该值添加到`seen`(第 10 行)并返回(第 12 行)。

第 15 行调用`getCircularReplacer`返回`replacer`函数，`JSON.stringify()`输出`{"a":1,"b":2}`，去掉循环属性。

## 第三方软件包

有许多第三方软件包可以解决循环引用问题。`[json-stringify-safe](https://github.com/moll/json-stringify-safe)`是一个受欢迎的，有 1700 万周下载量。这是一个类似于`JSON.stringify`的包，但是不抛出循环引用。可以使用以下命令进行设置:

```
npm i json-stringify-safe
```

`json-stringify-safe`成为`package.json`中`dependencies`的一部分:

这个包有一个方法，`stringify`。该方法有四个参数，`stringify(obj, serializer, indent, decycler)`。前三个参数与`JSON.stringify`相同。默认情况下，`stringify`将循环引用显示为一个字符串，`'[Circular]'`。`decycler`可以自定义显示方式。

第 5 行显示了带有循环引用的 JSON 结构的默认`stringify`结果。

第 6 行定制结果以删除循环引用的属性。

下面是关于`json-stringify-safe`如何定义`stringify`的代码:

# 结论

JSON 是一种轻量级的数据交换格式。它比对象的`toString()`更优雅地处理对象序列化。

我们已经详细研究了 JSON 和 JSON5 的工作原理，并提供了多种方法来解决循环引用问题。

感谢阅读。我希望这有所帮助。如果你有兴趣，可以看看[我的其他媒体文章](https://jenniferfubook.medium.com/jennifer-fus-web-development-publications-1a887e4454af)。