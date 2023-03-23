# 防止对象在 JavaScript 中被更改

> 原文：<https://betterprogramming.pub/javascript-prevent-objects-from-being-changed-d1ca82f02975>

## 冻结、密封、防拉伸、isFrozen、isSealed、isExtendable 和 const 的不变性

![](img/33025288b23cb0ced1df49a9635b5f00.png)

照片由[马克·索拉斯基](https://unsplash.com/@marksolarski?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 JavaScript (ES6)中，有多种方法可以通过使对象不可变来保证对象的安全。这减少了副作用和不可预测的行为。但是什么是什么？有四个选项可用，我将在下面的一些简单示例中向您展示。

*注意:这也适用于 TypeScript。*

# 限制对象

## 1 — `const`

防止对象被更改的最常见也是最广为人知的方法是使用`const`关键字。与`let`和`var`不同，你**不能将对象**分配给其他东西，但是你仍然可以改变每个属性的值，删除一个属性，或者创建一个属性。

## 2—预防拉伸

更严格一点，我们使用`Object`方法。第一个防止改变对象属性的对象方法叫做`preventExtentions`。这将**阻止向对象添加新属性**，但仍允许更新和删除现有属性。

## 3 —密封

接下来的方法是`seal`。这将**防止向对象添加新属性和从对象中删除现有属性**，但是仍然允许更新现有属性。

## 4 —冻结

而最后一个叫做`freeze`。这将**阻止对对象做任何**改变。使用`freeze`你可以确定它没有在你的代码中被修改。

## 感谢您的阅读！我的 [Github](https://github.com/jeroenouw/) 或者 [Twitter](https://twitter.com/jeroenouw) 。如果你觉得这篇文章有用，请鼓掌，并考虑阅读我的其他文章:

[](https://medium.com/@jeroenouw/ecmascript-vs-typescript-private-fields-640ae37aa162) [## EcmaScript 与 TypeScript —私有字段

### TypeScript 的 private 关键字和 EcmaScript/JavaScript 的#字符有什么区别

medium.com](https://medium.com/@jeroenouw/ecmascript-vs-typescript-private-fields-640ae37aa162) [](https://levelup.gitconnected.com/typescript-nullish-coalescing-operator-always-have-a-fallback-value-c24e2be6fe5e) [## TypeScript Nullish 合并运算符:始终有一个回退值

### 没有 null 且未用 Nullish 合并运算符定义的回退值

levelup.gitconnected.com](https://levelup.gitconnected.com/typescript-nullish-coalescing-operator-always-have-a-fallback-value-c24e2be6fe5e) [](https://levelup.gitconnected.com/upgrade-to-angular-9-within-10-minutes-671c6fd6174b) [## 10 分钟内升级到 Angular 9

### 现在轻松地将您现有的项目更新到 Angular 的新版本

levelup.gitconnected.com](https://levelup.gitconnected.com/upgrade-to-angular-9-within-10-minutes-671c6fd6174b)