# JavaScript 的对象构造函数:防止更改和获取条目

> 原文：<https://betterprogramming.pub/javascripts-object-constructor-preventing-changes-and-getting-entries-1d27882c19f3>

## 如何防止更改和获取条目

![](img/fb375c5037f3e3eb13743c683e519a52.png)

由[德尔菲·德拉鲁阿](https://unsplash.com/@delfidelarua7?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 JavaScript 中，`Object`构造函数让我们用给定的值创建一个对象包装器。如果将`null`或`undefined`传递给`Object`构造函数，它将创建一个空对象。

如果传入构造函数的值已经是一个对象，那么它将返回该对象。

`Object`构造函数有两个属性。它有一个始终为 1 的`length`属性，像所有其他对象一样，`Object`构造函数有一个原型来获取类型`Object`的所有附加属性。

在本文中，我们将研究如何防止更改并获取键和值。

# `Object.isSealed()`

`Object.isSealed()`方法用于检查对象是否密封。

密封一个对象意味着我们防止新的属性被添加到一个对象中，并将所有现有的属性标记为不可配置，这意味着属性的属性描述符不能改变，属性不能被删除。

属性描述符是一个对象，其属性名作为关键字，属性`writable`、`configurable`、`enumerable`和`value`作为属性名关键字的属性。

`writable`表示属性值可以更改。`configurable`表示属性描述符是否可以更改，以及属性是否可以从对象中删除。

`enumerable`属性表示该属性在用`for...in`循环枚举属性的过程中出现，`value`是该属性的值。

如果对象是密封的，则返回`true`，否则返回`false`。例如，我们可以使用如下函数:

```
let obj = {};
const objSealed = Object.isSealed(obj);
console.log(objSealed);
```

这是最简单的情况，其中对象没有被显式密封或者修改了属性描述符。上面的`console.log`语句将返回`false`,因为对象不是密封的。

```
let obj = {};
Object.preventExtensions(obj);
const objSealed = Object.isSealed(obj);
console.log(objSealed);
```

如果我们调用`Object.preventExtensions()`方法来阻止属性被添加，那么上面代码中的`objSealed`将返回`true`，因为我们用`Object.preventExtensions()`方法阻止了属性被添加，并且对象中没有现有的属性。这只适用于空对象。

如果我们将带有`configuration`属性的属性描述符设置为`false`，并对对象调用`Object.preventExtensions()`，那么`Object.isSealed()`将返回`true`，因为我们阻止了对象属性的属性描述符被修改或删除。

例如，如果我们有:

如果我们在一个对象上调用`Object.seal()`方法，那么它会做一切事情来密封该对象，包括防止属性被添加，防止现有属性的属性描述符被修改，以及防止对象的属性被删除。

例如，如果我们有:

```
let sealedObj = { a: 1 };
Object.seal(sealedObj);
console.log(Object.isSealed(sealedObj));
```

上面的`console.log`将记录`true`，因为我们在对象上调用了`Object.seal()`。

# `Object.keys()`

`Object.keys()`方法返回一个字符串形式的对象属性名数组，这些属性名被设置为可枚举的。它们的返回顺序与普通循环相同。

如果一个数组作为参数传入，那么我们返回数组的索引。例如，如果我们有:

```
const obj = {
  a: 1,
  b: 2,
  c: 3
};
console.log(Object.keys(obj));
```

然后，当我们运行`console.log`时，我们得到`[“a”, “b”, “c”]`，因为我们在对象`obj`中有这些属性名。

我们也可以将数组传递给这个方法。例如，我们可以写:

```
const arr = [1, 2, 3];
console.log(Object.keys(arr));
```

然后，当我们运行`console.log`时，我们得到`[“0”, “1”, “2”]`，因为我们在`arr`数组中有这些属性作为索引。如果我们有一个类似数组的对象，它包含所有的数字键，那么我们将按顺序返回这些键。

例如:

```
const obj = {
  2: 1,
  3: 2,
  1: 3
};
console.log(Object.keys(obj));
```

然后，当我们运行`console.log`时，我们得到`[“1”, “2”, “3”]`，因为我们有数字键，`Object.keys()`方法将按升序返回数字键。

如果我们有数字键和非数字键的混合，那么数字键将在其余的键被返回之前以升序被返回。例如，如果我们有:

```
const obj = {
  a: 1,
  3: 2,
  1: 3
};
console.log(Object.keys(obj));
```

当我们运行`console.log`时，我们得到`[“1”, “3”, “a”]`。

`Object.keys()`只返回可枚举属性的名称。例如，如果我们有:

那么`console.log`将返回一个空数组，因为属性`a`有一个属性描述符，其中`enumerable`被设置为`false`。

# `Object.preventExtensions()`

`Object.preventExtensions()`方法防止新属性被添加到对象中。在对其调用`Object.preventExtensions()`后，不能添加新属性。

但是，仍然可以删除现有属性。一个对象不可扩展后就不能再扩展了。它的返回值是不可扩展的对象。例如，如果我们有:

```
let obj = {};
Object.preventExtensions(obj);
Object.defineProperty(obj, 'a', {})
```

我们将得到一条错误消息，说明:`‘Cannot define property a, object is not extensible.’`

这是我们所期望的，因为我们阻止了使用`Object.preventExtensions()`方法将属性添加到对象中。

如果我们尝试用点符号向它添加一个属性，如下面的代码所示:

```
let obj = {};
Object.preventExtensions(obj);
obj.a = 1;
console.log(obj);
```

自从我们对它调用了`Object.preventExtension()`之后，我们得到了一个空的日志对象。如果我们像下面的代码一样以严格模式运行上面的代码，我们将得到`‘Cannot define property a, object is not extensible.’`:

```
'use strict';
let obj = {};
Object.preventExtensions(obj);
obj.a = 1;
console.log(obj);
```

如果我们向该方法传递一个原始值，它将被原样返回，就好像它是一个不可扩展的对象。例如，如果我们有:

```
console.log(Object.preventExtensions(1));
```

那么我们得到 1，因为它原样返回原始值。在 ES5 中，相同的代码将记录一个`TypeError`。

# `Object.seal()`

`Object.seal()`方法密封一个对象。密封一个对象意味着我们防止新的属性被添加到一个对象中，并将所有现有的属性标记为不可配置，这意味着属性的属性描述符不能改变，属性不能被删除。

属性的值仍然可以被修改，除非它们的属性描述符的`writable`属性被显式地设置为`false`。原型链没有被修改，但是`__proto__`属性也被密封。

在对象上调用`Object.seal()`之前，可以添加属性，修改属性描述符，或者随意删除属性。例如，我们可以写:

如果我们在`obj`上运行了`console.log`，那么我们会看到我们得到了`{a: 3}`，因为我们修改了`a`的值并删除了`b`属性。

默认情况下，JavaScript 对象是不密封的，所以您可以随意更改属性。

一旦我们对一个对象调用`Object.seal()`，如下面的代码所示:

我们在`console.log`输出中得到`{a: 3, b: 2}`,因为`Object.seal()`阻止属性被删除，但是不阻止属性值被赋值。例如，如果我们有以下代码:

```
let obj = {
  a: 1,
  b: 2
};
Object.seal(obj)
console.log(Object.getOwnPropertyDescriptors(obj));
```

然后我们得到:

在`console.log`中，因为`Object.seal()`的全部目的是将每个属性描述符的`configurable`属性设置为`false`。

这意味着不能再更改它们的属性描述符，也不能从对象中删除这些属性。

![](img/44515a3124a5a5978d408e5df1cc9b02.png)

[阿尔瓦罗·雷耶斯](https://unsplash.com/@alvaroreyes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# `Object.setPrototypeOf()`

`Object.setPrototypeOf()`方法让我们设置一个对象的原型。它需要两个参数。第一个参数是要更改其原型的对象，第二个参数是要将其设置为的原型对象。

这意味着我们改变了调用`Object.setPrototypeOf()`的对象所继承的对象。

该方法不能在不可扩展的对象上调用，也就是说，不能在那些有`Object.isExtensible()`返回`false`的对象上调用。如果`prototype`参数不是一个对象或者它是`null`，它什么也不做。

例如，如果我们有一个普通的对象，那么我们可以用`Object.setPrototypeOf()`方法设置对象的原型。返回具有原型集的对象。例如，如果我们有:

然后我们得到`{a: 3, b: 4}`作为`newObj`的值，它的原型是`proto`对象。我们也可以用这个方法将一个对象的原型设置为`null`:

```
let obj = {
  a: 3,
  b: 4
};
const newObj = Object.setPrototypeOf(obj, null);
console.log(newObj);
```

然后我们得到`{a: 3, b: 4}`作为原型为`null`的`newObj`的值。如果设置原型的对象是不可扩展的，则该方法不起作用。例如，如果我们有:

然后我们会得到一个`TypeError`因为对象是密封的，也就是说所有的属性包括 prototype 属性都不能添加。

同样，如果我们冻结一个对象，这将密封一个对象，并防止属性值被更改，当我们调用此方法时，它也将失败。例如，如果我们有:

然后我们会得到一个`TypeError`。

# `Object.values()`

`Object.values()`方法返回一个对象数组，这些对象是对象中可枚举的顶级属性的值。它们以与`for...in`循环中相同的顺序返回。

然而，`Object.values()`不返回原型链上的属性值。例如，如果我们有:

```
const obj = {
  a: 1,
  b: 2,
  c: 3
};console.log(Object.values(obj));
```

然后我们得到`[1, 2, 3]`，因为我们在`obj`对象中列出了三个属性。如果我们有一个像 array 这样的对象，所有的数字键都在里面，那么它们是按升序排列的。例如，如果我们有:

```
const obj = {
  6: 1,
  5: 2,
  4: 3
};
console.log(Object.values(obj));
```

然后我们记录了`[3,2,1]`,因为这些键都是数字，所以是升序排列的。如果我们有一个数组，那么这个数组将原样返回。例如，如果我们有:

```
const arr = [1, 2, 3]
console.log(Object.values(arr));
```

然后我们记录了`[1, 2, 3]`，因为我们向`Object.values()`方法传递了一个数组。如果我们有不可枚举的属性值，那么它们不会被列出。例如，如果我们有:

然后我们记录了`[“a”]`,因为我们只有属性`a`是可枚举的。

# 结论

`Object`构造函数有更多的方法来从一个带有键值的数组中构造对象，还有从对象、属性名和属性符号中获取属性描述符的方法。

它获取对象的键，防止属性被添加、删除或修改它们的属性描述符。

它们对于控制属性的行为非常有用，因为 JavaScript 对象在默认情况下是动态的。这些方法可以更容易地防止意外更改。