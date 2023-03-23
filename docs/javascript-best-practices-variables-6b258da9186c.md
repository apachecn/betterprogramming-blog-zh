# JavaScript 最佳实践—变量

> 原文：<https://betterprogramming.pub/javascript-best-practices-variables-6b258da9186c>

## 我们看看如何以一种易读的方式声明变量

![](img/b75caf085fb4bae36ea00bb5c8fb9f03.png)

照片由[雷·轩尼诗](https://unsplash.com/@rayhennessy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何其他编程语言一样，JavaScript 有自己的最佳实践列表，使程序更容易阅读和维护。JavaScript 有很多棘手的部分，所以有很多东西要避免。

我们可以遵循一些最佳实践来使我们的 JavaScript 代码易于阅读。

在本文中，我们看看如何以一种易读的方式声明变量。此外，我们还研究了避免声明全局变量和对外部隐藏私有变量的方法。

# 避免全局变量

出于各种原因，我们应该尽可能避免使用全局变量。

一个是它们很容易在不同的地方被覆盖，因为它们在任何地方都是可用的。它们也可以覆盖`window`对象中的内容，因为全局变量是`window`对象的属性。

这两个是使我们的代码难以遵循的真实问题。因此，我们应该尽可能多地定义局部变量。我们可以通过使用`var`、`let`或`const`关键字来定义局部变量。

用`var`定义的变量在定义时可用，在定义前可用。例如，如果我们写:

然后我们得到第一个`console.log`的`undefined`和第二个日志的一个。

这和写作是一样的:

用`let`声明的变量只有在定义后才可用，所以如果我们给:

我们得到错误:

```
Uncaught ReferenceError: Cannot access ‘x’ before initialization
```

使用`const`关键字，我们定义了只能赋值一次且不能再赋值的常量。也是声明后才可用，不像`var`。

例如，我们可以写:

```
const log = () => {
  console.log(x);
}const x = 1;
log();
```

在`const x = 1`之前调用`log`也会使我们:

```
Uncaught ReferenceError: Cannot access ‘x’ before initialization
```

如果我们希望变量在程序的不同部分都可用，我们应该使用 JavaScript 模块，然后在发布代码时将模块构建到一个或几个大文件中。这是从 ES6 开始提供的。

我们可以在其他模块中`export`我们的变量和`import`它。还有`export default`可以导出整个模块。这样，我们只导出应该在模块外部可用的东西，其他的都是私有的。

我们还可以使用闭包将变量保存在函数内部，这样就不能在外部访问它们。简单闭包的一个例子是:

```
const multiply = () => {
  const x = 3;
  return () => x * 2;
}
```

我们将`x`放在`multiply`函数中，这样它就不能被外部访问，并返回一个函数来处理它。

然后，我们把它写成:

```
console.log(multiply()());
```

# 总是声明局部变量

作为推论，我们应该总是声明局部变量。我们应该总是用`var`、`let`或`const`来声明局部变量和常量。

否则，它将被声明为一个全局变量，作为`window`的属性，这是我们绝对不想要的。

例如，我们不应该写:

```
x = 1;
```

相反，我们应该写:

```
let x = 1;
```

幸运的是，JavaScript 严格模式不允许未声明的变量，所以我们不能意外地创建全局变量。

此外，JavaScript 模块默认启用了严格模式，所以我们也不能在那里意外地定义全局变量。

![](img/717160786224a604bb6012e15319f091.png)

弗朗西斯科·德·托马索在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 将变量和常量声明放在最上面

将变量和常量声明放在顶部使我们的代码更整洁，因为一切都在顶部。它还防止我们在定义之前意外引用用`let`或`const`声明的东西。

如果关闭了严格模式，我们也避免定义全局变量。我们也避免了意外的变量重声明。重声明应该被许多文本编辑器捕捉到，但它仍然是一种可能性。

通过用逗号分隔变量名，我们可以在一行中声明多个变量。例如，我们可以写:

```
let x, y;
x = 1;
y = 1;
console.log(x, y);
```

然后我们得到:

```
1 1 
```

从`console.log`开始。

我们可以对循环做同样的事情:

```
let i;for (i = 0; i < 10; i++) {
  console.log(i);
}
```

# 声明变量时初始化变量

在声明变量或常量时，用值初始化它们是一个好主意。它可以防止未定义的值错误，我们可以在一行中声明变量或常量，并为其设置初始值。

否则，我们用一行声明变量或常量，另一行设置它的值。

例如，我们可以写:

```
let x = 1,
  y = 1;
```

同时声明`x`和`y`。

这比写作要短得多:

```
let x;
let y;
x = 1;
y = 1;
```

我们可以通过用逗号分隔每个声明和赋值来声明和初始化多个变量。

# 结论

以下是声明和初始化变量的一些基本最佳实践。

我们研究了如何干净地声明变量，以及如何避免声明全局变量。编写干净的 JavaScript 程序还有很多内容，请继续关注。