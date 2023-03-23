# 常见的 JavaScript 错误—第 3 部分

> 原文：<https://betterprogramming.pub/common-javascript-mistakes-part-3-6756605f40b1>

## 重载函数、缺少参数等等

![](img/dbf20bdd9f7c021154eced57fa2f3e78.png)

照片由[乔希·考奇](https://unsplash.com/@joshcouchdesign?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

JavaScript 是一种比世界上许多其他编程语言更友好的语言。然而，在编写 JavaScript 代码时，由于误解或忽略我们已经知道的东西，仍然很容易犯错误。通过避免下面的一些错误，我们可以通过防止代码中的错误和错别字来使我们的生活变得更容易，这些错误和错别字会使我们陷入意想不到的结果。

# 试图重载函数

函数重载是一些编程语言的一个特性，在这里你可以用相同的名字但是不同的签名来声明函数。在 JavaScript 中，我们不能重载函数。每当一个函数被声明不止一次时，后面声明的函数会覆盖前面声明的函数。这是因为函数是对象，声明一个函数就像将一个对象赋给一个变量。当你将一个对象多次赋给一个变量时，后赋的值会覆盖前赋的值。这意味着在 JavaScript 的同一个模块中不能有两个同名的函数。例如，如果我们有以下内容，

```
function add(a, b, c) {
  return a + b + c;
}function add(a, b) {
  return a + b;
}console.log(add(1, 2, 3));
```

然后我们得到三个，因为后来声明的`add`函数覆盖了之前声明的函数。要解决这个问题，我们必须重命名其中一个。我们也可以把它们放在两个不同的物体里。那么他们可以有相同的名字，因为他们不在同一个级别。另外，我们可以写一个直接调用的函数表达式，简称 IIFE。生命一旦被定义就开始运行。要将它们包装在一个对象中，我们可以编写以下代码:

```
const a = {
  add(a, b, c) {
    return a + b + c;
  }
}const b = {
  add(a, b) {
    return a + b;
  }
}console.log(a.add(1, 2, 3));
console.log(b.add(1, 2, 3));
```

正如我们所见，如果我们运行代码，那么第一个的`console.log`将是 6，第二个将是 3，因为`a.add`有 3 个参数而`b.add`有 2 个。

我们也可以像下面的例子一样使用 IIFE:

```
const sum1 = (function add(a, b, c) {
  return a + b + c;
})(1, 2, 3);const sum2 = (function add(a, b) {
  return a + b;
})(1, 2, 3);console.log(sum1);
console.log(sum2);
```

在上面的代码中，我们将函数包装在括号内，然后在定义后立即调用它。然后我们将返回的结果赋给一个变量。在那之后，我们得到了我们想要的 6 和 3。因为我们立即调用了每个函数并返回了结果，所以我们得到了正确的结果，因为它们没有重叠。这也意味着它们不能被再次调用。

# 缺少参数

当我们给函数添加一个新参数时，我们必须记住在函数调用中传递额外的参数。否则可能会出现`undefined`错误。为了避免未定义的参数产生错误，我们可以在函数中检查它，或者设置参数的默认值。例如，如果我们有以下函数

```
function addressFn(address, city, region) { ... }
```

我们想添加一个`country`参数，我们程序的其他部分调用了上面的函数，然后我们可以添加一个默认参数。我们可以通过编写以下代码来做到这一点:

```
function addressFn(address, city, region, country = 'US') { ... }
```

这样，如果`country`参数没有被传入，`country`将被设置为`'US'`。

![](img/7a678ff73d48bc7f431d75978604f99d.png)

由[肯德尔巷](https://unsplash.com/@kendall3lane?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 忘记了 this 关键字

当我们试图从一个对象中的另一个属性访问某个属性时，我们应该使用`this`关键字来获取我们想要的属性值。例如，如果我们有以下内容，

```
let obj = {
  prop: "some text",
  method() {
    console.log(prop);
  }
};obj.method();
```

当我们运行上面的代码时，我们将得到一个*未捕获的引用错误:属性未定义*错误。这是因为我们忘记了将`this`关键字放在 prop 变量之前。相反，我们需要编写以下内容:

```
let obj = {
  prop: "some text",
  method() {
    console.log(this.prop);
  }
};obj.method();
```

当我们运行上面的代码时，我们得到了`'some text'`，这就是我们想要的。

# 遍历对象键

`for...in`循环将遍历当前对象的关键点以及所有原型的关键点。这并不适合所有情况。它也比其他遍历对象键的方法慢。使用`for...in`循环，我们需要使用`Object.hasOwnProperty`函数来检查属性最初是在对象中定义的。这使得循环更加缓慢。如果我们有一个有很多属性的大对象，这是一个问题。例如，如果我们有，

```
const parent = {
  pa: 1,
  pb: 2
}
let obj = Object.create(parent);obj.a = 1;
obj.b = 2;
obj.c = 3;
obj.d = 4;
obj.e = 5;
```

然后`for...in`循环将遍历`parent`的所有属性和添加到`obj`的属性。如果我们只想遍历`obj`中的属性，那么我们必须使用`hasOwnProperty`函数进行循环，如以下代码所示:

```
for (const key in obj) {
  if (obj.hasOwnProperty(key)) {
    console.log(obj[key]);
  }
}
```

然而，这比新的替代方法要慢，新的替代方法是用`Object.keys`获取对象的键，用`Object.entries`获取对象的键值对。然后我们用`for...of`循环遍历它们，因为它们都返回数组。它们只遍历对象的属性，不遍历原型链。遍历条目的最快方法是这两个函数。我们可以如下使用它们:

```
const parent = {
  pa: 1,
  pb: 2
}
let obj = Object.create(parent);obj.a = 1;
obj.b = 2;
obj.c = 3;
obj.d = 4;
obj.e = 5;for (const key of Object.keys(obj)) {
  console.log(obj[key]);
}for (const [key, value] of Object.entries(obj)) {
  console.log(value);
}
```

在每个示例中，我们都记录了以下内容，

```
a 1
b 2
c 3
d 4
e 5
```

这意味着我们从`Object.keys`和`Object.entries`函数中得到了我们想要的东西。

即使 JavaScript 是一种友好的语言，在编写 JavaScript 代码时仍然很容易出错。记住，在 JavaScript 中，我们不能重载函数，所以我们不能在同一个级别中定义两个同名的函数。如果某个函数参数有可能没有被设置，那么我们可以设置一个默认参数，这样它就永远不会是`undefined`。此外，当我们从同一个对象的另一个属性访问一个属性时，我们不能忘记`this`对象。最后，我们不应该再使用`for...in`循环来遍历一个对象的键，因为如果我们只是想遍历当前对象的键而不遍历其原型的键，那么它会很慢而且笨拙。我们想使用`Object.keys`或`Object.entries`函数来代替，这样我们就可以分别获得作为数组的键或键值对，并且我们可以像其他数组一样遍历它们。