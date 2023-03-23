# JavaScript 中的 Map、WeakMap、Set 和 WeakSet

> 原文：<https://betterprogramming.pub/map-weakmap-set-weakset-in-javascript-77ecb5161e3>

## 每个集合对象的说明

![](img/29fb15d014aeb292bfa28a09e573f152.png)

杰夫·谢尔登在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我猜想超过 70%的 JavaScript 开发人员只在他们的项目中使用对象来收集和维护数据。事实上，更新的集合对象，如`Map`和`Set`，并没有得到充分利用，尽管它们是在 2015 年推出的。

所以今天，我要谈谈 2015 年的奇妙新功能——`Map`、`Set`、`WeakMap`和`WeakSet`。

## 阅读前

*   这篇文章不会告诉你如何使用它们。但是我见过一些申请者在编码测试中使用了其中的一个，我喜欢在某些情况下使用它们。由您决定何时在您的项目中使用它们。
*   为了更好地理解我将要谈论的内容，你应该知道什么是。查看我以前的文章，了解更多关于 t 下摆的信息。

# 目标

应该先说说如何使用对象。

好吧，我相信超过 90%的人已经知道了这一部分，因为你点击了这篇文章，以了解新的集合对象，但对于 JavaScript 的初学者，让我们简单地谈谈它们。

```
const algorithm = { site: "leetcode" };
console.log(algorithm.site); // leetcodefor (const key in algorithm) {
  console.log(key, algorithm[key]);
}
// site leetcodedelete algorithm.site;
console.log(algorithm.site); // undefined
```

所以我做了一个`algorithm`对象，它的键和值是一个字符串类型的值。我已经通过使用关键字`.`成功地检查了这个值。

此外，一个`for-in`循环对于遍历对象是很好的。您可以使用`[]`关键字访问与其键对应的值。但是不能使用`for-of`循环，因为对象是不可迭代的。

对象的属性可以用`delete`关键字删除。这完全从对象中去除了属性，您应该小心不要与这种方法混淆。

```
const algorithm = { site: "leetcode" };// Property is not removed!!
algorithm.site = undefined;// Property is removed!!
delete algorithm.site;
```

`algorithm.site = undefined`只是将新值分配给`site`。

好了，我们已经快速讨论了一些关于对象的事情:

*   如何添加属性
*   如何在对象上循环
*   如何删除属性

# 地图

一个`Map`是 JavaScript 中一个新的集合对象，其功能类似于一个对象。但是与常规对象相比，还是有一些主要的区别。

首先，让我们看一个创建`Map`对象的简单例子。

## 如何添加属性

```
const map = new Map();
// Map(0) {}
```

就是这样。`Map`不需要创造任何东西。但是添加数据的方式略有不同。

```
map.set('name', 'john');
// Map(1) {"name" => "john"}
```

`Map`有一个特殊的添加属性的方法叫做`set`。它有两个参数:键作为第一个参数，值作为第二个参数。

```
map.set('phone', 'iPhone');
// Map(2) {"name" => "john", "phone" => "iPhone"}map.set('phone', 'iPhone');
// Map(2) {"name" => "john", "phone" => "iPhone"}
```

但是，它不允许您在其中添加现有数据。如果新数据的关键字对应的值已经存在于`Map`对象中，则不会添加新数据。

```
map.set('phone', 'Galaxy');
// Map(2) {"name" => "john", "phone" => "Galaxy"}
```

但是您可以用不同的值覆盖现有数据。

## 如何在对象上循环

`Map`是一个 iterable 对象，这意味着它可以用`for-of`语句映射。

```
for (const item of map) {
  console.dir(item);
}// Array(2) ["name", "john"]
// Array(2) ["phone", "Galaxy"]
```

需要记住的一点是`Map`以数组的形式给出数据。您应该析构数组或访问每个索引来获取键或值。

要仅获取键或值，您也可以遵循一些方法。

```
map.keys();
// MapIterator {"name", "phone"}map.values();
// MapIterator {"john", "Galaxy"}map.entries();
// MapIterator {"name" => "john", "phone" => "Galaxy"}
```

您甚至可以使用 spread 操作符来获得`Map`的完整数据，因为 spread 操作符也可以处理场景背后的 iterable 对象。

```
const simpleSpreadedMap = [...map];
// [Array(2), Array(2)]
```

## 如何删除属性

从`Map`对象中移除数据也非常容易。你需要做的就是调用`delete`。

```
map.delete('phone');
// truemap.delete('fake');
// false
```

`delete`返回布尔值，表示删除功能是否成功删除数据。如果是，则返回`true`，否则返回`false`。

