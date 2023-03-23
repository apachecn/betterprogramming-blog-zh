# 用 JavaScript 映射存储键值对

> 原文：<https://betterprogramming.pub/storing-key-value-pairs-with-javascript-maps-52fb713bb096>

## 地图和物体有什么区别？

![](img/d8ed5ebdb5503df580160b8267860eae.png)

Andrew Stutesman 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 ES2015 中，我们有了一种新的数据结构来存储键-值对，称为映射，这是一种字典，我们可以使用它来存储任何对象作为键和任何对象作为值。

在我们有地图之前，我们必须使用对象作为字典，字典只使用字符串作为键，但是值可以是任何东西。

使用地图很简单，我们可以用下面的代码定义地图:

```
let messageMap = new Map();
messageMap.set('hi', 'Hello');
messageMap.set('greeting', 'How are you doing?');
messageMap.set('bye', 'Bye');
```

除了使用`set`方法添加键和值，我们还可以传递一个嵌套数组，其中数组的每个条目都将键作为第一个元素，将值作为第二个元素。

例如，我们可以写:

```
const arrayOfKeysAndValues = [
  ['a', 1],
  ['b', 2],
  ['c', 3],
  ['d', 3],
  ['e', 4],
  ['f', 5],
  ['g', 6],
  ['h', 7],
  ['i', 8],
  ['j', 9],
  ['k', 10],
  ['l', 11],
  ['m', 12],
  ['n', 13],
  ['o', 14],
]
let numMap = new Map(arrayOfKeysAndValues);
console.log(numMap)
```

上面的代码将创建一个映射，其中每个数组的第一个元素作为键，每个数组的第二个元素作为相应的值。因此，在运行了`console.log`行之后，我们得到了与数组顺序相同的所有 Map 条目。

我们可以通过使用`size`属性来获得映射中定义的键值对的数量:

```
messageMap.size // 3
```

为了通过键获得值，我们使用了`get`函数:

```
messageMap.get('hi'); // 'Hello'
messageMap.get('hello'); // undefined
```

如果该键存在，它将返回值；如果带有该键的条目不存在，它将返回`undefined`。

要删除给定了键的条目，我们可以使用`delete`函数传递键:

```
messageMap.delete('hi');
```

如果我们多次使用同一个键调用`set`，那么后面的`set`调用中的值会覆盖前面的值。例如:

```
let messageMap = new Map();
messageMap.set('hi', 'Hello');
messageMap.set('greeting', 'How are you doing?');
messageMap.set('bye', 'Bye');
messageMap.set('hi', 'Hi');console.log(messageMap.get('hi'))
```

如果我们运行`console.log(messageMap.get(‘hi’))`，我们得到的是`'Hi'`而不是`'Hello'`。

要清除地图对象的所有条目，我们可以调用`clear`函数，如下面的代码所示:

```
let messageMap = new Map();
messageMap.set('hi', 'Hello');
messageMap.set('greeting', 'How are you doing?');
messageMap.set('bye', 'Bye');
messageMap.set('hi', 'Hi');
messageMap.clear();
console.log(messageMap)
console.log(messageMap.size)
```

我们看到当我们记录它时`messageMap`应该是空的，并且它的`size`应该是 0。

可以用一个`for...of`循环迭代映射的条目。通过每个条目的析构赋值，我们可以使用类似于下面代码中的`for...of`循环获得每个条目的键和值:

```
let messageMap = new Map();
messageMap.set('hi', 'Hello');
messageMap.set('greeting', 'How are you doing?');
messageMap.set('bye', 'Bye');for (let [key, value] of messageMap) {
  console.log(`${key} - ${value}`);
}
```

对象和贴图在很多方面都是不同的。对象的键只能是字符串类型。在地图中，关键字可以是任何值。

我们可以通过 objects 中没有的属性`size`轻松获得地图的大小。贴图的迭代顺序是元素的迭代顺序，而对象的键的迭代是不确定的。

对象有原型，所以对象中有默认键，而映射中没有。

如果我们希望存储非字符串键，当运行时才知道这些键时，以及当所有键都是同一类型并且值也是同一类型(可能与键是同一类型，也可能不是同一类型)时，最好使用映射。

我们可以使用`entries`函数来获取地图的条目。例如，我们可以写:

```
let messageMap = new Map();
messageMap.set('hi', 'Hello');
messageMap.set('greeting', 'How are you doing?');
messageMap.set('bye', 'Bye');for (let [key, value] of messageMap.entries()) {
  console.log(`${key} - ${value}`);
}
```

循环遍历地图的条目。

除了`NaN`被认为与自身相等以及+0 和-0 也被认为相等之外，键的相等性检查主要遵循三重等于运算符的规则。该算法被称为同值零算法。

所以，如果我们有:

```
const map = new Map();
map.set(NaN, 'abc');
```

然后我们运行`map.get(NaN)`，我们得到返回的`'abc'`。另外，需要注意的是，我们可以像上面一样使用非字符串值作为键。例如:

```
let messageMap = new Map();
messageMap.set(1, 'Hello');
messageMap.set(2, 'How are you doing?');
messageMap.set(3, 'Bye');
```

如果我们调用`messageMap.get(1)`，我们得到`'Hello'`。

