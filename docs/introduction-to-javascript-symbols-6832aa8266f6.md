# JavaScript 符号简介

> 原文：<https://betterprogramming.pub/introduction-to-javascript-symbols-6832aa8266f6>

## ES6 引入的新数据类型，您可能已经错过了

![](img/6d9a0ebcefaa754236a9e80d1aee1c91.png)

照片由[斯密特·帕特尔](https://unsplash.com/@mesmitpatel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 ES2015 中，引入了名为`Symbol`的新原语类型。这是一个唯一且不可变的标识符。一旦创建，就不能复制。

每次你创造一个新的符号，它都是独一无二的。符号主要用于对象中的唯一标识符。这是一个符号的唯一目的。

还有一些特殊的符号，我们可以用它们来实现各种操作或者覆盖一些操作的默认行为。

# 定义符号

有一些它自己的静态属性和方法公开全局符号注册表。它就像一个内置对象，但是它没有构造函数，所以我们不能写`new Symbol`用`new`关键字构造一个符号对象。

为了创造新的符号，我们可以写:

```
const fooSymbol = Symbol('foo')
```

注意，每次我们调用`Symbol`函数，我们都会得到一个新的符号，所以如果我们写:

```
Symbol('sym') === Symbol('sym')
```

上面的表达是`false`。这是因为每个符号都是独一无二的。

# 内置符号

有一些内置符号被用作各种方法和值的标识符。当使用一些运算符时，调用带有一些符号的方法。

## `Symbol.hasInstance`

`Symbol.hasInstance`是一个检查对象是否是给定构造函数的实例的方法。当调用`instanceof`操作符时，这个方法被调用。

我们可以如下重写`Symbol.hasInstance`方法:

```
class Foo {  
  static [Symbol.hasInstance](instance) {
    return typeof instance.foo != 'undefined';
  }
}
console.log({ foo: 'abc' } instanceof Foo);
```

在上面的代码中，我们定义了如果有一个`foo`属性的值，那么这个对象就是`Foo`类的一个实例。因此，`{ foo: ‘abc’ } instanceof Foo`应该返回`true`，因为它将`foo`属性设置为`'abc'`。

## `Symbol.isConcatSpreadable`

`Symbol.isConcatSpreadable`是一个布尔值，指示是否应该通过 array `concat`方法在数组中展平一个对象。

我们可以在下面的代码中使用它:

第一个`console.log`应该输出:

```
["a", "b", "c", true, false]
```

第二个应该输出:

```
["a", "b", "c", Array(2)]
```

这是因为在第二个`concat`调用之前，我们将`arr2[Symbol.isConcatSpreadable]`设置为`false`，这防止了`arr2`的内容扩散到由`concat`方法返回的新数组中。

## `Symbol.iterator`

当我们想要为 spread 操作符或`for...of`循环返回一个迭代器时，就会调用这个方法。在运行`for...of`循环时调用它。

例如，假设我们有以下代码:

```
const obj = {
  0: 1,
  1: 2,
  2: 3
};
console.log(obj[0]);
```

如果您尝试使用`for...of`循环或`forEach`函数循环遍历数组，或者尝试对其使用 spread 运算符，那么使用`obj`对象的示例将会导致错误，因为它不是一个可迭代的对象。

我们可以通过添加一个带有符号`Symbol.iterator`的生成器函数来使它可迭代，如下面的代码所示:

然后，当我们像下面这样用`for...of`循环迭代`obj`对象时:

```
for (let num of obj) {
  console.log(num);
}
```

我们得到了新的`obj`对象的条目，我们把它变成了 iterable。

扩展操作符也可以工作。如果我们有以下代码:

```
console.log([...obj]);
```

我们从`console.log`输出中得到`[1, 2, 3]`。

## `Symbol.match`

一个布尔属性，它是替换字符串的匹配子字符串的正则表达式实例的一部分。它由字符串的`replace`方法调用。

例如，我们可以使用它来调用带有正则表达式字符串的`startsWith`和`endsWith`方法:

```
const regexpFoo = /foo/;
regexpFoo[Symbol.match] = false;
console.log('/foo/'.startsWith(regexpFoo));
console.log('/baz/'.endsWith(regexpFoo));
```

重要的部分是我们将`regexpFoo[Symbol.match]`设置为`false`，这表明我们调用的字符串`startsWith`和`endsWith`不是正则表达式对象，因为`isRegExp`检查将表明`'/foo/'`和`'/baz/'`字符串不是正则表达式对象。

否则，它们将被视为正则表达式对象，即使它们是字符串，我们也会得到以下错误:

```
Uncaught TypeError: First argument to String.prototype.startsWith must not be a regular expression
```

![](img/c6ed69ee81a83b77cc914f56362983aa.png)

由 [Niklas Garnholz](https://unsplash.com/@digitalfive?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## `Symbol.replace`

替换字符串的匹配子字符串的正则表达式方法。由`String.prototype.replace`方法调用。

通过使用`Symbol.replace`符号作为方法的标识符，我们可以为我们的对象创建我们自己的`replace`方法，如下所示:

`Replacer`类有一个构造函数，它接受一个可以用来替换当前字符串实例的值。

当我们运行最后一行时，我们应该得到`‘bar’`,因为`string`的值是`'foo'`,我们调用`replace`方法，用我们传递给`Replacer`的构造函数的内容替换它本身。

## `Symbol.search`

一种正则表达式方法，返回与正则表达式匹配的字符串中的索引。由`String.prototype.search`方法调用。

例如，我们可以实现我们自己的`Symbol.search`方法，就像我们在下面的代码中做的那样:

在上面的代码中，我们的`Symbol.search`方法查找`string`，也就是我们调用`search`的字符串，是否有我们传递到`this.value`字段的内容，这个字段是我们调用构造函数时分配的。

因此，我们从`console.log`输出得到`true`，因为`'bar'`在`‘foobar'`中。另一方面，如果我们调用:

```
console.log('foobar'.search(new Searcher('baz')));
```

然后我们得到值`false`，因为`‘baz’`不在`'foobar'`中。

## `Symbol.species`

一种属性，其值为一个函数，即用于创建派生对象的构造函数。

## `Symbol.split`

正则表达式对象的一部分，它根据匹配正则表达式的索引来拆分字符串。它由字符串的`split`方法调用。

## `Symbol.toPrimitive`

将对象转换为相应原始值的方法。当使用`+`一元操作符或者将一个对象转换为原始字符串时，就会调用这个函数。

例如，我们可以编写自己的`Symbol.toPrimitive`方法来将各种值转换为原始值:

然后我们得到:

```
10
hello
true
false
```

来自代码底部的`console.log`语句。

## `Symbol.toString`

返回对象的字符串表示形式的方法。每当一个对象的`toString`方法被调用时，它就会被调用。

## `Symbol.unscopables`

一个对象，它自己的属性名是从相关对象的`with`环境绑定中排除的属性名。

# 结论

符号是 ES6 引入的一种新的数据类型。它们用于识别对象的属性。它们是不可变的，每个实例都被认为是不同的，即使它们可能有相同的内容。

我们可以在自己的代码中实现由特殊符号标识的各种方法，以实现某些操作，如`instanceof`，将对象转换为原始值，以及搜索子字符串。