# WeakMap

`WeakMap`源于`Map`，所以彼此很像。然而，`WeakMap`有一个很大的不同。

`WeakMap`是如何得名的？嗯，这是因为它与它的引用链接所引用的数据对象的连接或关系不如`Map`的连接或关系强，使得*弱。*

那么这到底意味着什么呢？

## 区别 1:键必须是一个对象

```
const John = { name: 'John' };
const weakMap = new WeakMap();weakMap.set(John, 'student');
// WeakMap {{...} => "student"}weakMap.set('john', 'student');
// Uncaught TypeError: Invalid value used as weak map key
```

您可以将任何值作为键传递给`Map`对象，但是`WeakMap`不同。它只接受一个对象作为键；否则，它将返回一个错误。

## 区别 2:并非所有来自 Map 的方法都受支持

您可以使用`WeakMap`的方法如下。

*   `delete`
*   `get`
*   `has`
*   `set`

这个主题中最大的不同是`WeakMap`不支持迭代对象的方法。但是为什么呢？下面有描述。

## 区别 3:当 GC 清理引用时，数据被删除

这是相比`Map`最大的不同。

```
let John = { major: "math" };const map = new Map();
const weakMap = new WeakMap();map.set(John, 'John');
weakMap.set(John, 'John');John = null;
/* John is garbage-collected */
```

当`John`对象被垃圾回收时，`Map`对象保持引用链接，而`WeakMap`对象失去链接。所以你在使用`WeakMap`的时候，要考虑这个特性。

# 一组

`Set`也和`Map`很像，但是`Set`对于单个值更有用。

## 如何添加属性

```
const set = new Set();set.add(1);
set.add('john');
set.add(BigInt(10));
// Set(4) {1, "john", 10n}
```

和`Map`一样，`Set`也阻止我们添加相同的值。

```
set.add(5);
// Set(1) {5}set.add(5);
// Set(1) {5}
```

## 如何在对象上循环

因为`Set`是一个可迭代的对象，你可以使用一个`for-of`或者`forEach`语句。

```
for (const val of set) {
  console.dir(val);
}
// 1
// 'John'
// 10n
// 5set.forEach(val => console.dir(val));
// 1
// 'John'
// 10n
// 5
```

## 如何删除属性

这部分和`Map`的删除完全一样。如果数据删除成功，返回`true`；否则，它返回`false`。

```
set.delete(5); 
// trueset.delete(function(){});
// false;
```

`Set`当你不想把相同的值添加到数组形式中时，可能会很有用。

```
/* With Set */
const set = new Set();set.add(1);
set.add(2);
set.add(2);
set.add(3);
set.add(3);
// Set {1, 2, 3}// Converting to Array
const arr = [ ...set ];
// [1, 2, 3]Object.prototype.toString.call(arr);
// [object Array]/* Without Set */
const hasSameVal = val => ar.some(v === val);
const ar = [];if (!hasSameVal(1)) ar.push(1);
if (!hasSameVal(2)) ar.push(2);
if (!hasSameVal(3)) ar.push(3);
```

# WeakSet

和`WeakMap`一样，`WeakSet`如果被垃圾收集，也会失去对内部数据的访问链接。

```
let John = { major: "math" };const set = new Set();
const weakSet = new WeakSet();set.add(John);
// Set {{...}}
weakSet.add(John);
// WeakSet {{...}}John = null;
/* John is garbage-collected */
```

一旦对象`John`被垃圾收集，`WeakSet`就无法访问引用`John`的数据。并且`WeakSet`不支持`for-of`或`forEach`，因为它不是可迭代的。

# 比较摘要

## 全部

*   不支持添加相同的值

## 地图与 WeakMap

*   `WeakMap`只接受对象作为键，而`Map`不接受

## 地图和布景

*   支持可迭代对象— `for-of`、`forEach`或`spread`运算符
*   不受 GC 关系约束

## WeakMap 和 WeakSet

*   不是可迭代对象—不能循环
*   如果引用数据被垃圾收集，则无法访问数据
*   支持性较差的方法

# 结论

尽管如此，我认为许多团队或公司并不使用`Map` s 或`Set` s。也许是因为他们觉得没有必要，或者是因为数组仍然可以做几乎所有他们想做的事情。

然而，根据不同的情况，`Map` s 或`Set` s 可能是 JavaScript 中非常独特和强大的东西。所以我希望有一天，你能有机会使用它们。

## 资源

*   [地图— MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)
*   [WeakMap — MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)
*   [设置— MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)
*   [WeakSet — MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakSet)