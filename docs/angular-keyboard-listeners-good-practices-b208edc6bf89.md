# Angular 中的可访问性——良好实践和缺陷

> 原文：<https://betterprogramming.pub/angular-keyboard-listeners-good-practices-b208edc6bf89>

## [软件工程](https://rakiabensassi.medium.com/list/software-engineering-7a179a23ebfd)

## 关于键盘监听器的 5 个案例研究

![](img/ad834674f968f0a1b1d596e9a5347b71.png)

由 [Sigmund](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

作为[开发人员](https://levelup.gitconnected.com/learning-velocity-and-coding-standards-10952f6c9640)，我们喜欢在日常编码生活中使用键盘。与使用鼠标相比，它使事情变得更容易、更快捷。然而，在这份爱中，我们并不孤独；我们正在实现的应用程序的许多最终用户也喜欢这样做。设计一个更易访问的应用程序通常可以改善用户体验。这里唯一的问题是，对我们来说，知道如何使用键盘比知道如何通过按键盘上的一些键来实现一个可访问的功能要简单得多。

每当你添加一个事件监听器到你的代码中，你应该以一种优化的方式去做，以避免从用户的浏览器中吸取性能或者耗尽他们的小电池。

在今天的文章中，我将重点关注通过键盘事件或快捷键对用户做出反应。我将举五个在 [Angular app](https://medium.com/better-programming/angular-10-new-features-dbc779061dc8) 中处理键盘监听器的真实例子。让我们开始吧。

# **案例研究 1**

假设您的组件中有一个输入字段，并且您想在用户按 F2 时打开一个对话框。你可以用两种方法来做这件事。

## **选项 1**

在您的 HTML 中添加`(keydown.F2)`:

```
<input matInput (keydown.F2)=”onF2KeyPress($event)”>
```

以及 TypeScript 代码中的`onF2KeyPress()`方法:

```
onF2KeyPress(event: KeyboardEvent) {
    this.openOptionsDialog(…);
}
```

## **选项二**

只需在您的 TypeScript 代码中添加此方法:

```
@HostListener(‘keydown.F2’, [‘$event’])
onF2KeyPress(event: KeyboardEvent) { 
    this.openOptionsDialog(…);
}
```

哪个选项更好？

对于第一个，我们有一个仅用于输入字段的`keydown.F2`监听器。在第二个例子中，我们有一个用于整个组件的`keydown.F2`监听器，每当按下 F2 键时就会被执行——即使焦点在输入字段之外。我个人更倾向于第一种选择。

## **陷阱**

如果同时使用这两个选项:

```
(keydown.F2)=”onF2KeyPress($event)”
```

在输入字段和

```
@HostListener(‘keydown.F2’, [‘$event’])
```

在方法`onF2KeyPress()`上面，你的逻辑将被执行两次。在这种情况下，对话框将打开两次，您可能不会注意到它，直到您看到只有单击两次关闭按钮才能关闭对话框。

# **案例研究 2**

假设您有一个`formArray`，每当用户点击另一个`formGroup`时，您需要保存之前编辑的`formGroup`。为了实现这一目标，您创建了以下方法:

```
@HostListener(‘document:click’, [‘$event’])
оnClick(event: KeyboardEvent) {
    this.saveItem();
}
```

这里有什么问题？

该方法是整个文档(您面前的 HTML 视图)上的任何单击的侦听器，而不仅仅是表单区域中的单击的侦听器。如果用户从`formArray`打开一个对话框并点击它，即使不需要`onClick()`和`this.saveItem()`方法也会被执行(因为用户仍在对话框中工作)。

如果`saveItem()`方法调用一些服务方法并发送一些 REST API 请求以在后端执行某些东西，这种情况可能会更糟。

# 案例研究 3

假设您有一个带有一些字段的表单，用于输入一些搜索标准，并且您想为用户提供通过点击搜索按钮或按回车键来执行`search()`方法的可能性。为了实现这一点，您实现了如下 HTML 代码:

你的打字稿是:

```
search() {
    this.service.search(this.form);
}
```

你能猜出这里出了什么问题吗？

有两个问题:

*   首先，每当发出`keyup`事件时(甚至当用户按 Tab、shift、F3、…)都会执行`search()`方法，而不仅仅是在按 Enter 键时。要解决这个问题，HTML 代码中的第一行必须调整如下:

```
<form name=”form” [formGroup]=”form” **(keyup.enter)=”search()”**>
```

*   其次，您希望仅当表单有效且被触摸时才允许调用进行搜索。这可以通过禁用搜索按钮(`[disabled]="form.invalid` || `form.untouched"`)来正确实现，但在按下 Enter 后调用`search()`会违反这一点。以下可能是此错误的解决方法:

```
search() {
    **if(this.form.valid && this.form.touched) {**
        this.service.search(this.form);
    **}**   
}
```

# 案例研究 4

您已经实现了一个带有表单的对话框来创建或更新一些项目。输入数据后，用户可以单击 save 按钮或按 Enter 键向 REST API 发送保存请求。发送保存请求后，对话框应该会自动关闭。

你能猜到这是否会像预期的那样起作用吗？

那么，如果表单包含一个下拉字段，而用户想要使用键盘从其中选择一个选项，该怎么办呢？

1.  要打开下拉列表中的选项列表，用户在该字段中设置焦点后按 Enter 键。
2.  然后，他们使用向下箭头/向上箭头导航到想要的选项。
3.  然后他们按回车键选择该选项。

当您的最终用户注意到每当他们按下回车键时，当他们想在关闭对话框之前继续编辑时，对话框消失了，他们会感到沮丧。

你如何解决这个问题？

您可以使用另一个键盘监听器来调用`save()`方法。`control.s`可能是另一种选择:

```
@HostListener(‘keydown.control.s’, [‘$event’])
оnKeyDown(event: KeyboardEvent) {
    this.save();
}
```

# 案例研究 5

在您的 web 应用程序中，有一个带有多个操作按钮的视图，应该可以通过键盘访问。对于每个动作按钮，您都添加了不同的快捷方式监听器:`alt+e`、`alt+r`、`alt+o`等。

这个解决方案有什么问题？

最终用户可能有不同的设备。这些快捷方式对 Mac 用户来说没有用。作为一种解决方案，您可以为这些快捷键添加监听器:`cmd+e`、`cmd+r`、`cmd+o`等。

# 外卖食品

让我们总结一下我们刚刚学到的内容:

*   限制并指定必须监听某些键盘事件的区域。
*   深思熟虑地选择你的[键盘监听器](https://medium.com/javascript-in-plain-english/angular-material-table-with-edit-function-like-excel-7c2c53332553)，并尝试想象它们可能的副作用以及它们不起作用的情况。
*   不要忘记，您的最终用户有不同的设备。当您添加键盘快捷键时，它们必须对所有可能的最终用户(PC、Mac 等)有用。

感谢阅读。如果您有任何问题，请随时发表评论。

🧠💡我为一群聪明、好奇的人写关于工程、技术和领导力的文章。 [**加入我的免费电子邮件简讯，独家获取**](https://rakiabensassi.substack.com/) 或在此注册 Medium [。](https://rakiabensassi.medium.com/membership)

*你可以在 Udemy 上查看我的* ***视频课程****:*[*如何识别、诊断、修复 Web Apps 中的内存泄漏*](https://www.udemy.com/course/identify-and-fix-javascript-memory-leaks/) *。*