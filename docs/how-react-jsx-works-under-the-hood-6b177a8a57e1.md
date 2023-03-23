# JSX 在幕后是如何工作的

> 原文：<https://betterprogramming.pub/how-react-jsx-works-under-the-hood-6b177a8a57e1>

## 让我们解决这些怪癖和警告

![](img/dba0d039b4de9ab9e67b55cdc8c687e4.png)

图片由 [@thevanegmond](https://unsplash.com/@thevanegmond) 于 [Unsplash](https://unsplash.com/photos/Fy1USiu0SbA) 拍摄，作者编辑

React 已经存在很长时间了。它还带来了 JSX，这是 React 开发的核心部分。乍一看，JSX 和 HTML 非常相似。然而， [JSX 实际上更接近于 JavaScript 而不是 HTML](https://reactjs.org/docs/introducing-jsx.html#specifying-attributes-with-jsx) 。在这里，我们将看看那些熟悉的类似 HTML 的标签。

下面是一些非常标准的 React 代码:

我们导入`React`，然后创建我们的组件并返回一些 JSX 标记。

## 为什么 React 需要导入

当我开始学习 React 的时候，我发现一件奇怪的事情，那就是我们有`import React`但是在任何地方都不使用它。事实上，我们确实在使用它。

浏览器无法直接理解 React 代码。每当我们编写 React 代码时，我们总是将其构建/转换为浏览器可以理解的 JavaScript。

这通常是用像 [Babel](https://babeljs.io/) 或 [TypeScript](https://www.typescriptlang.org/) 这样的工具来完成的。如果你使用像 [Create React App](https://github.com/facebook/create-react-app) 这样的工具和库，它们会在幕后为你做编译。

当我们构建/传输 React 代码时，我们的组件`HomePage`将看起来像这样:

*注意:我做了一些改动，使其可读性更好，但概念仍然有效。* [*跟随此链接*](https://babeljs.io/repl#?browsers=&build=&builtIns=false&corejs=3.6&spec=false&loose=false&code_lz=GYVwdgxgLglg9mABACTgWwKYAUCGBzDRACgEpEBvAKEUQCcMoRakibFq2AeXAxWKADYYAvAHJUmUQD4ObRJwAOiCAJwBnNQDkcmMQvwYAtPtr5TCgBbTZcxAE04IRDnp8LhcgBYAVAEYADIEAvlAWiABuMGowUHC0NjScAPQKMrbyFrRJaVxJPBg5JADclEFAA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-2&prettier=false&targets=&version=7.4.4&externalPlugins=) *来一窥原著，更丑的版本。*

JSX 语法实际上被转换成普通的旧 JavaScript。特别是，所有等同于 HTML 标签的东西都被转换成对`React.createElement`的调用。如果我们愿意处理这种语法，我们实际上可以编写 React 代码，而不用编写任何 JSX！

## 如果我不想导入 React 怎么办？

有了新版本的 Babel 或 TypeScript，我们实际上可以省去 React 导入。从 React 17 开始，增加了对一个新函数`_jsx`的支持。transpiler 可以使用该功能代替`React.createElement`。为了在巴别塔上运行，我们需要一个更新版本的 [JSX 变换插件](https://babeljs.io/docs/en/babel-plugin-transform-react-jsx)。这对于新版本的 TypeScript 来说是现成的。

如果您不直接使用 Babel/TypeScript，而是使用 Create React App 或基于 React 的库，这些库的新版本也对此提供了支持。

如果您使用 React 以下版本的最新版本，这些版本也支持。请注意，这仍然需要更新版本的 Babel 或 TypeScript。

关于哪些版本支持这个以及如何在 Babel 和 TypeScript 中设置它的更多信息，请看[这个链接](https://reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html)。

如果你想更多地了解这一变化带来的好处，那么 Dilantha Prasanjith 的文章是一个很好的阅读材料。

[](https://blog.bitsrc.io/new-jsx-enhancements-in-react-17-e5f64acbea89) [## React 17 中新的 JSX 增强功能

### React 17 对 JSX 有什么新功能，为什么您应该关注它

blog.bitsrc.io](https://blog.bitsrc.io/new-jsx-enhancements-in-react-17-e5f64acbea89) 

## JavaScript 表达式是如何计算的

如果我们查看 transpiled 代码，我们会注意到花括号中写的纯文本子元素(在本例中为`You are the {4*1000}th visitor`)被拆分，每个部分都作为一个单独的子元素被传递。这允许 React 评估用 JSX 编写的 JavaScript 表达式。

## HTML 标签和 React 组件的区别

在上面经过编译的代码中，如果我们更深入地研究对`createElement`的实际调用，我们可以看到`paragraph, horizontalRule`和`ourComponent`的调用有些不同。

当呈现一个 React 组件时(在我们的例子中是`Page`):

*   第一个参数是对要呈现的组件的引用。
*   第二个参数指定必须传递给组件的属性。
*   所有其他参数都是要传递给组件的子参数。

当呈现一个 HTML 标签时(在我们的例子中是`p`和`hr`):

*   第一个参数是标记的名称。
*   第二个参数是用于创建传递给标记的属性的属性。
*   所有其他参数都是要传递给组件的子参数。

## 为什么 React 组件必须以大写字母开头

那么，transpiler 如何区分哪些 JSX 标签呈现为 HTML，哪些呈现为 React 组件呢？它只是查看标签的大小写。如果标签以大写字母开头，它将被视为 React 组件，否则它将被视为 HTML 标签。

所以，如果我们要做这样的事情:

在这种情况下，transpiler 将把`componentToRender`视为普通的 HTML 标签，而不是 React 组件。[这里有一个到传输代码](https://babeljs.io/repl#?browsers=&build=&builtIns=false&corejs=3.6&spec=false&loose=false&code_lz=GYVwdgxgLglg9mABACTgWwKYAUCGBzDRACgG9EQBnDAeQBsATAEQwpjyQF8BKREgKESIICClCHoADggxgoAFTgAlGfQwAnRAF5yVOkxZskAfkR7cBRAC5E5jAG4BiNRigg1SIoMSPBAHmFoUmAy8koq6oiwULQYmgDkqJhxAHw-XsgYtLRwab4A9AFBIQrKYKpqqYJcDhxAA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-2&prettier=false&targets=&version=7.4.4&externalPlugins=)的链接。

为了使用这种模式，我们可以简单地将`componentToRender`重命名为`ComponentToRender`。神奇之处在于标识符的第一个字母。

这样做的好处是 React 不必维护有效 HTML 标签的列表。即使他们维护了这样一个列表，它也不能很好地与允许我们创建自定义元素的 [Web 组件](https://developer.mozilla.org/en-US/docs/Web/Web_Components)一起使用。因此，最好不要限制哪些标签应该被视为 HTML 标签。

## 为什么`className`又不上课？为什么是`forHtml`而不是`for`？

如果我们看一看带有`className`属性的典型组件的 transpiled 代码，它看起来会像这样:

我们了解到，对于 HTML 标记，第二个参数是将传递给 HTML 标记的属性。但是，`className`不是 HTML 属性。这里我们不使用`class`,因为它是 JavaScript 中的保留关键字。

尽管在对象中将其作为键传递不成问题，但 React 团队还是选择了`className`,因为`class`是 JavaScript 中的保留关键字。用`forHtml`代替`for`也是如此。

在 React 16 之前，这些属性被忽略。从 React 16 开始，这些属性不会被忽略，但会给出警告。至于为什么——答案并不简单。详细回答，[关注此链接](https://github.com/facebook/react/issues/13525#issuecomment-417818906)。

## 呈现动态 HTML

如果我们要传输上面的代码，我们会得到类似这样的结果:

这里重要的是，上面的转换代码不同于下面的转换代码:

不同之处在于，`htmlFromSomewhere`中的`p`标签实际上被视为一个字符串，而不是 HTML 标记，因为它是以字符串值的形式传递的。对于被视为 HTML 标记的`p`标签，需要使用`React.createElement`来创建。

*如果你熟悉 DOM API，其区别类似于设置*[*innerText*](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/innerText)*vs 设置*[*innerHTML*](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML)*。*

React 将该字符串视为 HTML 并不困难，因为 HTML 已经处理了文本。然而，将未知来源的字符串视为 HTML 标记会带来一些安全漏洞。这是因为我们不知道这个字符串来自哪里——有人可能会在那里注入一个`script`标签并利用系统。这被称为 XSS 攻击。

务实的程序员的 [Secure Your Node.js Web 应用](https://medium.com/pragmatic-programmers/table-of-contents-d56d9bab2639)有一整节是关于 XSS 攻击和应对它们的方法的。别担心，你不需要买任何东西。这在介质上是可得到的。

这是书中 XSS 部分的链接。

 [## 认识不同类型的 XSS

medium.com](https://medium.com/pragmatic-programmers/recognize-different-types-of-xss-be8d66a91caf) 

如果你完全确定这个字符串是安全的，那么你可以如下使用`dangerouslySetInnerHTML`属性。

请注意，该值必须是一个对象，并且将`__html`属性设置为要呈现的 HTML。[根据文档](https://reactjs.org/docs/dom-elements.html#dangerouslysetinnerhtml)的说法，这种语法被故意弄得很奇怪，以便提醒你这是危险的。

# 最后的想法

JSX 的引擎盖下发生了很多事情，也有一些怪癖和警告。理解这些怪癖能让我们更好地理解事情为什么会这样。