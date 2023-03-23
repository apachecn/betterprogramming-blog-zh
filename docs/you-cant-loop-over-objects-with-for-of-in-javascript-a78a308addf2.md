# 在 JavaScript 中，不能用 for-of 循环对象

> 原文：<https://betterprogramming.pub/you-cant-loop-over-objects-with-for-of-in-javascript-a78a308addf2>

## 理解对象循环和数组循环的区别

![](img/b440f3dd255de24bcf720dc97218bcd8.png)

[艾蒂安·布朗热](https://unsplash.com/@etienneblg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/loop?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

一个小问题。你不能用 for-of 循环来循环`{x: 1, y:2}`。为什么？

```
const obj = { x: 1, y: 2 };for (const key of obj) {
  // ERROR
  console.log(key);
}
```

如果你不知道确切的原因，你来对地方了！在这篇文章中，我将谈论什么是可迭代的。

# 让我们先看看你能循环什么

重要的事情先来。在 JavaScript 中，有些值可以用 for-of 循环，有些值不能循环。通常，可以对数组使用 for-of。

字符串也可以循环。

但是，这些值不能被映射。

一旦执行了产生错误的 for-of，就可以看到错误消息很奇怪。

```
Uncaught TypeError: 123 is not **iterable**
```

没有一个变量名是可迭代的，但这意味着什么呢？

# 迭代器是你真正需要的

从 ES6 开始，增加了一个新特性，`Symbol`，用于唯一值。在`Symbol`函数中，有一个你可以调用的对象，`Symbol.iterator`。这是一个为对象返回默认迭代器的函数。

当创建一个对象时，JavaScript 在内部创建一个名为`%IteratorPrototype`的对象，并将其分配给新创建的对象的迭代器对象。

例如，当这个对象被 for-of 循环使用时，JavaScript 检查它是否有 iterator 对象，该对象继承自 JavaScript 的内部核心原型`%IteratorPrototype`，用于迭代。如果它不存在，您会得到以下错误。

```
Uncaught TypeError: 123 is not **iterable**
```

也就是说，`%IteratorPrototype`在被创建的时候必须被一个对象使用，这样它才能被循环，一个对象的迭代器对象，比如`[1, 2, 3]`，被称为`Symbol.iterator`——它在规范中的内部术语是`%%iterator`，但是你不能在你的代码中使用这个名字。

如果[类型](http://ecma-international.org/ecma-262/10.0/index.html#sec-ecmascript-data-types-and-values) (O)不是`Object`，抛出`TypeError`异常。

# 符号迭代器

每个可映射对象都有一个`Symbol.iterator` —数组、字符串、类型数组、映射和集合。可以显式地使用迭代器函数对其进行循环。

当你得到迭代器对象时，它看起来像这样。

```
{
  __proto__: {
    next: *f next()*,
    Symbol(Symbol.toStringTag): "String Iterator"
  }
}
```

它有移动对象索引指针头部的`next`函数和定义当调用其`toString`时如何打印出值的`Symbol.toStringTag`方法。

如果你调用`next`，它返回这个。

```
{
  value: "h",
  done: false
}
```

`done`是显示迭代器是否访问了所有索引的属性，`value`是当前索引的值。

如果再次调用`next`，返回值如下。

```
{
  value: "e",
  done: false
}
```

现在你知道为什么`value`是`“e”`了吧。

您可以继续调用`next`并检查`value`将是“l”、“l”和“o”，因此它是“hello”。如果你再调用一次`next`，返回值会有一点不同。

```
{
  value: undefined,
  done: true
}
```

`value`现在未定义，`done`为`true`。

一旦迭代器遍历了字符串或数组或任何有`Symbol.iterator`的东西的所有索引，它一直指向索引的头现在指向空白空间。

因此，`value`将是未定义的。并且`done`变为`true`，因为头部指向空的空间。

然而，你可以继续调用`next`，它只会在`value`未定义而`done`为真时给你相同的响应。

# 使对象可迭代

现在，让我们使用`Symbol.iterator`创建对象 iterable。

```
const obj = { x: 1, y: 2 };
```

这是最初的目标。我要做的是添加`[Symbol.iterator]`函数，使其可循环。

代码看起来相当混乱，但我会向您解释这是如何工作的。

`next()`有`this`的范围，是指在`next()`之内。所以，`next()`中的`this`就不看`obj`了。

所以，首先我用`Object.entries()`获取了`obj`中的每一个条目。

```
// Object.entries(obj)
[
  0: ["x", 1],
  1: ["y", 2],
  length: 2
]
```

然后，我创建了 index 属性来循环访问`entries` — `i: 0`。

```
if (this.i >= this.entries.length) {
  return { done: true, value: undefined };
}
```

如果`i`等于或大于`entries`长度，这意味着迭代器完成了对`entries`的循环，它将从下一个迭代器调用开始返回`{ done: true, value: undefined }`。

在满足上述条件之前，返回值将为:

```
{
  done: false,
  value: {
    [this.entries[this.i][0]]: this.entries[this.i++][1]
  }
}
```

现在，数据将被成功打印。

```
const (let k of obj) {
  console.log(k);
}// { x: 1 }
// { y: 2 }
```

# 结论

决定 JavaScript 中的对象是否可循环的因素是它内部是否有`Symbol.iterator`。只有具有`Symbol.iterator`的对象才是可循环的——通常是数组。

因此，像`{ x: 1, y: 2 }`这样的对象是不可映射的，但是有一种方法可以让它可映射。你可以给它们添加一个定制的`next()`方法，我个人不推荐——太费工夫了。

# 资源

*   [符号— MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)
*   [Symbol.iterator — MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/iterator)
*   [% iterator prototype %—ECMAScript 规范](http://ecma-international.org/ecma-262/10.0/index.html#sec-%25iteratorprototype%25-object)
*   [iterable—JavaScript . info](https://javascript.info/iterable)