# 主要指令

> 原文：<https://betterprogramming.pub/the-prime-directive-35bf7aa10e8c>

## 用 Angular 中的指令和自定义验证器验证数据

![](img/63cc1ca5eb79b1da1d78ffcead22d86f.png)

由 [Max Nelson](https://unsplash.com/@maxcodes?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 介绍

使用 Angular 构建站点时，首先要做的事情之一是确保用户输入的格式正确并经过验证。您不希望以电话号码字段中的字母或格式不正确的邮政编码结束。

Angular 提供了两种主要的方法来确保数据的格式和有效性:指令和验证器。

# 应用程序

在本文中，我们将讨论一个简单的应用程序，它只有一个编辑字段，您只能在其中添加数字——只有当这些数字生成一个质数时，它才会被正确验证。

应用程序可以在这里查看和播放[，代码可以在 GitHub](https://vbwigzmp.github.stackblitz.io) 上找到，或者通过这个[完整的 StackBlitz 视图](https://stackblitz.com/github/nickhodges/primedirective)找到。

# 指令

确保正确收集数据的第一种方法叫做*指令*。在 Angular 中，指令用于通过向 HTML 元素添加属性来扩展 HTML。您可能熟悉像`ngFor`和`ngIf`这样的指令。

Angular 为您提供了创建自己的指令的能力。要创建一个指令，你需要在一个类中放置`[@Directive](https://angular.io/api/core/Directive)` 装饰器。装饰器有许多参数可以接受，但最重要的一个是`selector`，它定义了将在 HTML 中使用的名称，以标识使用该指令的元素。

下面是我们在演示应用程序中定义的`numbersonly`指令的基本版本:

```
@Directive({selector: '[numbersonly]'})export class NumbersOnlyDirective {...}
```

这里我们定义了一个`NumbersOnlyDirective`，当`numbersonly`选择器被放置在 HTML 标签中时，它将被使用，如下所示:

```
<input placeholder="Enter only prime numbers!" **numbersonly** />
```

当然，这个类必须实际做一些有用的事情。以下是该项目的完整说明:

关于这个类，有许多事情需要注意:

*   这个类叫做`NumbersOnlyDirective`。它的选择器自然被称为`numbersonly`。
*   该类在其构造函数中引入一个`ElementRef`，它将是对指令将嵌入其中的元素的引用。一会儿我们将看到元素中使用的选择器。
*   这个类有三个方法，分别用于处理在输入框中输入文本的三种方式:键入、粘贴和拖动。
*   这些方法中的每一个都用`[@HostListener](https://angular.io/api/core/HostListener)`[装饰器](https://angular.io/api/core/HostListener)装饰。这个装饰器将来自浏览器的事件绑定到您的 TypeScript 中的代码。它有两个参数——它将绑定到的事件的名称和事件发生时传递给处理程序方法的参数。结果是，当给定事件发生时，将调用此代码。例如，如果您将一些文本拖放到 input 元素中，那么`onDrop`事件将被触发，文本在被插入到 input 元素之前将被去除所有非数字字符。
*   `onKeyDown`方法将使输入只接受数字键和导航键。`onPaste`方法只接受通过粘贴的数字，这与`onDrop`方法非常相似。
*   `onKeyDown`方法使用一组常数值来确定哪些键是可接受的，哪些是不可接受的。那些常量可以在[这个名为`app.consts.ts`的项目文件](https://github.com/NickHodges/primedirective/blob/master/src/app/app.consts.ts)中找到。最终结果是只有数字键和导航键(删除、退格等。)被控件接受，所有其他的都被忽略。

这是我们的应用程序中的一个 HTML 模板，在`<input>`元素中使用了`numbersonly`指令。

注意`<input>`标签上有一个`numbersonly`属性。当`onKeyDown`、`onPaste`和`onDrop`事件在该输入中发生时，这将导致该输入执行来自`NumbersOnlyDirective`的所有代码。因此，当您试用该应用程序时，您会注意到除了输入数字之外，没有别的方法——这正是我们想要的。

最后，为了确保系统知道我们的指令，我们需要将它声明为我们主`AppModule`的`@NgModule`装饰器的一部分:

```
@NgModule({declarations: [AppComponent, NumbersOnlyDirective],...})export class AppModule {}
```

# 自定义验证程序

自定义指令允许您控制给定 HTML 元素的功能。然而，即使我们的 HTML 控件现在可以将输入限制为特定类型的字符，我们可能仍然希望验证该输入，并确保即使在输入限制之后它也是正确的形式。例如，即使您可能将输入限制为有效的电子邮件字符，您仍然需要确保用户输入了格式有效的电子邮件地址。

在我们的例子中，我们想要的唯一有效的输入是质数。为了做到这一点，我们可以通过创建一个定制的*验证器*来插入 Angular 的验证过程。要创建一个定制的验证器，你需要创建一个新的类，这个类有一个方法[返回一个](https://angular.io/api/forms/ValidatorFn) `[ValidatorFn](https://angular.io/api/forms/ValidatorFn)`，这是一个知道如何验证内容的特殊函数。一个`ValidatorFn`采取这种形式:

```
interface [ValidatorFn](https://angular.io/api/forms/ValidatorFn) {[(control: AbstractControl): ValidationErrors | null](https://angular.io/api/forms/ValidatorFn#call) }
```

在我们的例子中，我们希望验证输入到输入控件中的数字是一个质数，因此我们最终得到了以下文件:

以下是关于上述文件的一些注意事项:

*   它利用一个独立的函数`isPrime`，来确定一个给定的`number` 是否是质数。
*   它创建了一个名为`isPrimeNumber`的静态函数，该函数与一个`ValidatorFn`的契约相匹配，从而返回一个函数来验证该数字是否是一个质数。
*   注意，函数本身的返回值要么是一个简单的对象，要么是`null`。在我看来有点奇怪的结果中，如果控件有效，它返回`null`,如果无效，则返回对象。而对象的值是`true`。我觉得这很奇怪，但事情就是这样。

为了利用自定义验证器，我们将其导入，然后将其添加到表单所在组件的`ngOnInit()`方法的`FormControl`中的验证器列表中:

```
ngOnInit(): void {this.noPrimeInput = new FormControl('', [ Validators.required, **NumberValidators.isPrimeNumber()**]);this.formModel = this.fb.group({noPrimeInput: this.noPrimeInput});}
```

主要组件如下所示:

**注意:**这个组件的 HTML 如上所示。

关于上面的代码，有几点需要注意:

*   与内置验证器不同，自定义验证器是通过方法名(包括括号)调用的。
*   该组件使用各种方法来显示或隐藏一个标签，该标签声明给定的数字是质数。这主要是内务代码，所以我们不会在这里讨论它。

# 完整的申请

在这一点上，我们已经使用了一个定制的指令和一个定制的验证器来完成我们设定的任务——也就是说，只通过`NumbersOnlyDirective`来限制对数字的输入，并通过`NumberValidators.isPrime()`方法来确保我们只在数字是质数时才验证输入。

同样，您可以在这里看到正在运行的应用[。](https://stackblitz.com/github/nickhodges/primedirective)

# 结论

输入控制和验证是应用程序中非常重要和基本的部分。让用户容易做正确的事情是良好用户体验的一部分。通过使用自定义指令和自定义验证器，您可以确保用户输入的数据被正确格式化和存储。