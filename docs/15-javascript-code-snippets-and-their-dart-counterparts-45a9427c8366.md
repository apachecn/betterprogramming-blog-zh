# 15 个 JavaScript 代码示例及其 Dart 对应物

> 原文：<https://betterprogramming.pub/15-javascript-code-snippets-and-their-dart-counterparts-45a9427c8366>

## JavaScript 和 Flutter 开发人员的快速备忘单

![](img/7605e7f6455c01cddb2923ec6c2ad686.png)

图片来自[天天码](https://everyday.codes/mobile/react-native-vs-flutter-which-one-should-you-use/)

作为一名 React/React 本地开发人员，我最近更多地使用了 Flutter，我遇到过使用 JavaScript 但需要查找 Dart 等价物的情况。

在这篇文章中，我将分享一系列常见的 JavaScript 方法，以及它们在 Flutter 的 Dart 文件中的等价方法。

# `**1\. JSON.stringify**` **和** `**JsonEncoder().convert**`

在 JavaScript 中，如果您想将一个对象转换成一个 JSON 字符串，您可以使用:

```
JSON.stringify(yourObject)
```

在 Dart 中，如果你`import 'dart:convert';`你可以使用下面的代码将你的`yourObject`转换成一个字符串:

```
JsonEncoder().convert(yourObject)
```

# 2.`**JSON.parse**` **和** `**JsonDecoder().convert**`

在 JavaScript 中将 JSON 转换成一个对象，我们将使用下面的代码:

```
JSON.parse(yourJson)
```

在 Dart 中，只需导入`'dart:convert'`就可以使用:

```
JsonDecoder().convert(yourJson)
```

# 3.`**array.push**` **和** `**list.add**`

操纵数组或列表是任何编程语言的重要组成部分。这种数据处理的一个常见部分是向数组/列表中添加内容。

在 JavaScript 中，要将`'hello'`添加到数组中，可以使用:

```
array.push('hello')
```

在 Dart 中，我们使用:

```
list.add(‘hello’)
```

# 4.`**array.splice**` **和** `**list.sublist**`

处理数组/列表的另一个有用的工具是从其中删除一个项目。比方说，您想从`['Jan', 'March', 'April']`中移除`'Jan'`。

JavaScript 使用以下代码删除第一个索引(第一个参数)处的`one`(第二个参数)项

```
array.splice(0, 1)
```

在 Dart 中，您可以将其写成:

```
list.sublist(1, 3)
```

这将返回索引范围(1 和 3)内的新列表。

是的，我们的列表在索引 3 没有项目。这仍然有效，因为第二个参数不是包含性的(这意味着它不包括索引 3 处的项，而是包括比它更低的任何索引。

# 5.`**array.splice**` **和** `**list.removeAt**`

除了能够在 Dart 中使用`list.sublist`之外，如果您试图从列表中删除一个项目，您所需要的就是该项目的索引，并且您可以使用`list.removeAt(itemIndex)`。

对于 JavaScript，`splice`也是同样的用法。as #4。

# 6.`**array.length > 0**` **和** `**list.isNotEmpty**`

检查数组是否为空是很有用的。在 JavaScript 中，您只需:

```
array.length > 0
```

在 Dart 中，您可以将它写成

```
list.isNotEmpty
```

# 7.`**array.length === 0**` **和** `**list.isEmpty**`

除了检查数组中是否有内容，还可以检查它是否为空。

在 JavaScript 中，你应该写

```
array.length === 0
```

在 Dart 中:

```
list.isEmpty
```

# 8.`**parseInt**` **和** `**int.parse**`

如果您想在 JavaScript 中将字符串转换成整数，您可以使用:

```
parseInt('123')
```

在 Dart 中:

```
int.parse('123')
```

# 9.`**parseFloat**` **和** `**double.parse**`

在 JavaScript 中，浮点数是十进制或分数。

在 Dart 中，这种类型的数字称为 double。

因此，像`parseInt`和`int.parse`一样，您可以简单地将`parseFloat(‘1.23’)`用于 JavaScript。`double.parse('1.23')`用于 Dart 将带小数的字符串转换成数字(或者更具体地说，转换成双精度)。

# 10.`**array.some**` **和** `**list.any**`

在 JavaScript 中，`array.some`遍历一个数组，并在每次迭代中执行一个回调函数(作为参数传递给`some`)来评估数组中是否有任何项目满足某个标准。它返回 true 或 false。它在 dart 中的工作方式是一样的，只是它被称为`any`。

例如，如果您想查看数组`[1, 2, 3, 4, 5, 6, 7]`中是否有大于 5 的项:

在 JavaScript 中:

```
var some = array.some(x=>x > 5)
```

在 Dart 中:

```
array.any((x)=>x > 5);
```

# 11.`**array.findIndex**` **和** `**list.indexWhere**`

JavaScript 使用`findIndex`来查找满足所提供回调的项目的第一个索引。

`indexWhere`在 Dart 中做同样的事情，但是看起来有点不一样。见下文。

```
**var** notes = ['do', 're', 'mi', 're'];**JavaScript** notes.findIndex(x=>x.indexOf("r") > -1) // 1**Dart** notes.indexWhere((note) => note.startsWith('r')); // 1
```

**奖励**:在 Dart 中，你也可以在函数后传递一个参数来提供一个开始索引。见下文。

```
notes.indexWhere((note) => note.startsWith('r'), **2**); // 3//this one find the index of the first item that satisfies the callback but the starting point is the second index. This is why the result is 3 and not 1.
```

# 共享方法

## `**1\. array.indexOf()**` **和** `**list.indexOf()**`

在 JavaScript 和 Dart 中，`indexOf`是一种用于在数组或列表中查找给定值的索引的方法。

如果在数组中找不到该项，它们都返回-1。如果给定一个名为`authors`的数组/列表，JavaScript 和 Dart 都可以像这样使用`indexOf`。

```
var authors = ['Edgar Allen Poe', 'JK Rowling', 'Ernest Hemingway']
authors.indexOf('JK Rowling') // 1
```

## 2.`**toString**` **和** `**toString**`

在 JavaScript 中，您可以使用`toString()`将另一种数据类型的值转换为字符串。它适用于整数、浮点、布尔、数组和对象(有点)。我之所以这么说，是因为如果你对一个对象执行`toString` S，你会得到如下结果。

```
var x = {id: 1}
console.log(x.toString())=> '[object Object]'
```

对于一个数组，你会得到数组中的所有项和逗号，但没有方括号。您可以在 dart 中使用该方法，但是您可以将`toString`直接附加到一个值上，这在 JavaScript 中是做不到的。见下文。

```
**Dart**
String test = 1.toString() // '1'**JavaScript**
var x = 1.toString() // Uncaught SyntaxError: Invalid or unexpected token
```

## 3.`**array.every**` **和** `**list.every**`

与`some`或`any`相反，你可以使用`every`来确定数组中的`every`项是否满足某个标准，而不是只满足其中的`some`。`every`接受一个将在每个项目上执行的回调函数，很像`some`和`any`。

## 4.`**map, forEach, filter, find, etc.**`

除了语法上的一些差异之外，上述所有方法都是 JavaScript 和 Dart 共享的。使用`map`时，数组`test`等于`[1, 2, 3, 4, 5]`见下图。

JavaScript:

```
test.map(x=>x*2)
```

省道:

```
est.map((x)=>x*2)
```

你有什么想要添加到列表中的吗？请在评论中告诉我！

[***升级您的免费 Medium 会员资格***](https://matt-croak.medium.com/membership) *并接收来自各种出版物上数千名作家的无限量、无广告的故事。这是一个附属链接，你的会员资格的一部分帮助我为我创造的内容获得奖励。*

*您还可以通过电子邮件* *订阅，每当我发布新内容时，您都会收到通知！*

*谢谢！*