# JavaScript 最佳实践——要避免的事情

> 原文：<https://betterprogramming.pub/javascript-best-practice-things-to-avoid-be8e8fa30cca>

## 我们看到了各种我们应该避免的旧结构

![](img/d607445aab62f4cb1f3c124674babcfb.png)

卢卡斯·法夫尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

像任何其他编程语言一样，JavaScript 有自己的最佳实践列表，使程序更容易阅读和维护。JavaScript 有很多棘手的部分，所以有很多东西要避免。

我们可以很容易地遵循一些最佳实践，使我们的 JavaScript 代码易于阅读。

在本文中，我们着眼于要避免的事情，包括尽可能声明全局变量、将字符串传递给`setInterval`和`setTimeout`、`with`语句等等。

# 避免声明全局变量

出于各种原因，我们应该尽可能避免使用全局变量。

一个是它们很容易在不同的地方被覆盖，因为它们在任何地方都是可用的。它们也可以覆盖`window`对象中的东西，因为全局变量是`window`对象的属性。

这两个是使我们的代码难以遵循的真实问题。因此，我们应该尽可能多地定义局部变量。

我们可以通过使用`var`、`let`或`const`关键字来定义局部变量。

用`var`定义的变量在定义时可用，在定义前可用。例如，如果我们写:

```
const log = () => {
  console.log(x);
}
log();
var x = 1;
log();
```

然后我们得到第一个`console.log`的`undefined`和第二个日志的一个。

这和写作是一样的:

用`let`声明的变量只有在定义后才可用，所以如果我们给:

```
const log = () => {
  console.log(x);
}
log();
let x = 1;
log();
```

我们得到错误:

```
Uncaught ReferenceError: Cannot access ‘x’ before initialization
```

使用`const`关键字，我们定义了只能赋值一次且不能再赋值的常量。它也只有在被声明为不像`var`之后才可用。

例如，我们可以写:

```
const log = () => {
  console.log(x);
}const x = 1;
log();
```

在`const x = 1`之前调用`log`也会得到`Uncaught ReferenceError: Cannot access ‘x’ before initialization`。

如果我们希望变量在程序的不同部分都可用，我们应该使用 JavaScript 模块，然后在发布代码时将模块构建到一个或几个大文件中。这从 ES6 开始就有了。

我们可以`export`我们的变量并将它们`import`到其他模块中。还有`export default`可以导出整个模块。这样，我们只导出应该在模块外部可用的东西，其他的都是私有的。

我们还可以使用闭包将变量保存在函数内部，这样就不能在外部访问它们。简单闭包的一个例子是:

```
const divide = () => {
  const x = 3;
  return () => x * 2;
}
```

我们将`x`放在`divide`函数中，这样它就不能被外部访问，并返回一个函数来处理它。

然后我们把它写成:

```
console.log(divide()());
```

![](img/df86d84499e4e0fdf91a8d23c4d1bf4d.png)

[马特·琼斯](https://unsplash.com/@mattrobinjones?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 不要将字符串传递给 setInterval 或 setTimeout

我们不应该将字符串传递给`setInterval`或`setTimeout`函数的第一个参数，而不是一个回调函数。

如果我们向它传递一个字符串，而不是一个回调函数，浏览器将使用`eval`来运行字符串中的代码，这很慢，并且容易受到代码注入攻击。

因此，没有理由，除非我们真的需要运行动态生成的代码，这应该是非常非常罕见的。

而不是写:

```
setTimeout(
  "document.getElementById('foo').textContent = 'foo'", 1000
);
```

我们应该写:

```
setTimeout(() => {
  document.getElementById('foo').textContent = 'foo';
}, 1000);
```

# 不要使用“with”语句

表面上看，`with`语句似乎是访问对象深层嵌套属性的捷径。例如，我们可以写:

而不是:

然而，它污染了全局范围，并产生了在`with`语句之外是否可以访问`baz`的模糊性。这是因为我们也可以在`with`语句中声明新的变量:

我们在`with`内部声明了`baz`，但是在外部是不可访问的。

幸运的是，它不允许在 JavaScript 严格模式下使用，所以我们现在不必担心使用`with`语句。

# var 关键字

对于 ES6，我们有块范围的`let`和`const`关键字分别用于声明变量和常量。我们可以用它们来声明变量，因为它们有明确的作用域。

用`var`声明的变量有一个混乱的范围。当它在像`if`块或循环这样的块中声明时，它在块外是可用的。

类似于:

```
for (let i = 0; i < 10; i++){
  var j = i;
}
console.log(j);
```

与`var`一起工作，但是如果我们用`let`代替`var`，我们会得到一个错误。

因为范围混乱，我们应该停止使用`var`来声明变量。

# 结论

当我们编写 JavaScript 时，我们希望避免早期版本的 JavaScript 中存在的许多旧结构。它们使我们的代码难以阅读，很容易产生错误。

首先，我们希望避免全局变量声明，以避免在全局范围内意外引用和声明。它还减少了意外修改全局变量的值的机会。

我们也应该停止在字符串中传递代码，而不是在`setTimeout`和`setInterval`函数的第一个参数中传递回调函数。这是因为`eval`将用于运行存储在字符串中的代码，这将我们的代码暴露于代码注入攻击，并且它也很慢。

此外，我们应该避免使用`with`语句，因为它会在全局范围内创建变量。

同样，由于用`var`关键字声明的变量的范围令人困惑，我们应该避免用`var`关键字声明变量。