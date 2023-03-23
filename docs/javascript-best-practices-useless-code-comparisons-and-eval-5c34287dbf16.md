# JavaScript 最佳实践:无用代码、比较和评估

> 原文：<https://betterprogramming.pub/javascript-best-practices-useless-code-comparisons-and-eval-5c34287dbf16>

## 让我们清理我们的代码

![](img/7288b1748ca001700c6c8ce05c1b69ff.png)

照片由[艾莉·史密斯](https://unsplash.com/@creativegangsters?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将了解一些 JavaScript 的最佳实践，包括避免空函数和析构、空比较、避免无用的`bind`和避免`eval`。

# 不要写空函数

空函数没有用，我们也不知道它们是不是故意的。这意味着我们至少应该写一个评论，让人们知道这是不是故意的。

这也适用于不返回任何内容的箭头函数回调，因为它们看起来类似于一个空对象。

例如，我们应该避免:

```
function foo() {}
arr.map(() => {});
arr.map(() => ({}));
```

相反，我们应该写:

```
function foo() {
  // do nothing
}
```

或者:

```
arr.map(() => {}); // returns nothing
```

或者:

```
arr.map(() => ({})); // returns empty object
```

# 空析构模式

空的析构模式也没有用，所以创建它们没有意义。例如，下面的内容是没有用的:

```
const {a: {}} = foo;
```

它也可能被误认为将`a`设置为空对象作为默认值，如下所示:

```
const {a = {}} = foo;
```

我们应该避免在代码中使用空的析构模式。如果有的话，我们可以也应该把它们去掉。

# 与==的空值比较

`null`与`==`或`!=`的比较也会将我们正在比较的操作数与其他值进行比较。因此，如果我们比较下面代码中的`foo`和`null`，下面也是`true`:

```
let foo = undefined;
foo == null;
```

我们得到`foo == null`返回`true`，这可能不是我们想要的。同样，如果我们对`!=`进行如下比较:

```
let foo = undefined;
foo != null;
```

我们得到`foo != null`是`false`，尽管`foo`是`undefined`。因此，我们应该使用`===`和`!==`来检查`null`，如下所示:

```
let foo = undefined;
foo === null;
```

并且:

```
let foo = undefined;
foo !== null;
```

# 永远不要使用 eval()

让我们传入一个带有 JavaScript 代码的字符串并运行它。这很危险，因为它可能会让任何人运行不是我们编写的 JavaScript 代码。它会使我们的程序面临几种注入攻击。

# 没有本地对象的扩展

在 JavaScript 中，我们可以用自己的方法扩展任何本地对象。然而，这并不是一个好主意，因为它可能会以我们意想不到的方式破坏其他使用本机对象的代码。

例如，如果我们有以下内容:

```
Object.prototype.foo = 55;const obj = {
  a: 1,
  b: 2
};for (const id in obj) {
  console.log(id);
}
```

我们看到除了`a`和`b`之外还记录了`foo`，因为`for...in`循环遍历了当前对象和对象原型链中的所有可枚举属性。

由于 JavaScript 默认扩展了 JavaScript `Object`对象，`for...in`循环也将遍历`Object`原型中的可枚举属性。

因此，我们不应该扩展本机对象，因为它可能会做一些我们不希望其他代码做的事情。

# 没有不必要的函数绑定

`bind`方法是一个函数的方法，在传统函数内部改变`this`的值。所以，如果我们的函数不引用`this`，那么我们就不需要在它上面调用`bind`来改变`this`的值。

例如，以下函数使用`bind`是有目的的:

```
function getName() {
  return this.name;
}
const name = getName.bind({
  name: "foo"
});
console.log(name());
```

在上面的代码中，我们有了`getName`函数，然后我们对它调用了`bind`来将`this`的值更改为`{ name: “foo” }`。然后当我们在最后一行中调用`name`时，我们看到返回`this.name`，它应该是`'foo'`，因为我们将`this`的值更改为对象。

另一方面，如果我们有:

```
function getName() {
  return 'foo';
}
const name = getName.bind({
  name: "foo"
});
console.log(name());
```

那么对`bind`的调用是无用的，因为`getName`没有引用`this`。所以，如果我们的函数不引用`this`，那么我们就不需要在它上面调用`bind`。

# 结论

比较`null`和`==`没有太大用处，因为像`undefined == null`这样的表达式也会因为类型强制而返回`true`。为了确保攻击者无法运行恶意代码，不应调用`eval`。

如果我们在函数中不引用`this`，那么`bind`是没有用的，所以如果我们调用它，我们应该确保我们的函数中有`this`。像空函数和析构模式这样的东西是没有用的，所以应该避免。