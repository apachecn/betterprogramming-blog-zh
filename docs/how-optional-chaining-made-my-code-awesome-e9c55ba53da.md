# 可选链接如何让我的代码棒极了

> 原文：<https://betterprogramming.pub/how-optional-chaining-made-my-code-awesome-e9c55ba53da>

## 访问嵌套属性的安全方式，即使属性不存在

![](img/1f5b2e0b7d8616398f729426a0f099d3.png)

[Jordan M. Lomibao](https://unsplash.com/@jlcruz_photography?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

# 介绍

如 MDN [文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining)中所述:

> "**可选链接**操作符(`**?.**`)允许读取位于连接对象链深处的属性值，而不必明确验证链中的每个引用是否有效。"

换句话说，使用`?.`的可选链接是访问嵌套对象属性的安全方式——即使中间属性不存在。

# 我为什么要用这个？

一个对象可以有非常不同的对象嵌套结构。在 JavaScript 中处理数据经常会遇到不确定某样东西是否存在的情况。

## 例子

例如，考虑应用程序数据的对象。我们的大多数应用程序在`app.author`属性中有一个名为`app.author.firstName`的作者，但是有些应用程序没有。

在这样的情况下，当我们试图获取`app.author.firstName`时，我们可能会得到一个错误:

```
let app = {}; // app object without author propertyconsole.log(app.author.firstName); // Error
```

## 我们该如何解决这个问题？

我们通常会使用如下条件运算符来检查该属性是否存在:

```
let app = {}; console.log(app.author ? app.author.firstName : undefined);
```

但是如果我们想读取作者的 Twitter 句柄呢？然后我们会继续写这个:

```
let app = {};console.log(app.author ? app.author.social ? app.author.social.twitter : undefined : undefined);
```

这看起来好可怕**，**对不对？这时，可选链接就来帮忙了。

# 可选链接(？。)

可选的链接`?.`不是一个操作符，而是一个特殊的语法结构，如果`?.`之前的部分没有被定义或者`null`没有被定义，它将停止求值并返回`undefined`。

对于上面的例子，编写代码的可选链接方式是:

```
let app = {};console.log(app?.author?.social?.twitter); // undefined
```

在这个例子中，由于作者没有被定义，它在那之后停止评估并返回`undefined`。这个语法看起来很简单，不是吗？

# 可选链接的变体

可选链接还有另外两种变体。您可以使用这种方法来调用函数，调用对象的属性，或者读取数组的条目。

## 调用函数

## 调用对象属性

# 需要小心的事情

## 1.之前的初始变量？。应该被定义

如果没有定义初始变量，就会导致错误:

```
console.log(app?.author) // app is not defined
```

## 2.使用？。用于读取和删除，但不能写入

不要在 JS 表达式的左边使用可选的链接。这会导致一个错误:

```
let app = {};  
app?.author = 'Harsha'; // Invalid left-hand side in assignment
```

# 摘要

可选的链接`?.`语法有三种形式:

1.  如果`obj`存在，则`obj?.prop`返回`obj.prop`。否则，它返回`undefined`。
2.  如果`obj`存在，则`obj?.[prop]`返回`obj[prop]`。否则返回`undefined`。
3.  如果`obj.method`存在，则`obj.method?.()`调用`obj.method()`。否则，返回`undefined`。

# 参考

[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining) [## 可选链接(？。)

### 可选的链接操作符()允许读取一个位于连接的…链深处的属性值

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining) [](https://javascript.info/optional-chaining) [## 可选链接？.'

### 可选链接？。是访问嵌套对象属性的安全方式，即使中间属性不…

javascript.info](https://javascript.info/optional-chaining) 

我希望你觉得这篇文章很有趣。感谢您的阅读！