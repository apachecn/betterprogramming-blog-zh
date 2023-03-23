# 如何在 JavaScript 中创建自己的事件发射器

> 原文：<https://betterprogramming.pub/how-to-create-your-own-event-emitter-in-javascript-fbd5db2447c4>

## 基于 ES6 类构建一个简单的事件发射器

![](img/4a478ee9f62d96659a0ac0c8f6573bc4.png)

凯文·Ku 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 这是干什么用的？

首先，对于你的理解和见识。要全面了解某个东西是如何工作的，就自己写吧。

其次，编写更干净的代码。我们会给你一个真实的例子。

最后但同样重要的是:你可能会在面试中被要求写下自己的小而简单的`EventEmitter`——有所准备是件好事！

你好世界！

让我们从文档开始。Node.js [文档](https://nodejs.org/api/events.html)回答了你可能有的所有问题，但是像许多文档一样，它有点复杂。这就是为什么你可能需要一个快速指南。

# 创造

正如我提到的，我们将使用 ES6 类。

```
class MyEventEmitter {}
```

我们需要一个构造函数来为事件和回调初始化我们的`store`。

```
constructor() {
    this._events = {};
  }
```

让我们描述一下我们的第一个方法，`on`。它有两个参数:事件的名称和侦听器，侦听器将被触发。

从上面的代码中我们可以看到，如果对象`_events`没有属性，它会创建它并分配一个空数组。之后，一个新的侦听器将被添加到数组中。如果属性存在，我们只需添加一个侦听器。

下一个方法是`removeListener`。和以前一样，它需要两个参数:事件的名称和侦听器，这两个参数必须被删除。

这是怎么回事？很简单。如果我们没有这样的事件，我们会抛出一个新的错误和相关的消息。否则，我们过滤一组侦听器。

最后一种方法是`emit`。毫无例外，这个方法也接受两个参数:事件的名称和参数，它们必须传递给侦听器。

很简单。如果事件的名字是错误的，我们抛出一个错误。否则，我们使用`forEach`遍历所有回调函数，并用必要的数据调用每个回调函数。

这是我们事件发射器的最终版本:

# **我们如何使用它？**

创建实例:

```
const myEventEmitter = new MyEventEmitter();
```

创建处理程序:

```
const handleMyEvent = (data) => {
 console.log('Was fired: ', data);
};
```

将其添加到实例中:

```
myEventEmitter.on('testEvent', handleMyEvent);
```

最后，发出一个事件:

```
myEventEmitter.emit('testEvent', 'hi'); // Was fired: hi
```

我们可以根据需要多次发出事件，并传递不同的值。每次都会触发相关的监听器:

```
myEventEmitter.emit('testEvent', 'trigger'); // Was fired: triggermyEventEmitter.emit('testEvent', 'again'); // Was fired: again
```

但是，如果我们试图发出不存在的事件，我们将得到一个错误:

```
myEventEmitter.emit('fakeEvent', {}); // Error: Can't emit an event. Event "fakeEvent" doesn't exits.
```

此外，我们可以删除一个侦听器:

```
myEventEmitter.removeListener("testEvent", handleMyEvent);
```

下面的 emit 不会做任何事情。同时，它不会触发错误，因为属性`testEvent` 存在于`this._event` 对象上。

# 来自真实项目的示例

以下代码取自 Chrome 扩展。不管你是否熟悉它，我将简要地解释它是如何工作的。长话短说，扩展的不同部分通过发送消息相互通信。一开始，事情是这样的:

```
switch(action) {
 case 'open':
  openPopup(payload);
  break;
...
```

在代码的另一部分，我们创建了这条消息:

```
const msg = {
 action: 'open',
 payload,
};chrome.runtime.sendMessage(msg);
```

可以想象，随着扩展的增长，这个“开关盒”变得越来越大。我们决定创建一个`connector` ，它完全作为一个事件发射器工作:

```
connector.addListener("open", openPopup);
...
```

要触发它:

```
const msg = {
 action: 'open',
 payload,
};connector.sendToBackground(msg)
```

# 摘要

这是我的第一篇英文文章！希望你觉得有用。

最后，我相信`EventEmitter`的知识会激励你写出更好的代码。对我来说是的！