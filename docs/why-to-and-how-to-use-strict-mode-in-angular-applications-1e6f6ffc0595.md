# 为什么以及如何在角度应用中使用严格模式

> 原文：<https://betterprogramming.pub/why-to-and-how-to-use-strict-mode-in-angular-applications-1e6f6ffc0595>

## 优势是值得努力的

![](img/80d6ce413cebc30b3192c42297f0719b.png)

由[泰勒·米利根](https://unsplash.com/@tyler_milligan_visuals?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

最近，我加入了一个中型代码库的新项目。像往常一样，我会偷偷摸摸地查看代码，修复一些错误，以熟悉代码、底层业务和潜规则。

我首先注意到的是 Angular API 的一些误用以及由于缺乏类型安全性而导致的许多问题。然而关键字`any`压倒性地出现在代码库中。

当然，我看了一下`tsconfig.json`，找到了这些问题的原因——严格模式没有启用。不适用于打字稿和角度模板。于是我激活了它们，旅程开始了…

# 为什么是严格模式？

但首先，让我们总结一下为什么启用严格模式很重要:

*   编译器是你的朋友——但你应该确保它能为你做得最好。如果它不能推断事物的类型，它就不能阻止你犯错。
*   将运行时错误转化为编译时错误。在代码在生产中运行之前可以发现的每个错误都是永远不会向用户显示的错误。
*   如果你需要重构你的代码，单元测试是很棒的。您将需要这样做——否则，您已经编写了可能从未使用过的代码。但是，如果不使用严格的类型检查，更改一些非预期内容的可能性就更大了。
*   你会提高你的生产力。因为每样东西都有一个合适的类型，所以您永远不需要分析完整的代码流来确定它是什么类型，或者它是否可以为空或未定义。
*   您解决的另一个问题是，可以检查从组件的 HTML 模板生成的代码，以匹配输入类型要求。如果你不使用模板严格的选项，你可以做一些奇怪的事情。

# 如何启用严格模式？

如果你正在使用一个新的 Angular 应用程序，你可以使用 Angular CLI 的`--strict`选项。这将启用您的`tsconfig.json`文件中所有可用的严格选项。我在下面的代码块中突出显示了负责的选项:

```
{
  "compileOnSave": false,
  "compilerOptions": {
    "baseUrl": "./",
    "outDir": "./dist/out-tsc",
    "forceConsistentCasingInFileNames": true,
    **"strict": true,
    "noImplicitOverride": true,
    "noPropertyAccessFromIndexSignature": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,**
    "sourceMap": true,
    "declaration": false,
    "downlevelIteration": true,
    "experimentalDecorators": true,
    "moduleResolution": "node",
    "importHelpers": true,
    "target": "es2017",
    "module": "es2020",
    "lib": [
      "es2020",
      "dom"
    ]
  },
  "angularCompilerOptions": {
    "enableI18nLegacyMessageIdFormat": false,
    **"strictInjectionParameters": true,
    "strictInputAccessModifiers": true,
    "strictTemplates": true**
  }
}
```

你可以在这里找到更多关于 Angular 编译器选项的信息:[https://angular.io/guide/template-typecheck](https://angular.io/guide/template-typecheck)。关于 TypeScript 编译器严格选项的描述可以在这里找到:[https://www.typescriptlang.org/tsconfig#strict](https://www.typescriptlang.org/tsconfig#strict)。

# 现有应用的严格模式

对于现有的应用程序，您也可以打开这些选项。但是——取决于代码库的大小和现有的代码质量——您将面临大量的编译器错误，并且您将立即关闭这些选项。一步到位解决所有问题可能是不可能的。

如果你已经在一个核心的、共享的和多特性的库中构建了你的代码，你是幸运的。在这种情况下，您可以逐个启用严格模式库并修复编译器错误。您拉取请求的审核者也会对此非常感激。

另一个策略是逐个启用严格选项。另外，你需要知道`strict`选项是不同选项的组合。大部分都可以独立开启。

我的情况是从`strictBindCallApply`、`strictFunctionTypes`、`noImplicitReturn` 和`noImplicitAny`开始。如果您已经修复了所有编译错误，那么您应该有一个代码库，其中所有内容都有一个合适的类型。这将使下面的重构变得容易得多。

最后一步是开启`strictNullChecks`和`strictPropertyInitialization`最后一个选项需要开启`strictNullChecks`。尤其是最后一个选项会改变你写代码的方式。

# StrictNullChecks

不过先来看看`strictNullChecks`。在大多数情况下，您所要做的就是使用可选的链接操作符来避免访问可能是`null` 或`undefined`的值。

但是可选的链会破坏你的代码。请记住，选项是语法糖，并被编译成 JavaScript。它们是`if`语句或三元运算符。例如，在您的 TypeScript 代码中，使用可选链接运算符的一个非常简单的情况很简单:

```
***console***.log(this.address?.street)
```

但是它被编译成 JavaScript，可能看起来像这样:

```
***var n;
console***.log(null===(n=this.address)||void 0===n?void 0:n.street)
```

不幸的是，即使你的`address`永远不可能是`null`或`undefined`，情况也是如此。所以请记住:不检查`address`是否存在是错误的，但是如果您毫无理由地使用可选的链接操作符，编译器不会警告您。

另一个绊脚石是可选的链接操作符阻止您访问`null`或`undefined`值。但是你不能给可以被`undefined`的东西赋值。以下情况是不可能的:

```
this.address?.street = 'any street';  (compile error)
```

不幸的是，这些并不是唯一有问题的案例。想象一个可能是`null`的数组，你想检查它的大小:

```
if (countries?.length > 2) {...}  (compile error)
```

因为`countries?.length`有可能是`undefined`，所以不能用一个数字来比较。在这些情况下，你需要检查`countries`是否以旧的方式存在。但正如我们马上会看到的，这也是可以解决的。

# StrictPropertyInitialization

如果您启用了`strictPropertyInitialization`选项，编译器将强制在对象创建后，每个类成员都有一个与其类型匹配的初始值。如果你不能提供一个合适的值，你需要告诉编译器这个成员可能是`undefined`。

之后，您几乎不可能访问一个可能是`undefined`的值并导致运行时错误。不仅如此，你将开始重新思考你的代码的设计。让我们来看一些例子，看看如何提供一个合适的初始值:

*   最简单的:用`false`初始化一个布尔值，用空字符串初始化一个字符串，或者用 0 初始化一个数字。如果数字表示数组的索引，则-1 也可以是合适的初始值。
*   我见过很多角应用的形式。在大多数情况下，它们是用`FormBuilder`创建的，但是你不需要使用这个对象，你可以用`name = new FormControl()`创建一个`FormControl`，然后用一个`FormGroup`组合它们。这都可以在成员初始化期间完成。好的副作用:在你的模板中不再需要`form.get('name')`。你只要写:`<input [formControl]="name"/>`模板里的一个函数调用就没了。
*   如果您有更高级别的对象，您可以提供一个“空对象”。并不意味着值为空；这意味着该值表示没有有意义行为的值。这种模式也可以应用于稍后设置的回调函数。角度应用的一个典型案例是`ControlValueAccessor`实现中的`onChange`和`onTouch`回调。只要用一个空函数`()=>{}`初始化这些函数，你就永远不必处理这些函数没有被设置的情况。
*   如果您有一个`enum`来代表不同的州，您可以创建一个附加选项`UNKNOWN`。
*   经常会有类似配置对象的东西。您可以用默认配置初始化成员，并将默认值与具体值合并。
*   一个简单但影响巨大的变化:确保所有数组都用空数组初始化。在这种情况下，您永远不会遇到长度检查必须处理我们在上面看到的`undefined`情况(`array.length > 0`)。同样，像`map`、`filter`或`reduce`这样受人喜爱的数组函数也将受益于这个初始值。
*   不要忘记在构造函数中设置初始值是可能的。但是要小心:Angular 注入的值可能还没有运行过它的`ngOnInit`函数，输入属性可能还没有设置。
*   如果您使用`ngrx`，您可以在构造器中初始化您的选择:`this.myObservable$ = store.select(selector())`。如果你这样做，你的模板中的`async`管道将像一个魔咒一样工作，因为可观察的永远不会是`null`或`undefined`。
*   如果您使用 OpenApi 并生成您的模型，不要忘记指定必填字段，否则它们会作为可选属性生成，您需要处理它们。我比较经常看到的:指定了模型，没有设置必填字段。现在每个使用这个 API 的人都必须处理属性可能是`undefined`的情况——在前端和后端代码中。但是在大多数情况下，这些属性总是有一个合适的值。不要忘记集合类型。如果没有可用的集合项目，请确保它们始终作为空集合传输。

一个警告。角度应用的严格模式从版本 10 开始可用。仍然存在框架没有为其 API 提供正确类型的情况。例如，反应式表单 API。但是团队正在解决这些问题。

看看[https://github.com/angular/angular/issues/13721](https://github.com/angular/angular/issues/13721)看看表单的类型化 API 的状态。

# 结论

有时候，我在想为什么人们会用 TypeScript 和 Angular。您有合适的工具来编写高质量、干净、可维护和可读的代码，而不必使用低级浏览器 API 和 DOM，这可以提高您的工作效率。

此外，您可以将运行时错误转化为构建时错误。但我看到的是，所有帮助你实现这些目标的东西都没有被激活或有意去激活。

不要这样。使用`strict`选项生成您的新应用。如果您还没有，现在激活严格编译器选项。优点值得努力！

下一个大任务是告诉您的同事，如果您不在每次提交时自动格式化代码，为什么会浪费时间和资源:)