然而，由于键是通过检查三重等于操作符来检索的，而`NaN`被认为等于自身，所以我们不能直接从对象键中获得您期望的值。

```
let messageMap = new Map();
messageMap.set({ messageType: 'hi' }, 'Hello');
messageMap.set({ messageType: 'greeting' }, 'How are you doing?');
messageMap.set({ messageType: 'bye' }, 'Bye');
const hi = messageMap.get({
  messageType: 'hi'
})console.log(hi);
```

`hi`将为`undefined`，因为它不检查对象的内容是否相等。

如果我们先将 Map 转换为数组，然后再将其转换回 Map，那么我们可以在 Map 上使用数组方法。例如，如果我们要将地图中的所有值乘以 2，那么我们可以编写以下内容:

```
let numMap = new Map();
numMap.set('a', 1);
numMap.set('b', 2);
numMap.set('c', 3);let multipliedBy2Array = [...numMap].map(([key, value]) => ([key, value * 2]));let newMap = new Map(multipliedBy2Array);console.log(newMap)
```

从上面的代码中我们可以看到，我们可以使用 spread 操作符，通过将值从映射复制到数组，直接将映射转换为数组。

每个条目由一个数组组成，其中键作为第一个元素，值作为第二个元素。然后我们可以对它调用`map`,因为它是一个数组。然后，我们可以将每个值的条目乘以 2，同时保持每个条目的数组结构相同，首先是键，其次是值。

然后我们可以将它直接传递给`Map`对象的构造函数，我们可以看到新的 Map 对象`newMap`的所有值都乘以 2，同时保持与`numMap`相同的键。

同样，我们可以组合多个 Map，首先用 spread 操作符将它们放在同一个数组中，然后将它们组合成一个，然后将它传递给 Map 构造函数以生成一个新的 Map 对象。

例如，我们可以写:

```
let numMap1 = new Map();
numMap1.set('a', 1);
numMap1.set('b', 2);
numMap1.set('c', 3);
numMap1.set('d', 3);
numMap1.set('e', 4);
numMap1.set('f', 5);
numMap1.set('g', 6);
numMap1.set('h', 7);
numMap1.set('i', 8);
numMap1.set('j', 9);
numMap1.set('k', 10);
numMap1.set('l', 11);let numMap2 = new Map();
numMap2.set('m', 1);
numMap2.set('n', 2);
numMap2.set('o', 3);
numMap2.set('p', 3);
numMap2.set('q', 4);
numMap2.set('r', 5);
numMap2.set('s', 6);
numMap2.set('t', 7);
numMap2.set('u', 8);
numMap2.set('v', 9);
numMap2.set('w', 10);
numMap2.set('x', 11);let numMap3 = new Map();
numMap3.set('y', 1);
numMap3.set('z', 2);
numMap3.set('foo', 3);
numMap3.set('bar', 3);
numMap3.set('baz', 4);const combinedArray = [...numMap1, ...numMap2, ...numMap3];
const combinedNumMap = new Map(combinedArray);
```

当我们记录`combinedNumMap`时，我们看到所有原始的键和值都完好无损，但是它们被组合到一个映射中。

如果我们有重叠的键，那么后面插入的键会覆盖前面插入的值。例如:

```
let numMap1 = new Map();
numMap1.set('a', 1);
numMap1.set('b', 2);
numMap1.set('c', 3);
numMap1.set('d', 3);
numMap1.set('e', 4);
numMap1.set('f', 5);
numMap1.set('g', 6);
numMap1.set('h', 7);
numMap1.set('i', 8);
numMap1.set('j', 9);
numMap1.set('k', 10);
numMap1.set('l', 11);let numMap2 = new Map();
numMap2.set('m', 1);
numMap2.set('n', 2);
numMap2.set('o', 3);
numMap2.set('p', 3);
numMap2.set('q', 4);
numMap2.set('r', 5);
numMap2.set('s', 6);
numMap2.set('t', 7);
numMap2.set('u', 8);
numMap2.set('v', 9);
numMap2.set('w', 10);
numMap2.set('x', 11);let numMap3 = new Map();
numMap3.set('y', 1);
numMap3.set('z', 2);
numMap3.set('a', 21);
numMap3.set('b', 22);
numMap3.set('c', 23);const combinedArray = [...numMap1, ...numMap2, ...numMap3];
const combinedNumMap = new Map(combinedArray);
```

当我们记录`combinedNumMap`时，我们看到大部分原始的键和值都完好无损，但是它们被组合成了一个映射。但是，`a`现在映射到 21，`b`现在映射到 22，`c`现在映射到 23。

有了映射，我们可以拥有键-值对，其中的键不是字符串。映射是可以按照插入键值对的顺序进行迭代的集合。

它们可以转换为数组，每个条目都是一个数组，第一个元素是键，第二个元素是值，这意味着我们可以将它们转换为数组，然后像数组方法一样使用 arras 操作。

我们可以使用 spread 操作符像处理任何数组一样处理它们，然后再将它们转换回地图。我们可以使用`set`函数添加更多的条目，使用`get`函数获取给定键的值。

这些值由关键字通过相同值为零算法检索，这意味着关键字由三重等于运算符匹配，除了-0 和+0 被视为相等，而`NaN`被视为等于自身。