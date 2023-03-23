# 理解 JavaScript 中的提升

> 原文：<https://betterprogramming.pub/hoisting-in-javascript-6af97650dbb2>

![](img/741b90e461b5dcd195f8ebb121ad766e.png)

由 [Ron Fung](https://unsplash.com/@oriz?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/jumping-dog?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

如果你是一个新手，提升可能是 JavaScript 中最令人难以置信的话题之一。一开始，这让几乎所有人都感到困惑。别担心——在本文中，我将帮助您理解 JavaScript 中的提升是如何工作的。

# ***什么是吊装？***

提升是 JavaScript 将所有声明移动到当前作用域顶部的默认行为。

以下项目在 JavaScript 中未被**提升**:

1.用表达式定义的函数。

2.用`let`或`const`声明的变量和常量。

3.箭头功能。

在 JavaScript 中，变量可以在声明之前使用。

好吧…但是这是什么意思？我不明白你想说什么。

让我用几个例子来帮助你理解这一点。

当函数声明被提升时，整个函数体也随之提升。因此，在解释器完成第一个示例中的代码后，它的运行方式更像这样:

但是 return 语句后面的代码不是不可达吗？

在 JavaScript 执行中，有`Context`(其中 ECMA 5 分解为`LexicalEnvironment`、`VariableEnvironment`、`ThisBinding`)和`Process`(一组要依次调用的语句)。

当进入执行范围时，声明对`VariableEnvironment`有贡献。它们不同于语句(如`return`)，不受其处理规则的约束。

让我们再看一个例子，这次是一些函数表达式。

请注意，变量在创建时被初始化为未定义的。当执行`VariableStatement`时，而不是当变量被创建时，带有初始化器的变量被赋予其`AssignmentExpression`的值。

```
var myFunction = function () {
   // some executable code
}
```

在上面的代码片段中，左边的`var myFunction`是一个变量声明。

变量声明会被提升，但它们的赋值表达式不会。因此，当`myFunction`升起时，解释器初始设置`*var* myFunction *= undefined*`。函数定义本身没有被提升。

记住以上几点，让我们看看例子二是如何工作的。

它大概是这样工作的:

调用由第一个函数表达式创建的函数后，第二个表达式的代码变得不可访问。

# 在顶部声明你的变量以避免混乱

提升，无论多么有趣，都可能导致错误，因为它很容易被忽视。

你可以尝试*严格模式*，在顶部使用`use strict` 指令。严格模式下的 JavaScript 不允许使用未声明的变量。

这就是本文的全部内容。我希望它能帮助你对 JavaScript 的提升有所了解。