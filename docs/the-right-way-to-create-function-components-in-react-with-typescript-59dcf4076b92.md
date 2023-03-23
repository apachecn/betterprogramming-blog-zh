# 用类型脚本创建函数组件的正确方法

> 原文：<https://betterprogramming.pub/the-right-way-to-create-function-components-in-react-with-typescript-59dcf4076b92>

## 更好的组件，类型

![](img/4c4ae6c48f719c2c422ccd6aa5072b13.png)

费伦茨·阿尔马西在 [Unsplash](https://unsplash.com/photos/eBRTYyjwpRY) 拍摄的照片

最近，我有一个拉动请求，引发了我和队友们的简短讨论。有人注意到我使用了不同的方法为我编写的函数组件编写类型，而代码库中使用了其他方法。

在这篇小小的 React 文章中，我将向您展示正确的方法。

# 从面向类型的角度看功能组件

一般来说，函数是编程工具，接受一些输入，对其进行处理，然后返回一些输出。功能组件基本上以相同的方式工作。它们获取属性并将其转换成 UI 元素。你可以在下面看到一个来自 reactjs.org[的使用普通 JavaScript 的超级基本函数组件的例子。](https://reactjs.org/docs/components-and-props.html)

通过用 TypeScript 重写这个组件，我们的目标是确保我们

*   使用具有正确类型的正确属性
*   获取从函数返回的正确类型的值

## 键入功能组件的常见(错误)方式

我经常看到开发人员使用的一种方法是只为组件的 props 定义类型，而忽略返回值。看起来是这样的:

这很好，考虑到 TypeScript 足够智能，可以隐式识别返回类型。但是，如果您的函数组件基于某些条件返回不同的值，它可能会让您失败—更不用说它会导致各种函数组件之间的摩擦。

# 正确的方式

定义函数组件类型的正确方法是使用 React 自己的类型定义`React.FunctionComponent`或`React.FC`。在这种情况下，我们将上面的代码重构为下面的代码:

这个版本使用 React 自己的类型定义，包括返回类型的定义和`props.children`属性。

# 结论

代码库中的一致性非常重要。它保持代码更改的整洁，并使入职更容易。尽量保持代码约定的一致性，尽可能使用标准方法。

— [奥赞通卡](https://medium.com/@ozantunca)

## 订阅更多

如果你喜欢这篇文章，并想知道类似的其他文章，[你可以在这里订阅我](https://ozantunca.org/subscribe)。我还会不时地给你发送免费的课程和电子书。我保证永远不会给你发垃圾邮件👍。