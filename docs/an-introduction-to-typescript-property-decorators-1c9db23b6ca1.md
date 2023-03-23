# TypeScript 属性装饰器简介

> 原文：<https://betterprogramming.pub/an-introduction-to-typescript-property-decorators-1c9db23b6ca1>

## 对 TypeScript 装饰器的深入探究

![](img/f3fc354bb9ced74ccc8f94b2b4d53c5e.png)

由[布鲁克·拉克](https://unsplash.com/@brookelark?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/cake?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

在上一篇文章中，我们探讨了 TypeScript 类装饰器。这一次，我们将深入到财产装饰。属性装饰者可以改变来自类属性的值。

如果你还没有读过我关于类装饰器的文章，我建议你先读一读。它包含有助于您跟进的重要信息。

# 装饰类型

为了描述 decorators 在 TypeScript 中做什么，我们需要更深入地研究每种类型。有几个不同的:

*   [类装饰器](https://medium.com/me/stats/post/afb996af0763)(上一张)
*   属性装饰器(当前片段)
*   方法装饰器(稍后会出现)
*   访问器装饰器(稍后会出现)
*   参数装饰器(稍后会出现)

# 物业装饰

使用属性装饰器，您可以在运行时更改属性中的值。属性装饰器是在属性声明之前声明的。它不能在声明文件中使用，也不能与`declare` [语句](https://www.typescriptlang.org/docs/handbook/declaration-files/by-example.html)结合使用。

这是使用属性装饰器的方式:

# 如何创建一个属性装饰器

当您创建一个属性装饰函数时，您需要有两个参数。

1.  `target`，它指的是正在使用装饰器的类的构造函数或原型。
2.  `key`，它指的是您正在使用装饰器的类属性。

当使用`target[key]`时，您将获得属性内部的值(该类实例的值)。这个超级有用！

`Class decorator`函数不能访问任何属性值。这就是为什么类装饰器更适合添加属性。对于修改属性值来说不是很有用。这是可能的，但会带来副作用。

让我们创建我们的球员姓名表情符号装饰器，根据球员类型在球员姓名后添加表情符号:

# 吸气剂

`getter`函数用于获取使用属性装饰器的类的属性值。

在这个函数中，我们可以访问这个类的实例。在 return 语句中，我们返回`val`，它包含该属性的值。接下来，我们调用`getEmoji(this.type)`，它根据玩家类型给出正确的表情符号。

如果我们试图在不传递`this`变量的情况下访问`getEmoji()`函数中的`this`，我们会得到一个错误。这是因为我们只能访问`getter`函数中`PlayerCharacter`的实例。

# 作曲者

使用`setter`，我们可以改变类属性实例的值。在这种情况下，我没有改变它，但增加了一个表情符号。

当我们试图在那里添加表情符号时，我觉得这听起来很合理，但我们会得到一个错误。这是因为我们无法访问`setter`中类的整个实例。

# 定义属性

最后，我们在属性装饰器中有一个访问器描述符`Object.defineProperty()`。这个描述符将描述我们的属性在类中的行为。注意:我强烈推荐阅读这篇文章，因为它非常强大。

在描述符内部，我们有一个`getter`和`setter`方法。当你想得到或改变它的值时，它们将被执行。接下来，我们将`enumerable`设置为`true`。这将确保它在相应对象的枚举过程中是可见的。

根据 [MDN web 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)，“访问器描述符是由 getter-setter 函数对描述的属性。”

# 错误

我猜你们中的很多人以后都会犯一些错误。(*喜欢我*😉)希望这些解决方案能进一步帮助您。

## 不影响这个

可能会出现这样的错误。[纳撒尼尔可能](https://medium.com/u/dd2a1653ff14?source=post_page-----1c9db23b6ca1--------------------------------)在[评论](https://medium.com/@nathaniel.may22/any-idea-how-to-handle-the-errors-related-to-the-noimplicitthis-tsconfig-json-complieroption-62b527add101)中问过这个问题。

```
'this' implicitly has type 'any' because it does not have a type annotation.
```

一个快速但肮脏的解决方法是在 tsConfig.json 中将`noImplicitThis`设置为`false`。

潜在的问题是，你在你的一个`Class`方法中使用了一个`function`，并且想要拥有一个`Class`的属性。

这个问题的解决方案是使用一个箭头函数，因为它们没有自己的`this`。[箭头中的`this`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)是指它的父函数。

希望这将修复您的错误。

# 结论

感谢阅读！

现在，您可以在您的类中使用属性装饰器的全部功能了。如果你有任何问题，请让我知道，这样我可以帮助你。

# 从我这里读更多

[](https://medium.com/better-programming/2-ways-to-resolve-duplication-in-javascript-arrays-and-objects-e377e1bdc5e1) [## 解决 JavaScript 数组和对象重复的两种方法

### 你知道如何处理重复吗？

medium.com](https://medium.com/better-programming/2-ways-to-resolve-duplication-in-javascript-arrays-and-objects-e377e1bdc5e1) [](https://itnext.io/what-is-the-scope-in-javascript-a2add52bf9d8) [## JavaScript 中的作用域是什么

### 关于全局范围内的变量、常量、Let、函数、对象和类的行为的解释。

itnext.io](https://itnext.io/what-is-the-scope-in-javascript-a2add52bf9d8) [](https://medium.com/the-human-programmer/top-5-favourite-medium-javascript-posts-6549c9a5c2e1) [## 最受欢迎的 5 篇中型 JavaScript 文章

### 我最喜欢的 2020 年 2 月关于 JavaScript 开发的帖子

medium.com](https://medium.com/the-human-programmer/top-5-favourite-medium-javascript-posts-6549c9a5c2e1) [](https://medium.com/better-programming/make-your-javascript-objects-more-predictable-by-creating-maps-20ac1a795442) [## 通过创建地图使您的 JavaScript 对象更加可预测

### 不再有未定义的属性

medium.com](https://medium.com/better-programming/make-your-javascript-objects-more-predictable-by-creating-maps-20ac1a795442) [](https://medium.com/better-programming/7-steps-to-dockerize-your-angular-9-app-with-nginx-915f0f5acac) [## 使用 Nginx 对 Angular 9 应用程序进行分类的 7 个步骤

### 在 Docker 环境中设置 Angular 9 应用程序，并立即进行部署

medium.com](https://medium.com/better-programming/7-steps-to-dockerize-your-angular-9-app-with-nginx-915f0f5acac)