# 如何在 JavaScript 中安全地使用嵌套对象

> 原文：<https://betterprogramming.pub/how-to-safely-work-with-nested-objects-in-javascript-aed39fb8bad2>

## 使用嵌套对象时要小心

![](img/7f0ee312eb55a7c1590943914e7c027c.png)

菲利普·姆罗兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

如果您以前使用过 API，那么您很可能使用过深度嵌套的对象。考虑以下对象:

让我们尝试访问一些值:

```
console.log(
    someObject.data[0].attributes.color
)
// red
```

这很好，但是如果我们试图访问数据中第二个元素的`color`属性呢？

```
console.log(
    someObject.data[1].attributes.color
)
// undefined
```

它打印出`undefined`,因为`attributes`属性是空的。让我们尝试访问`arr`属性中的第二个元素:

在第一种情况下，`2`被打印在控制台上。然而，在第二种情况下，我们得到一个错误。

这是因为`someObject.data[1].attributes`为空，因此`attributes.arr`为`undefined`。当我们试图访问`arr[1]`时，我们实际上是试图索引`undefined`，这导致了一个错误。

我们可以将代码放在一个`try..catch`块中来优雅地处理错误，但是如果您有很多需要访问深度嵌套值的情况，您的代码看起来会很冗长。

让我们看另一个场景。这一次，我们想要更新`arr`中索引`0`处元素的值:

我们再次得到类似的类型错误。

# 安全访问深度嵌套的值

在本文中，我们将讨论三种方法。

## 使用普通 JS

我们可以使用[可选链接(？。)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining)操作员:

请注意，这次它不会导致错误。而是打印`undefined`。`?.`导致表达式短路(即，如果`?.`左侧的数据是`undefined`或`null`，则返回`undefined`，不再对表达式求值)。

目前，大多数浏览器都支持可选链接。但是，如果您对使用 Lodash/Rambda 感兴趣，可以查看下面的部分。

## 使用 Lodash

如果你不想在你的代码中看到一堆问号，你可以使用 Lodash 的`get`函数。下面是语法:

```
get(object, path, [defaultValue])
```

首先，我们需要安装 Lodash:

```
npm install lodash
```

下面是使用`get`函数的代码片段:

默认值是可选的。如果不指定默认值，它将简单地返回 undefined。

## 使用 Rambda

我们可以使用`path`或`pathOr`功能。不同之处在于，使用`pathOr`函数，我们可以指定一个默认值。

要安装 Rambda:

```
npm install rambda
```

下面是访问这些值的代码片段:

# 安全设置深度嵌套对象的值

## 使用 Lodash

我们可以使用洛达什的`set`函数。下面是语法:

```
set(object, path, value)
```

如果我们提供一个不存在的路径，它将创建路径:

最初，`attributes`属性是空的，但是当我们试图为`attributes.arr[1]`设置一个值时，一个`arr`属性被添加到`attributes`，一个空元素被添加，然后`200`被添加。

基本上，如果我们指定的路径不存在，它将创建该路径并设置值。

## 使用 Rambda

我们可以使用 Rambda 中的`assocPath`函数做一些类似于 Lodash 的`set`函数的事情:

`assocPath`不是就地函数(即不更新对象)。它返回一个新的对象。

# 资源

[本文](/4-ways-to-safely-access-nested-objects-in-vanilla-javascript-8671d09348a8)作者[曾厚林](https://medium.com/u/2290b8c53ff8?source=post_page-----aed39fb8bad2--------------------------------)讲述了使用普通 JavaScript 访问深度嵌套值的四种不同方法。

[本文](https://medium.com/javascript-inside/safely-accessing-deeply-nested-values-in-javascript-99bf72a0855a)作者 [A. Sharif](https://medium.com/u/52e7bcc5db67?source=post_page-----aed39fb8bad2--------------------------------) 也谈到了安全访问深度嵌套对象和一些更多的 Rambda 函数。

【https://www.realpythonproject.com】原载于[](https://www.realpythonproject.com/how-to-safely-work-with-nested-objects-in-javascript/)