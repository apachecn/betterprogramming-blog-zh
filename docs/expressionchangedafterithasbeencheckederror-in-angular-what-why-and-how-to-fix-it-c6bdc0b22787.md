# Angular 中的 expressionchangedafterithasbeencheckedererror—什么、为什么以及如何修复？

> 原文：<https://betterprogramming.pub/expressionchangedafterithasbeencheckederror-in-angular-what-why-and-how-to-fix-it-c6bdc0b22787>

![](img/349ac973036c7686095899dbd2344987.png)

`ExpressionChangedAfterItHasBeenCheckedError` 毫无疑问是我在[角度](https://angular.io/)应用中最喜欢的错误。

当我第一次开始使用 Angular 时，我经常遇到这个错误。[根据 GitHub](https://github.com/angular/angular/issues/17572) 的说法，很多其他人也是如此。

# 什么时候会抛出 ExpressionChangedAfterItHasBeenCheckedError？

最常见的原因是:

1.  您正在执行`AfterViewInit` 中的代码，这在使用`ViewChild` **、**时经常发生，因为它在`AfterViewInit` 被调用之前是未定义的。
2.  您正在直接操作 DOM(例如，使用 [jQuery](https://jquery.com/) )。Angular 并不总能检测到这些变化并做出适当的反应。
3.  当您在 HTML 模板中调用函数时，由于竞争条件也会发生这种情况。

# ExpressionChangedAfterItHasBeenCheckedError 试图警告我什么？

在 Angular 检查后，当你的 HTML 中的一个表达式改变时，抛出`ExpressionChangedAfterItHasBeenCheckedError`(这是一个非常有表现力的错误)。

此错误仅在开发模式下抛出，且有充分的理由；这通常是一个信号，表明你应该重构你的代码，因为 Angular 警告你，当[启用生产模式](https://angular.io/api/core/enableProdMode)时，你表达式中的这种变化将不会被察觉！

生产模式比开发模式更快的原因之一是 Angular 跳过了一些检查(例如`AfterViewInit`*之后的变更检测)，即在开发模式下完成。这意味着在开发模式下运行良好的代码在生产模式下将无法运行。*

*下面是一个在开发模式下运行良好，但在生产模式下运行不佳的示例:*

*另一个例子:通过`EventEmitter`改变`ngOnInit`中的`@Input`属性也会导致`ExpressionChangedAfterItHasBeenCheckedError`。*

# *如何修复 expressionchangedafterithasbeencheckedererror*

## *解决方案一:让 Angular 知道如何选择更改*

*作为一种快速解决方法，经常使用`[setTimeout](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout)` 或`[ChangeDetectorRef](https://angular.io/api/core/ChangeDetectorRef)` 来使错误消失。*

*后者更好，因为用`ChangeDetectorRef`组件视图及其子视图被检查。另一方面，`setTimeout` 将导致 Angular 检查整个应用程序的更改，这种方式开销更大。*

## *修复二:将代码从 ngAfterViewInit 中移走*

*有时候，错误甚至更容易修复——只需将您的代码移到`[OnInit](https://angular.io/api/core/OnInit)` *。**

*除非你不得不依赖`ViewChild` *、*或者如果一些代码应该只在 Angular 完全初始化一个组件的视图后运行，那么转移到`OnInit` 将会解决你的问题。*

*这是因为 Angular 在生产和开发模式下都会在`OnInit` 之后执行变更检测。*

## *修复三:使用 OnPush 变更检测*

*不喜欢角魔术和它如何检测变化？只需禁用组件中的自动变化检测，并告诉 Angular 自己何时应该检测变化。*

*`[ChangeDetectionStrategy](https://angular.io/api/core/ChangeDetectionStrategy)`可以在组件装饰器中指定给`OnPush`。现在，Angular 只会自动检测`[Input](https://angular.io/api/core/Input)` 的变化，剩下的就看你的了。*

*虽然启用`OnPush` 策略所涉及的魔法更少，但您也可以获得更好的性能，因为 Angular 要做的工作更少。这对于优化大型复杂组件非常有用。*

*另一方面，你需要确保让 Angular 获得变化。最好的方法是使用`[markForCheck](https://angular.io/api/core/ChangeDetectorRef#markForCheck)`。如果你没有让 Angular 意识到这些变化，你通常会看到 UI 错误或不一致(例如，一个模态没有消失)，除非 Angular 做另一个变化检测。*

*所以，不要在没有彻底检查你的组件之前就急于应用它。*

## *修复四:避免@Input 属性的突变*

*`@Input`理想情况下，组件本身的属性不应被修改。通过将`@Input`属性的更新留给父组件，可以避免意外的行为和副作用。除了直接更新`@Input`属性，您还可以将更新的发送给父组件，父组件将更新子组件中的`@Input`属性。*

*顺便说一下:如果你不能避免它，那么你可以利用 [EventEmitter](https://angular.io/api/core/EventEmitter#constructor) 的`isAsync`参数来自动触发变化检测(它在内部调用`setTimeout`)。*

# *结论*

*你现在应该能够理解臭名昭著的`ExpressionChangedAfterItHasBeenCheckedError` 发生的时间和原因了。*

*如您所见，有多种方法可以处理这个错误。只要确保你确实在解决真正的潜在问题，而不是绕过它。如果你知道如何处理这个错误的其他方法，我会很感兴趣。*

*更新 2021: [角度文档现在包含一个常见错误列表，包括这个错误。对于一些错误，如果你更愿意从视频中学习而不是阅读，甚至还有视频。](https://angular.io/errors)*