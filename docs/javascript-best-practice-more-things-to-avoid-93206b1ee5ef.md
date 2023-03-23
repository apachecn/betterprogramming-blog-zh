# JavaScript 最佳实践:更多需要避免的事情

> 原文：<https://betterprogramming.pub/javascript-best-practice-more-things-to-avoid-93206b1ee5ef>

## 我们来看看 for…in 循环、==操作符、arguments 对象和“this”的误用

![](img/6315caef2a86e06fadb8a2860d1309b4.png)

[Franck V.](https://unsplash.com/@franckinjapan?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/avoid?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

像任何其他编程语言一样，JavaScript 有自己的最佳实践列表，使程序更容易阅读和维护。JavaScript 有很多棘手的部分，所以有很多东西要避免。

我们可以遵循一些最佳实践来使我们的 JavaScript 代码易于阅读。

在本文中，我们将看到更多我们应该避免的构造，包括对`for...in`循环、`==`操作符、`arguments`对象和`this`的误用。

# 误用 For…in 循环

`for...in`循环不应该用于遍历数组和类似数组的对象。例如，如果我们有:

```
const arr = ['a', 'b', 'c', 'd', 'e'];
for (let i in arr) {
  console.log(i)
}
```

我们回来了:

```
0
1
2
3
4
```

然而，这是对`for...in`循环的误用，因为它应该遍历对象及其原型的可枚举属性，而不是遍历数组的索引。

枚举的顺序没有保证，所以我们可能不会按顺序遍历数组，这是不好的。

`for...in`循环还遍历可枚举的继承属性，这是另一个问题。

例如，如果我们有:

然后我们用带有`foo`属性的原型对象创建一个对象。`for...in`循环将遍历`obj`原型和`obj`的可枚举属性。

为了避免遍历其原型的属性，我们可以使用`Object.keys`来代替:

```
for (let prop of Object.keys(obj)) {
  console.log(prop);
}
```

我们可以循环遍历`obj`的键-值对，不需要任何来自其原型的东西，我们可以使用`Object.entries`:

```
for (let entry of Object.entries(obj)) {
  console.log(entry);
}
```

对于遍历数组和类似数组的对象，我们应该使用`for...of`循环:

```
const arr = ['a', 'b', 'c', 'd', 'e'];
for (let a of arr) {
  console.log(a)
}
```

然后我们得到数组的条目。

# 搞乱这个

对象一直是 JavaScript 中的一个问题。这是因为它根据范围而变化。

幸运的是，在 ES6 中，我们有箭头函数，如果我们在内部引用它，它不会改变`this`的值。这也意味着我们不能用`call`、`bind`或`apply`来改变`this`的值。

例如，下面的代码将记录`window`对象:

即使我们试图用`bind`改变`this`的值，它也不会改变。

此外，ES6 有创建构造函数的类语法，所以很明显，只要我们使用类方法和箭头函数，`this`就应该在类中。

类方法将类作为`this`的值，类中的箭头函数将具有与`this`相同的值。

![](img/f2819fd9151ceee6466631275c4344ca.png)

照片由[凯西·艾伦](https://unsplash.com/@westbeach013?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# ==运算符

在比较操作数是否相等之前，`==`运算符会自动进行类型转换。这造成了不同类型的数据被视为相同的问题。

比如`null == undefined`返回`true`，我们很可能不想要。

`null == undefined`返回`true`，因为它们都是假的。

为了避免这类问题，我们应该使用`===`操作符，这样可以避免比较返回`true`的这类操作数。这是因为`===`除了检查操作数的内容外，还检查每个操作数的类型。

# 参数对象

随着 rest 操作符的引入，我们最终可以避免使用`arguments`对象来获取传递给函数的参数。

有几个原因不应该使用`arguments`对象。一个是它是一个类似数组的对象，这意味着像`length`这样的属性在其中，它可以用一个`for`循环来循环，但是像`map`和`forEach`这样的数组方法不在其中。

此外，我们可以用它的索引来访问它的条目，这具有欺骗性，因为它实际上不是一个数组。

使用`arguments`还会阻止浏览器引擎的任何代码优化，这意味着性能会更慢。另外，`arguments`对象在箭头函数中不可用。

因此，要访问所列参数之外的额外参数，我们应该使用 rest 操作符。

例如，不要写:

我们可以写:

```
const add = (...args) => args.reduce((a, b) => a + b, 0);
console.log(add(1, 2, 3, 4, 5));
```

注意，使用 rest 操作符要短得多，它是函数签名的`...args`部分。`args`已经是一个数组，所以我们有所有可用的数组方法。

# 结论

在现代 JavaScript 中，我们可以抛弃许多旧的结构，使我们的代码更容易阅读和维护。

我们可以使用 rest 操作符来代替`arguments`对象。这是因为 rest 操作符给了我们一个参数数组，而不是类似数组的`arguments`对象。

此外，我们应该避免使用`==`操作符进行相等比较，因为它会在比较之前自动进行类型转换，这可能是我们不希望的。

我们还应该通过使用箭头函数和构造函数的类语法来避免弄乱`this`。这两个构造使得`this`的值更加清晰。我们只对类使用类方法，对不是类方法的函数使用箭头函数。

最后，`for...in`循环不应该用于遍历数组和类似数组的对象，因为枚举的顺序不能保证是有序的，这会导致意想不到的结果。