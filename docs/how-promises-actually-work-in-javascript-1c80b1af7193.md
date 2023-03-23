# JavaScript 中承诺的实际工作方式

> 原文：<https://betterprogramming.pub/how-promises-actually-work-in-javascript-1c80b1af7193>

## 了解何时以及如何使用它们

![](img/52a642ba950c20f20a8bc317488e3ad4.png)

由 [Ferenc Almasi](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

在当前的 JavaScript 时代，`Promises`是 JavaScript 中处理异步行为的默认方式。但是它们是如何工作的呢？为什么你要非常了解他们？

在本文中，我将深入 JavaScript `Promises`来理解它们的作用以及何时应该使用它们。

# 现实生活中的承诺

当我向你承诺时，你要相信我的话，我会实现那个承诺。

但是我不告诉你那个承诺什么时候兑现，所以生活还要继续…

有两种可能的情况:满足或拒绝。

## 完成

有一天，我实现了那个承诺。它让你如此开心以至于你在推特上发布了它！

## 拒绝

有一天，我告诉你，我不能履行承诺。

你在推特上发了一个悲伤的帖子，说我没有兑现我的承诺。

这两种情况都会导致一个动作。第一个是积极的，第二个是消极的。

在研究 JavaScript `Promises`如何工作的时候，请记住这个场景。

# 何时使用承诺

JavaScript 是同步的。它从上到下运行。下面的每一行代码都将等待上面代码的执行。

但是当你想从一个 API 获取数据的时候，你不知道多快能把数据拿回来。更确切地说，你还不知道你会得到数据还是错误。错误无时无刻不在发生，那些事情是无法计划的。但是我们可以做好准备。

因此，当您等待从 API 获得结果时，您的代码会阻塞浏览器。它会冻结浏览器。我们和我们的用户对此都不高兴！

对一只`Promise`来说是完美的情况！

# 如何使用承诺

既然我们知道在发出 Ajax 请求时应该使用`Promise`，我们就可以开始使用`Promises`了。首先，我将向您展示如何定义一个返回`Promise`的函数。然后，我们将深入研究如何使用返回`Promise`的函数。

## 用承诺定义一个函数

下面是一个返回`Promise`的函数示例:

该函数返回一个`Promise`。这个`Promise`可以解决，也可以拒绝。

就像现实生活中的承诺一样，一个`Promise`可以实现，也可以拒绝。

## 州

根据 [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)，一个 JavaScript `Promise`可以有三种状态之一:

> "-待定:初始状态，既未完成也未拒绝。
> 
> -已完成:表示操作成功完成。
> 
> - rejected:表示操作失败。"

## 悬而未决的

`pending`状态是初始状态。这意味着我们一调用`doSomething()`函数就有这种状态，所以我们还不知道`Promise`是被拒绝还是被解决。

## 断然的

在这个例子中，如果`value`为真，那么`Promise`将被解析。在这种情况下，我们将变量`value`传递给它，以便在调用这个函数时使用它。

我们可以定义我们的条件来决定何时解决我们的`Promise`。

## 拒绝

在本例中，如果`value`为假，则`Promise`将被拒绝。在这种情况下，我们传递一个错误消息。这里只是一个字符串，但是当你发出一个 Ajax 请求时，你传递的是服务器的错误。

# 使用有承诺的功能

现在我们知道了如何定义一个`Promise`，我们可以深入研究如何使用一个返回`Promise`的函数:

您可以通过`.then()`方法或`await`关键字识别返回`Promise`的函数。如果您的`Promise`出现错误，将会调用`catch`。所以为`Promise`进行错误处理是非常简单的。

`Promises`也在很多 JavaScript 库和框架中使用。但是最简单的 web API 是 Fetch API，您应该使用它来发出 Ajax 请求。如果你想知道它是如何工作的，请查看下面的教程:

[](/from-xhr-to-fetch-with-async-await-on-the-finish-line-b021de1d226b) [## 从 XHR 到终点的异步/等待取货

### 看看 JavaScript 中请求的发展和历史

better 编程. pub](/from-xhr-to-fetch-with-async-await-on-the-finish-line-b021de1d226b) 

# 结论

希望这能帮助你理解`Promises`是如何工作的，以及你能用它们做什么。

如果你对`Promises`有任何疑问，请告诉我。

编码快乐！

# 从我这里读更多

[](/7-web-development-guidelines-to-speed-up-page-loading-e8f0e13a53b) [## 加速页面加载的 7 个 Web 开发指南

### 为了你的访问者的爱，改善用户体验

better 编程. pub](/7-web-development-guidelines-to-speed-up-page-loading-e8f0e13a53b) [](https://devbyrayray.medium.com/css-variable-with-styled-components-7e91d89f13f3) [## 带有样式化组件的 CSS 变量

### 在 Next.js/React.js 轻松使用它们

devbyrayray.medium.com](https://devbyrayray.medium.com/css-variable-with-styled-components-7e91d89f13f3) [](https://devbyrayray.medium.com/how-to-use-css-media-queries-with-styled-components-in-react-js-f5db5ffcc5f0) [## 如何在 React.js 中对样式化组件使用 CSS 媒体查询

### 在样式化组件中更智能地使用媒体查询

devbyrayray.medium.com](https://devbyrayray.medium.com/how-to-use-css-media-queries-with-styled-components-in-react-js-f5db5ffcc5f0) [](https://devbyrayray.medium.com/developer-dilemmas-new-technology-vs-old-technology-3de8726ef6c1) [## 开发者的困境:新技术 VS 旧技术

### 中短篇小说

devbyrayray.medium.com](https://devbyrayray.medium.com/developer-dilemmas-new-technology-vs-old-technology-3de8726ef6c1) [](https://javascript.plainenglish.io/how-to-build-a-custom-checkbox-with-javascript-82083a66ae04) [## 如何用 JavaScript 构建自定义复选框

### 以最简单的方式学习 JavaScript

javascript.plainenglish.io](https://javascript.plainenglish.io/how-to-build-a-custom-checkbox-with-javascript-82083a66ae04)