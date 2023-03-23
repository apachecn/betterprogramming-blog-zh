# 使用 JavaScript 的对象构造函数—第 2 部分

> 原文：<https://betterprogramming.pub/using-javascripts-object-constructor-entries-and-descriptors-7c76f28fae8e>

## 条目和描述符

![](img/269e7e8d78952621b7aa9db948e8b40c.png)

由 [Kelly Sikkema](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 JavaScript 中，`object`构造函数让我们用给定的值创建对象包装器。如果将`null`或`undefined`传递给`object`构造函数，它将创建一个空对象。如果传入构造函数的值已经是一个对象，那么它将返回该对象。

`object`构造函数有两个属性。它有一个始终为 1 的`length`属性，像所有其他对象一样，`object`构造函数有一个原型来获取类型`object`的所有附加属性。

继续《T21》第 1 部分，构造函数`object`有许多有用的方法，不用构造新的对象就可以使用。

# `Object.fromEntries()`

`Object.fromEntries()`方法接受一个包含键-值对的数组，其中键作为第一个元素，对应键的值作为第二个元素。我们还可以将其他具有相同类型数组的 iterables 传入该方法。

例如，我们可以编写以下代码来传入一个键值对数组以创建一个对象:

```
const entries = [
  [
    ['a', 1],
    ['b', 2]
  ]
];const obj = Object.fromEntries(entries);console.log(obj);
```

运行上面的代码时，我们可以从`console.log`输出中看到，结果对象`obj`应该是`{a: 1, b: 2}`。我们可以传入其他可记录的东西，比如地图:

```
const entries = new Map([
  ['a', 1],
  ['b', 2]
]);const obj = Object.fromEntries(entries);console.log(obj);
```

我们应该看到同样的事情被记录下来。此外，我们可以使用以下代码将数组转换为对象:

```
const arr = [1,2,3];
const entries = arr.map((value, index) => [index, value]);const obj = Object.fromEntries(entries);console.log(obj);
```

当上面的代码运行时，我们得到`{0: 1, 1: 2, 2: 3}`,因为我们将每个数组条目的索引映射到键，并将每个数组条目的值映射到值。

# `Object.getOwnPropertyDescriptor()`

`Object.getOwnPropertyDescriptor()`方法获取对象中属性的属性描述符并返回它。顾名思义，它只获取对象本身的属性描述符，而不是原型链的上游。

属性描述符是一个对象，其属性名作为关键字，属性`writable`、`configurable`、`enumerable`和`value`作为属性名关键字的属性。

`writable`指财产的价值可以改变。`configurable`表示属性描述符可能被更改，并且属性是否可以从对象中删除。

`enumerable`属性意味着该属性在用`for...in`循环枚举属性的过程中出现，`value`是该属性的值。

例如，如果我们用以下命令记录一个对象的属性描述符:

```
const obj = {
  a: 1
}const descriptor = Object.getOwnPropertyDescriptor(obj, 'a');
console.log(descriptor);
```

我们得到`{value: 1, writable: true, enumerable: true, configurable: true}`。`value`是属性的值，其余的属性是属性描述符的属性。如果我们有属性 getters 和 setters，它们也会随方法调用一起返回:

```
let obj = {};
let value;
Object.defineProperty(obj, 'a', {
  get() {
    return value;
  },
  set(a) {
    value = a;
  }
});

const descriptor = Object.getOwnPropertyDescriptor(obj, 'a');
console.log(descriptor);
```

当上面的代码运行时，我们在描述符对象中获得了`get`和`set`函数。

# `Object.getOwnPropertyDescriptors()`

当`Object.getOwnPropertyDescriptor()`获取单个对象的属性描述符时，`Object.getOwnPropertyDescriptors()`获取一个对象中一个对象的所有属性描述符——同样没有这个对象在原型链上继承的属性，属性名作为键，对应属性名键的属性描述符作为值。

例如，如果我们有

```
const obj = {
  a: 1,
  b: 2
}
const descriptors = Object.getOwnPropertyDescriptors(obj);
console.log(descriptors);
```

然后我们得到

```
{
  "a": {
    "value": 1,
    "writable": true,
    "enumerable": true,
    "configurable": true
  },
  "b": {
    "value": 2,
    "writable": true,
    "enumerable": true,
    "configurable": true
  }
}
```

我们获得每个属性的所有值和属性描述符属性。像`Object.getOwnPropertyDescriptor()`一样，这个方法对属性描述符有相同的定义。

属性描述符是一个对象，其属性名作为关键字，属性`writable`、`configurable`、`enumerable`和`value`作为属性名关键字的属性。

`writable`意味着财产的价值可以改变。`configurable`表示属性描述符可以被改变，并且属性是否可以从对象中删除。

`enumerable`属性表示该属性在用`for...in`循环枚举属性时出现，`value`是该属性的值。

# `Object.getOwnPropertyNames()`

`Object.getOwnPropertyNames()`方法返回一个属性名数组，这些属性名是在对象本身而不是原型链上的任何对象中定义的。除了符号属性以外，还会返回不可枚举的属性。例如，如果我们有:

```
let obj = {
  a: 1,
  b: 2
}Object.defineProperty(obj, 'c', {
  "value": 2,
  "writable": true,
  "enumerable": false,
  "configurable": true
})const names = Object.getOwnPropertyNames(obj);
console.log(names);
```

如果我们运行上面的代码，我们会返回`[“a”, “b”, “c”]`,因为除了用符号标识的属性之外，所有定义的属性都会返回。如果我们的对象中有符号，我们将不会在返回的数组中看到它。例如，如果我们有

```
let obj = {
  a: 1,
  b: 2,
  [Symbol('foo')]: 3
}
Object.defineProperty(obj, 'c', {
  "value": 4,
  "writable": true,
  "enumerable": false,
  "configurable": true
})
const names = Object.getOwnPropertyNames(obj);
console.log(names);
```

如果我们运行上面的代码，我们仍然会看到`[“a”, “b”, “c”]`，因为符号没有包含在数组中。我们用`Object.getOwnPropertySymbols()`来获取用符号标识的属性。

# `Object.getOwnPropertySymbols()`

`Object.getOwnPropertySymbols()`返回一个符号数组，这些符号在一个对象的属性中被用作标识符。

它只获取用符号命名的属性标识符，没有其他内容。

此外，它不会遍历原型链来获取原型链上的对象的属性。例如，如果我们有以下代码:

```
let obj = {
  a: 1,
  b: 2,
  [Symbol('foo')]: 3
}
Object.defineProperty(obj, 'c', {
  "value": 4,
  "writable": true,
  "enumerable": false,
  "configurable": true
})
const symbols = Object.getOwnPropertySymbols(obj);
console.log(symbols);
```

我们如预期的那样记录了`[Symbol(foo)]`。

# `Object.getPrototypeOf()`

`Object.getPrototypeOf()`方法获取对象的原型，与指定对象的`[[Prototype]]`属性相同。例如，如果我们有

```
const prototype1 = {};
const obj = Object.create(prototype1);
console.log(Object.getPrototypeOf(obj) === prototype1);
```

`console.log`将输出`true`，因为`Object.create`的第一个参数是它返回的对象的原型对象；同样，如果我们使用`Object.setPrototypeOf()`方法来设置一个现有对象的原型。

```
const prototype2 = {};
let obj2 = {};
Object.setPrototypeOf(obj2, prototype2);
console.log(Object.getPrototypeOf(obj2) === prototype2);
```

由于明确设置了`obj2`的原型，`console.log`也会输出`true`。

![](img/7695644a03c7d754f1a44a1c89c9cd87.png)

尼古拉斯·托马斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# `Object.is()`

`Object.is()`方法比较在其参数中传递的两个对象是否是相同的值。两个值相同，如果它们是:

*   两者`undefined`
*   双双`null`
*   两个`true`或两个`false`
*   两个字符串长度相同，字符顺序相同
*   两个对象具有相同的引用
*   两个数字和都是`+0`，或者都是`-0`，或者都是`NaN`，或者都是非零且都不是`NaN`，并且它们都具有相同的值

它不像`==`操作符那样转换类型来比较对象，也不将真值或假值转换成布尔值。此外，这与使用`===`运算符比较对象不同，因为使用`===`运算符时，`-0`和`+0`相等，`NaN`与自身不同。

例如，我们可以进行以下比较:

```
Object.is('a', 'a');             // true
Object.is(document, document);   // true

Object.is('a', 'b');             // false
Object.is([], []);               // false

const obj = { a: 1 };
const obj2 = { a: 1 };
Object.is(obj, obj);             // true
Object.is(obj, obj2);            // false

Object.is(null, null);           // true

Object.is(0, -0);                // false
Object.is(-0, -0);               // true
Object.is(NaN, 0/0);             // true
```

正如我们所看到的，只有对象引用和值被比较。对象的内容不会被比较，所以即使两个对象有相同的内容，它们仍然被认为是不同的，因为它们在内存中的引用是不同的，因为它们是由两个不同的变量定义的。

## `Object.isExtensible()`

方法检查一个对象的属性是否是可扩展的。也就是说，如果一个对象可以添加新的属性。例如，如果我们有:

```
const obj = {};
const obj2 = {};console.log(Object.isExtensible(obj));Object.preventExtensions(obj2);console.log(Object.isExtensible(obj2));
```

那么`console.log(Object.isExtensible(obj));`将记录`true`，因为我们没有明确阻止向`obj`添加新属性。然而，`console.log(Object.isExtensible(obj2));`将记录`false`，因为我们调用了`Object.preventExtensions(obj2);`来防止新的属性被添加到`obj2`中。

如果我们运行下面的代码，我们也会在两个`console.log`语句中记录`false`，因为我们明确地阻止了对象`obj`和`obj2`分别使用`Object.freeze()`和`Object.seal()`方法添加新属性:

```
const obj = {};
const obj2 = {};
Object.freeze(obj);
console.log(Object.isExtensible(obj));Object.seal(obj2);
console.log(Object.isExtensible(obj2));
```

# `Object.isFrozen()`

`Object.isFrozen()`方法确定一个对象是否被冻结。冻结意味着对象中的所有属性值都不能更改。此外，不能向其添加新属性，也不能更改冻结对象的现有属性描述符。物体被冻结在原地。该方法不返回新的冻结对象。相反，它返回冻结前的原始对象。冻结对象的原型也不能改变。例如，如果我们有:

```
const obj = {
  a: 1
};console.log(Object.isFrozen(obj));
```

我们从`console.log`语句中得到`false`，因为我们没有通过调用`Object.freeze(obj)`来显式冻结对象。另一方面，我们已经冻结了对象，那么`Object.isFrozen()`将返回`true`。例如:

```
const obj = {
  a: 1
};Object.freeze(obj);
console.log(Object.isFrozen(obj));
```

然后我们从`console.log`语句中得到`true`，因为我们通过调用`Object.freeze(obj)`冻结了对象。如果原始值被传入，`Object.isFrozen()`将返回`true`，因为它们是不可变的。例如，如果我们有:

```
console.log(Object.isFrozen(1));
console.log(Object.isFrozen('string'));
console.log(Object.isFrozen(null));
console.log(Object.isFrozen(undefined));
console.log(Object.isFrozen(NaN));
console.log(Object.isFrozen(true));
console.log(Object.isFrozen(Symbol('a')));
```

那么所有的`console.log`语句都将是`true`，因为它们都是不可变的。

`Object`构造函数有更多的方法，用于从具有属性的键和值的数组中构造对象，也有方法从对象、属性名称、属性符号中获取属性描述符，获取对象的键，并防止属性被添加或删除或修改它们的属性描述符。