# TypeScript 的严格模式如何实际修复 TypeScript

> 原文：<https://betterprogramming.pub/how-typescripts-strict-mode-actually-fixes-typescript-736ba8108c85>

## 默认情况下没有理由不打开它

![](img/18e4cd6df97f95f7ab0966f55a06fd9f.png)

[Clem Onojeghuo](https://unsplash.com/@clemono2?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

严格模式实际上是其他六个标志的组合(从 TypeScript 3.8 开始):

*   `noImplicitAny`
*   `strictNullChecks`(从 2.0 开始)
*   `noImplicitThis`(从 2.0 开始)
*   `strictFunctionTypes`(从 2.6 开始)
*   `strictPropertyInitialization`(从 2.7 开始)
*   `strictBindCallApply`(从 3.2 开始)

每个选项都可以单独启用或禁用。其中一些使 TypeScript 的类型检查更好，一些有助于使您的代码可读性更好，更不容易出错。

您可以在您的`tsconfig.json`中启用严格模式:

您也可以在`compileOptions`中禁用任何您不喜欢的严格系列选项(例如`"noImplicitAny": false`)。

我认为最重要的旗帜是`noImplicitAny`和`strictNullChecks`。这两点将真正提高代码的类型检查和可读性。

让我们来看看每面旗。

# 无 ImplicitAny

没有打开`noImplicitAny`，你只是*主要是*使用类型脚本，因为现在你甚至没有注意到你的部分代码是类型`any`的。

因为 type `any`基本上禁用了类型检查，所以除非别无选择，否则真的不应该有。问题是在你的代码中很容易错误地使用`any`，所以通过使用`noImplicitAny`，你现在只有在你明确使用`any`的地方才会有它。

让我们看看错误地禁用类型检查是多么容易:

在这个例子中，我们定义了一个函数(`fn`)，它只返回它得到的结果。我们很容易忽略的是，这个函数的返回类型其实是`any`。你可以看到和`num`一样的`copyOfNum`，现在是`any`类型，我们可以用它做各种错误的事情。

如果我们打开`noImplicitAny`，我们将不能像`fn`那样定义一个隐式返回`any`的函数，而必须正确地定义它，导致`copyOfNum`不会丢失它的类型:

另一种我们在没有注意到的情况下将`any`放入代码的常见方式是在导入外部模块时:

在这里，我们只是想使用 lodash 添加 1 + 1，但是得到了类型为`any`的结果，因为我们没有安装 lodash 的类型。这可以通过安装`@types/lodash`来解决。如果我们使用没有可用类型的外部模块，我们可以使用`declare module ‘lodash’ { /* types goes here */}`自己编写它们。

如果我们同意这个模块是类型`any`或者现在没有能力修复它，我们可以通过声明这个模块没有类型来通知编译器:

```
declare module ‘lodash’;
```

# strictNullChecks

最常见的 JS 运行时错误可能是`Uncaught TypeError: Cannot read property ‘foo’ of undefined.` 这是在试图访问一个属性或调用一个`undefined`对象上的方法时引起的。

幸运的是，TypeScript 可以提供帮助。

默认情况下，`null`和`undefined`可赋给所有其他类型，而您的代码实际上有许多类型永远不可能是`null`或`undefined`，这使得它们的类型在默认情况下是错误的。

启用`strictNullChecks`后，只有明确标记为`null`或`undefined`的类型。这意味着你不能初始化一个没有值的变量。例如:

你从这些额外的工作中得到了什么？现在您的类型更加具体了，TypeScript 将能够分析您的代码流并找到潜在的错误:

有时你不得不退出这个功能。也许某些外在类型是错误的，但是你没有办法修复或者你想逐步引入这个标志。您可以随时使用`!`符号禁用`strictNullChecks`。
任何带有`!`符号的属性访问在该检查中将被忽略:

只有启用了`strictNullChecks`，你才能启用下一面旗帜。

# **严格属性初始化**

使用`strictPropertyInitialization`，TypeScript 将抛出一个错误，除非所有的类属性都在构造函数中或由属性初始化器初始化。这有助于防止无意中访问代码中的`undefined`属性:

有时你不能在类创建时初始化。例如，您可能使用外部服务来获取数据。在这些情况下，您可以使用`!`符号忽略`strictPropertyInitialization`，就像您使用`strictNullChecks`符号一样:

# strictFunctionTypes

在 TypeScript 中，[参数类型是双变量的](https://github.com/Microsoft/TypeScript/wiki/FAQ#why-are-function-parameters-bivariant)(既有协变的也有逆变的)，这是不合理的(虽然你现在可以在 [TypeScript 2.6](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-6.html) 中用`--strictFunctionTypes`或`--strict`修复这一点)。

让我们看一个简单的例子来理解它的意思:

在这个例子中，我们用另一个函数调用`forEach`，这个函数得到一个`HTMLElement`，即使`querySelectorAll`返回一个`Element`的列表(这个列表是`HTMLElement`扩展的)。您可以看到我们访问了元素的`offsetHeight`，这是一个不存在于`Element`而只存在于`HTMLElement`的属性。

如果没有`strictFunctionTypes`，这段代码可以编译，但是它的类型是错误的，会导致潜在的错误。如果我们打开`strictFunctionTypes`，我们将得到一个错误:

```
Type ‘Element’ is missing the following properties from type ‘HTMLElement’ …
```

这基本上意味着我们不能决定`element`是`HTMLElement`而不是`Element`,因为那样我们就可以使用只有`HTMLElement`在我们函数内部才有的属性，这是错误的。

# strictBindCallApply

使用`strictBindCallApply`标志，`bind`、`call`和`apply`方法是强类型的。如果没有这个标志，您可能会错误地使用这三个函数，其参数与使用它们的函数不匹配:

# 不影响这个

这与`noImplicitAny`的作用相同，但对于`this`类型。如果你有一个使用了`this`的函数，但是编译器无法从代码中推断出`this`的类型是什么，那么你就会出错。这有助于在编译时而不是运行时捕获 bug。例如:

在这个例子中，我们得到一个运行时错误:

```
Uncaught TypeError: Cannot read property ‘x’ of undefined.
```

我们得到一个错误，因为我们用错误的`this`参数调用了`isEqual`(当我们将函数赋给第 11 行的另一个变量时，我们丢失了`this`绑定)。

如果我们打开了`noImplicitThis`标志，而不是运行时错误，我们将在第 6 行得到一个编译时错误:`‘this’ implicitly has type ‘any’`。我们可以通过显式编写`this`类型来修复这个错误，这将迫使我们修复这个错误:

# 结论

我希望您现在理解了严格模式，并且您应该在您的代码中使用它，因为好处大于代价——至少对于不需要迁移的新项目来说是这样。

对于现有项目，考虑一次添加一个严格标志，使迁移更加渐进。