# 为 React 国际化消息编写抽象代码

> 原文：<https://betterprogramming.pub/building-an-abstraction-for-react-internationalization-messages-7054d07af9b7>

## 让您的应用全球化变得更加简单

![](img/74f39465d25ca5a05cbb10f8cc994d34.png)

照片由[凯尔·格伦](https://unsplash.com/@kylejglenn?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这篇文章最初发表在我的博客上: [**构建 React 国际化消息的抽象**](http://leandrotk.github.io/tk/2020/01/building-an-abstraction-for-react-internationalization-messages/index.html) 。

我遇到了一个我想要构建的特性，它的一部分包括基于来自 API 的数据类型呈现国际化文本。这个 API 可以返回三种类型:`common`、`password`或`biometry`。我们用它来构建我们的`EntryInfo`组件。

对于`common`类型，API 响应如下所示:

类型为`common`，`password`为`null`，`ownerName`以字符串的形式出现。

对于`password`型:

类型是`password`，类型`ownerName`是`null`，但是`password`是以字符串的形式出现的。

对于`biometry`型:

类型为`biometry`，没有`ownerName`和`password`。

这是我们从 API 收到的三种可能的有效载荷。我需要根据这些数据呈现一个国际化的文本。

基于类型和其他值构建消息文本的逻辑是:

*   当`type`为`'Common'`时，渲染`'Owner {ownerName} will be there'`。
*   当`type`为`'Password'`时，渲染`'Password: {password}'`。
*   当`type`为`'Biometry'`时，渲染`'Type: biometry'`。
*   当`type`为`null`时，渲染`'Call the owner'`。

所以，我做的第一件事是建立消息定义:

`EntryInfo`组件类似于:

为了遵循定义的逻辑，我只是添加了一个`if-elseif-else`来使用`intl.format`函数呈现适当的消息。很简单，`intl.format`函数接收适当的消息，返回条目信息文本，并将其传递给组件进行呈现。

但是我可以在一个助手函数`getEntryInfo`中将它分离出来，将逻辑从 UI 组件中分离出来。我也可以将它导出到单元测试中，而不会太复杂。

我还将`intl`对象传递给这个新函数，以返回正确的字符串。

这个逻辑可能只是一个对象映射器，而不是 if-else 或 switch-case 语句。所以在`getEntryInfo`中只是一个小的重构:

该类型是硬编码的，因此我们也可以使用枚举来重构这些常量:

现在我们可以走了。

考虑到`cohesion`，我认为`getEntryInfo`函数确实对组件如何呈现文本消息知道得太多了(通过使用`intl`)。

一个想法是考虑每个功能的单一责任。

因此，对于`getEntryInfo`函数，我们可以移除作为依赖项的`intl`参数，并构建消息对象，而不是返回一个字符串。

并在组件中这样使用:

作为组件重构，我们可以析构消息对象:

它可读性更强，不那么冗长。

对于消息对象，我们可以构建一个简单的函数来处理消息对象的创建:

看一下`values = {}`的论证。我们添加这个空对象作为默认值，在`biometry`和`default`情况下不需要传递任何东西。

没有了`intl`依赖，使用和测试函数就更容易了。它只依赖于数据，不再依赖于依赖关系。

# 最后一部分

包含所有分离逻辑的完整组件更具内聚性。每个部分都有自己的职责，这有助于减少耦合。

# 资源

*   [初学 JavaScript](https://BeginnerJavaScript.com/friend/LEANDRO)
*   [学习 ES6 — JavaScript](https://ES6.io/friend/LEANDRO)
*   [初学者的反应](https://BeginnerJavaScript.com/friend/LEANDRO)
*   [全栈高级反应](https://AdvancedReact.com/friend/LEANDRO)
*   [学习之路有什么反应](https://www.educative.io/courses/the-road-to-learn-react?aff=x8bV)
*   [学习反应前的 JavaScript 基础知识](https://www.educative.io/courses/javascript-fundamentals-before-learning-react?aff=x8bV)
*   [重新推出 React: V16 及更高版本](https://www.educative.io/courses/reintroducing-react-v16-beyond?aff=x8bV)
*   [带挂钩的高级反应模式](https://www.educative.io/courses/advanced-react-patterns-with-hooks?aff=x8bV)
*   [反应模式](https://www.educative.io/courses/react-in-patterns?aff=x8bV)
*   [高内聚低耦合](https://stackoverflow.com/a/3085419/3159162)

> 我希望你喜欢这个内容。[支持我在高保真方面的工作](https://ko-fi.com/teekay)

我的[Twitter](https://twitter.com/LeandroTk_)&[Github](https://github.com/LeandroTk)。☺