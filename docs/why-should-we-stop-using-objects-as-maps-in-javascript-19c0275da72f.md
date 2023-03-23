# 为什么我们应该停止在 JavaScript 中使用对象作为地图？

> 原文：<https://betterprogramming.pub/why-should-we-stop-using-objects-as-maps-in-javascript-19c0275da72f>

## 是时候使用映射而不是常规对象来存储键值数据了

![](img/e22756ba9c9f4504cec48f5b3220e815.png)

迪伦·穆林斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在 ES6 之前，为了制作字典或地图，我们经常使用对象来存储键和值。这有一些问题可以用地图来避免。

对象让我们将字符串映射到值。然而，有了 JavaScript 对象的缺陷和`Map`构造函数的存在，我们终于可以停止使用对象作为地图或字典了。

# 继承和读取属性

通常，如果没有显式设置原型，JavaScript 中的对象继承自`Object`对象。这意味着我们有对象原型中的方法。

要检查属性是在对象中还是在它的原型中，我们必须使用对象的`hasOwnProperty`。这是一种痛苦，我们很容易忘记这一点。

这意味着我们可能会意外地获取和设置实际上不在我们定义的对象中的属性。例如，如果我们定义一个空对象:

```
let obj = {}
```

然后，当我们写下:

```
'toLocaleString' in obj;
```

我们得到返回的`true`的值。这是因为`in`操作符将对象原型中的属性指定为对象的一部分，这对于字典或地图来说并不是我们真正想要的。

要创建一个没有原型的纯对象，我们必须写:

```
let obj = Object.create(null);
```

`create`方法将它创建的对象的原型对象作为参数，所以我们将得到一个不从任何原型继承的对象。像`toString`或`toLocaleString`这样的内置方法，它们是不可枚举的，所以它们不会包含在`for...in`循环中。

但是，如果我们像下面的代码那样创建一个具有可枚举属性的对象:

```
let obj = Object.create({
  a: 1
});for (const prop in obj) {
  console.log(prop);
}
```

然后，我们在上面的`for...in`中记录了`a`属性，它循环遍历一个对象的所有拥有和继承的属性。

要忽略继承的属性，我们可以使用对象的`hasOwnProperty`方法。例如，我们可以写:

```
let obj = Object.create({
  a: 1
});for (const prop in obj) {
  if (obj.hasOwnProperty(prop)) {
    console.log(prop);
  }
}
```

然后，我们不会记录任何东西。

正如我们所看到的，用属性键访问值对于常规的 JavaScript 对象来说是很棘手的。

# 覆盖属性值

使用普通对象，我们可以很容易地覆盖和删除现有的属性。任何可写属性的值都可以被覆盖或删除。

我们可以给对象中的任何属性赋值。例如，我们可以写:

```
let obj = {};
obj.toString = null;
```

然后，当我们运行时:

```
obj.toString();
```

我们得到错误`Uncaught TypeError: obj.toString is not a function`。

这是一个大问题，因为我们可以很容易地改变对象的任何原始或继承属性的值。正如我们所见，我们只用一次赋值操作就用`null`覆盖了内置的`toString`方法。

这意味着使用对象作为字典或地图是有风险的，因为我们很容易意外地这样做。除了检查可能是内置方法名称的值之外，没有其他方法可以防止这种情况。

# 对象的原型

对象的原型可以通过它的`__proto__`属性来访问。这是一种我们既能获得又能设定的属性。例如，我们可以写:

```
let obj = Object.create({
  a: 1
});obj.__proto__ = {
  b: 1
};
```

那么，我们对象的原型就是`{ b: 1 }`。这意味着我们把原来是`{ a: 1 }`的`obj`原型改成了`{ b: 1 }`，只需要设置`obj`的`__proto__`属性。

当我们像下面这样用`for...in`循环遍历`obj`对象时:

```
for (const prop in obj) {
  console.log(prop);
}
```

我们记录了`b`。

这意味着当我们试图访问我们用于字典或地图的对象时，我们必须避免访问`__proto__`属性。我们所拥有的是另一个陷阱，当使用对象作为地图或字典时，它可能会使我们陷入困境。

# 获取自己的可枚举属性以避免陷阱

为了避免获取从其他对象继承的属性的陷阱，我们可以使用`Object.keys`来获取对象自己的属性名。它返回一个包含我们定义的对象的键的数组，并忽略任何继承的属性名。

例如，如果我们有:

```
let obj = Object.create({
  a: 1
});
console.log(Object.keys(obj));
```

然后我们记录一个空数组。

类似地，`Object.entries`接受一个对象作为参数，并返回一个数组，该数组的第一个元素是键，第二个元素是键的值。

例如，如果我们写:

```
let obj = Object.create({
  a: 1
});
console.log(Object.entries(obj));
```

然后我们还会记录一个空数组。

# ES6 地图

更好的是，我们应该使用 ES6 `Map`对象，它是地图或字典的实际实现。

`Map`对象有`set`方法，让我们添加键和值，它们分别是方法接受的第一个和第二个参数。

我们可以像在下面的代码中那样定义地图:

```
let objMap = new Map();
objMap.set('foo', 'bar');
objMap.set('a', 1);
objMap.set('b', 2);
```

除了使用`set`方法添加键和值，我们还可以传递一个嵌套数组，其中数组的每个条目都将键作为第一个元素，将值作为第二个元素。

对象的一个好处是我们可以使用非字符串键。例如，我们可以写:

```
let objMap = new Map();
objMap.set(1, 'a');
```

我们也可以使用嵌套数组来定义一个`Map`。例如，代替多次使用`set`方法，我们可以编写以下代码:

```
const arrayOfKeysAndValues = [
  ['foo', 'bar'],
  ['a', 1],
  ['b', 2]
]
let objMap = new Map(arrayOfKeysAndValues);
console.log(objMap)
```

还有一些专门的方法可以通过键获取条目、获取所有条目、遍历每个条目以及删除条目。我们可以使用`get`方法通过它的键获得一个条目:

```
objMap.get('foo'); // 'bar'
```

与对象不同，我们也可以从非字符串键中获取值。例如，如果我们有:

```
let objMap = new Map();
objMap.set(true, 'a');
```

然后`console.log(objMap.get(true));`会给我们`'a'`。

我们可以用`clear`方法清除`Map`对象的所有条目。例如，我们可以写:

```
objMap.clear();
```

我们可以用`objMap.entries`方法得到所有的条目，我们也可以使用`for...of`循环遍历条目，因为它有一个迭代器。

# 结论

我们现在应该停止使用物体作为字典。有太多的陷阱，因为默认情况下对象继承自`Object`对象，当我们设置它们时继承自其他对象。

它还让我们覆盖像`toString`这样的方法的值，这不是我们大多数时候想要的结果。

为了避免这些问题，我们应该使用 ES6 中引入的`Map`对象。它有特殊的方法来获取和设置条目，我们可以用`for...of`循环遍历它们或将对象转换成数组。