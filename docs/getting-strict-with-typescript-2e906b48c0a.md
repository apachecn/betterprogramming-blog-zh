# 对类型脚本越来越严格

> 原文：<https://betterprogramming.pub/getting-strict-with-typescript-2e906b48c0a>

## 使您的类型脚本代码更具可读性和类型安全

![](img/ab799de3727cc3445eb7f464c5b30aab.png)

[Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

正如大多数人所知，TypeScript 是微软在 2012 年构建的一种语言，尽管我们直到 2016 年随着期待已久的 Angular 2 和 mapped types 的发布才看到它的腾飞。如果你想了解更多关于映射类型的知识，可以看看我写的主题为的文章。

许多人反对让 TypeScript 成为他们生态系统的一部分，因为他们说这违背了 JavaScript 的精神。我不确定这些人是否知道它可以配置得更严格。

人们经常给出这些不使用 TypeScript 的理由:

*   这并不能解决问题。
*   它不是超集，而是子集。
*   它是开源的，但仅此而已。
*   只是炒作而已。

这些理由对我来说都没有多少说服力，但是一旦我们进入 TypeScript 的`strict`模式，它们将看起来更加牵强。

# 什么是 Typescript 的严格模式？

通过使用一个`tsconfig.json`文件来配置 TypeScript。启用`strict`模式非常简单:

这是为了启用引擎盖下的`noImplicitAny`、`noImplicitThis`、`alwaysStrict,`、`strictBindCallApply`、`strictNullChecks`、`strictFunctionTypes`和`strictPropertyInitialization`。

这真是个好消息。这意味着我们可以逐步朝着`strict`前进，所以你不需要马上赶去你的项目并改变`"strict": true`。您可能永远不想将这些属性切换到`on`。这取决于你。

我们将一起查看每处房产，看看为什么拥有它会有意思`on/off`。

在某些地方，您可能希望例外地忽略一些类型脚本错误，您可以通过使用下面的批注来做到这一点:

```
// @ts-ignore
code throwing error here
```

# 1.无 ImplicitAny

该标志防止您公开带有推断的`any`的合同。如果不指定类型，无法推断，则默认为`any`。

如果您对 TypeScript 的推断系统的工作方式有任何疑问，可以查看下面的内容:

 [## 文档型推理

### 在 TypeScript 中，有几个地方在没有…

www.typescriptlang.org](https://www.typescriptlang.org/docs/handbook/type-inference.html) 

我可能会写一篇文章来详细讨论这个问题。敬请关注。

我们来看一些`noImplicitAny`转`on`的代码:

```
const add10 = (number) => number + 10; 
// error: Parameter 'number' implicitly has an 'any'
```

修复非常简单:

```
const add10 = (number: number) => number + 10;
```

所以`noImplicitAny`标志确保你公开了你的代码可以遵守的可读契约。否则，`add10`调用者需要推导出参数是数字，那么使用 TypeScript 又有什么意义呢？

你希望你的代码可读性更强，防错吗？那么这个标志绝对应该打开。

# 2.不影响这个

`this`关键字将基本上防止不必要的行为。它可以让您摆脱一些调试难题:

我知道这个实现很糟糕，但这只是为了说明一点。让我们面对它:如果你没有使用 Angular，你就不会有这个问题，因为你不应该使用类。即使您正在使用类，您也应该使用`lambda`操作符，这将为您解决这个问题，因为它从函数创建者返回相同的`this`上下文。

我看不出你有什么理由不喜欢这个`on`。

# 3.总是严格

对于这一个，只要确保`use strict`在所有转换的 JavaScript 文件中发出，除了编译器。

`ECMAScript strict`模式是在`ES5`中引入的，它只是提示编译器代码应该在`strict mode`中执行，这可以防止你使用未声明的变量。

代码以更安全/更高效的方式运行？绝对值得拥有`on`。

如果您想深入了解 ECMAScript `strict mode`，请查看下面的 MDN 参考资料:

[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode) [## 严格模式

### ECMAScript 5 中引入的 JavaScript 的严格模式是一种选择 JavaScript 的受限变体的方式，因此…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode) 

# 4.strictBindCallApply

启用它将确保您使用正确的参数调用`call`、`bind`和`apply`函数。我不知道你，但我在那里犯了错误，所以它肯定有帮助。

这是一个显而易见的问题，应该转向`on`。

# 5.strictNullChecks

对我来说，这是最重要的一个。这将产生巨大的影响，而且可能是最耗时的。如果该标志为`off`，任何类型都可以分配给`null`或`undefined`。这种`lax`类型的问题是，你不会捕捉到`null`指针异常，你的契约也不会那么具体。

禁用`strictNullChecks`后，即使`book.name`可能是`undefined`，这段代码也会编译。您将错过 TypeScript 的`null`检查警告。

当将`strictNullChecks`设置为`true`时，您将在第 12 行得到一个错误:

```
console.log(book.name) // Object is possibly 'undefined'.
```

因此，您必须为要编译的代码添加空检查:

函数和契约也会发生类似的事情:

您有两种可能的解决方案，请选择更符合您需求的一种:

*   使`logBookName`方法接受`book | undefined`。
*   一个`null`检查条件调用。

很明显，启用这个标志会迫使您编写具有更好类型描述的代码。您将会发现许多`null`错误。通常，这些问题会在单元测试期间被缓存，但是使用 TypeScript，您可以免费获得这些问题。

# 6。strictFunctionTypes

具有该标志`on`可确保更彻底地检查功能参数。

如果该标志没有启用，第 13 行的赋值将正常工作。由于某种原因，TypeScript 的编译器忽略了函数中的类型。最好启用这个来防止这种行为。

# 7.`strictPropertyInitialization`

当设置为`true`时，这将强制您设置构造函数中的所有属性值。

*注意:只有当您启用了* `*strictNullChecks*` *时，才会显示此错误。那是因为如果* `*strictNullChecks*` *被禁用，那么* `*author*` *为* `*undefined*` *是可以接受的。*

# 包扎

![](img/623cef70cff8f602c6dfa7c4eb6b078f.png)

[罗伯特·蒙特罗拉(roberto monterola jr.](https://unsplash.com/@rmonterola?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

通过这七个简单的步骤，我们让 TypeScript 为我们捕捉更多的错误，并在键入合同时更加具体。

这是 TypeScript 所能达到的最严格的要求吗？不完全是。这是开箱后最严格的要求了！您可以向它抛出`eslint`规则来进一步定制它。我的建议是简单一点，用`@typescript-eslint/recommended`。我将很快写一篇文章列举使用这个插件的好处。敬请关注。

如果你想在一个有大量代码的项目上启用`strict`模式特性，并且你觉得你不能一次修复所有的问题，你可以使用一个工具逐步实现。

下面的工具将帮助你学会`strict`打字。通过转换大型代码库，您肯定会学到很多东西。当您的`strict`之旅结束后，您可以永久删除该工具。

[](https://github.com/cschroeter/ts-strictify) [## cschroeter/ts-strictify

### 在严格模式下对已更改的文件运行 TypeScript。TBD:当你开始开发一个新功能或修复一个错误时，你…

github.com](https://github.com/cschroeter/ts-strictify) 

干杯！